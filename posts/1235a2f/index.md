# Velox_日志模块


## 概述

日志模块通过封装 [spdlog](https://github.com/gabime/spdlog) 库来实现，模块以 `velox::log` 命名空间组织，且提供了宏定义方便外部调用。该模块支持**异步日志输出**、**多目标日志（控制台&#43;文件）**、**配置文件定义**（通过集成`config`模块实现）。

这意味着整个日志系统的行为——包括有哪些日志记录器（Logger）、每个记录器的日志级别、输出格式以及输出目标（控制台、文件等）——都可以在 YAML 配置文件中定义。更进一步，当配置文件被重新加载时，日志系统能够**动态地、无需重启应用**就完成新增、删除或修改日志记录器的操作，实现了日志系统的热重载。

此外，该模块默认会创建一个名为 `default` 的全局异步日志器，可同时输出到控制台和日志文件，方便快速使用。

## 主要特性

- **配置驱动**：整个日志系统的结构和行为由 `velox::config` 模块中的 `logs` 配置项驱动，实现了代码与配置的分离
- **动态热重载**：通过监听配置项 `logs` 的变更事件，可以动态地创建、更新或删除日志记录器，极大提升了灵活性
- **全异步日志**：默认创建的所有日志记录器都是异步的，使用全局线程池处理 I/O 操作，最大限度地降低了对业务线程性能的影响
- **简洁的宏接口**：提供了一系列 `VELOX_...` 宏，如 `VELOX_INFO`, `VELOX_LOGGER_WARN`，简化了日志调用，并与 `spdlog` 的使用方式保持一致
- **多目标输出**：每个日志记录器可以配置多个输出目标（称为 `Appender`），例如可以同时向控制台和每日轮转的日志文件输出
- **精细化控制**：可以为整个日志记录器及其下的每个 `Appender` 单独设置不同的日志级别和输出格式

## 重要函数介绍

### 初始化函数

```cpp
  /**
   * @brief 对 spdlog 进行初始化, 并创建和设置配置参数 logs
   * @param[in] queue_size 用于异步 logger 的队列大小
   * @param[in] n_threads 用于异步 logger 的线程数
   * @return 成功返回 true
   */
  bool initSpdlog(std::size_t queue_size, std::size_t n_threads)
  {
    try
    {
      /*------------- 配置默认的日志器 -------------*/
      spdlog::init_thread_pool(queue_size, n_threads);

      // 标准控制台输出
      auto stdout_sink = std::make_shared&lt;spdlog::sinks::stdout_color_sink_mt&gt;();
      stdout_sink-&gt;set_level(spdlog::level::debug);

      // 日志文件输出, 0点0分创建新日志
      auto log_path = getLogPath(&#34;default&#34;);
      auto file_sink = std::make_shared&lt;spdlog::sinks::daily_file_sink_mt&gt;(log_path.string(), 0, 0);
      file_sink-&gt;set_level(spdlog::level::info);

      std::vector&lt;spdlog::sink_ptr&gt; sinks{ stdout_sink, file_sink };

      // 创建一个异步日志器
      auto logger = std::make_shared&lt;spdlog::async_logger&gt;(
          &#34;default&#34;, sinks.begin(), sinks.end(), spdlog::thread_pool(), spdlog::async_overflow_policy::block);

      // 设置输出阈值
      logger-&gt;set_level(spdlog::level::trace);

      // 设置输出格式
      logger-&gt;set_pattern(&#34;%^[%Y-%m-%d %T.%e][thread %t][%l][%n][%s:%#]: %v%$&#34;);

      // 设置刷新阈值, 当出发 warn 或更严重的错误时立刻刷新日志到  disk
      logger-&gt;flush_on(spdlog::level::warn);

      // 每3秒自动刷新依次缓冲区
      spdlog::flush_every(std::chrono::seconds(3));

      // 将其注册为默认 logger
      spdlog::set_default_logger(logger);

      // 设置 spdlog 内部错误处理回调
      spdlog::set_error_handler([](const std::string&amp; msg)
                                { spdlog::log(spdlog::level::critical, &#34;=== SPDLOG LOGGER ERROR ===: {}&#34;, msg); });

      /*------------- 设定日志的配置参数 -------------*/
      // 获取日志配置参数 logs 的指针, 该函数内部使用静态成员的方式存储该指针
      auto log_defines = getLogDefinesConfigVar();

      // 添加回调函数, 在值更新时自动更新对应参数
      // NOTE: 新旧值的交换 setValue() 函数已经完成了, 回调函数无需更新配置参数值
      log_defines-&gt;addListener(
          [](const std::set&lt;LogDefine&gt;&amp; old_value, const std::set&lt;LogDefine&gt;&amp; new_value)
          {
            spdlog::info(&#34;on_logger_conf_changed&#34;);

            // 将旧值和新值转为 map, 方便 diff
            std::unordered_map&lt;std::string, LogDefine&gt; old_map;
            std::unordered_map&lt;std::string, LogDefine&gt; new_map;

            for (const auto&amp; item : old_value)
            {
              old_map[item.name] = item;
            }

            for (const auto&amp; item : new_value)
            {
              new_map[item.name] = item;
            }

            // 处理被删除的日志器
            for (const auto&amp; [name, old_def] : old_map)
            {
              if (new_map.count(name) == 0)
              {
                spdlog::drop(name); // 删除指定日志器
                spdlog::info(&#34;Logger [{}] dropped due to config removal&#34;, name);
              }
            }

            // 处理新增或更新的日志器
            for (const auto&amp; [name, new_def] : new_map)
            {
              auto old_it = old_map.find(name);

              if (old_it == old_map.end())
              {
                // 新增
                // 该函数会根据参数 LogDefine 创建对应的日志器并进行注册,
                // 以便能够通过 spdlog::get 获取
                createLoggerFromDefine(new_def);
                spdlog::info(&#34;Logger [{}] created from new config&#34;, name);
              }
              else
              {
                // 更新
                spdlog::drop(name);  // 卸载旧 logger
                createLoggerFromDefine(new_def);
                spdlog::info(&#34;Logger [{}] reloaded due to config change&#34;, name);
              }
            }
          });
    }
    catch (const spdlog::spdlog_ex&amp; ex)
    {
      std::cerr &lt;&lt; &#34;spdlog initialization failed: &#34; &lt;&lt; ex.what() &lt;&lt; &#39;\n&#39;;
      return false;
    }
    catch (...)
    {
      std::cerr &lt;&lt; &#34;spdlog initialization failed: unknown error&#34; &lt;&lt; &#39;\n&#39;;
      return false;
    }

    return true;
  }
```

### 偏特化

因为 `LogDefine` 是我们自定义的类型，为了序列化和反序列化（即`toString`和`fromString`）函数能够正常工作，我们需要实现对应的偏特化：

```cpp
// 相关结构体定义
namespace velox::log
{
  // 日志输出器类型(强类型枚举)
  enum class AppenderType
  {
    File = 1,
    Stdout,
  };

  // 日志输出器参数
  struct LogAppenderDefine
  {
    AppenderType type = AppenderType::File;
    std::string level;
    std::string formatter;
    std::string file;

    bool operator==(const LogAppenderDefine&amp; oth) const
    {
      return type == oth.type &amp;&amp; level == oth.level &amp;&amp; formatter == oth.formatter &amp;&amp; file == oth.file;
    }
  };

  // 日志器参数
  struct LogDefine
  {
    std::string name;
    std::string level = &#34;DEBUG&#34;;
    std::string formatter = &#34;%^[%Y-%m-%d %T.%e][thread %t][%l][%n][%s:%#]: %v%$&#34;;
    std::vector&lt;LogAppenderDefine&gt; appenders;

    bool operator==(const LogDefine&amp; oth) const
    {
      return name == oth.name &amp;&amp; level == oth.level &amp;&amp; formatter == oth.formatter &amp;&amp; appenders == oth.appenders;
    }

    bool operator&lt;(const LogDefine&amp; oth) const { return name &lt; oth.name; }
  };
}  // namespace velox::log

// 实现 LogDefine 的偏特化
namespace velox::config
{
  // string -&gt; LogDefine
  template&lt;&gt;
  class TypeConverter&lt;std::string, velox::log::LogDefine&gt;
  {
   public:
    velox::log::LogDefine operator()(const std::string&amp; v)
    {
      YAML::Node node = YAML::Load(v);
      velox::log::LogDefine log_def;

      // 判断该 key 是否定义
      if (node[&#34;name&#34;].IsDefined())
      {
        log_def.name = node[&#34;name&#34;].as&lt;std::string&gt;();
      }
      else
      {
        std::cout &lt;&lt; &#34;log config error: name is null\n&#34;;
        throw std::logic_error(&#34;log config name is null&#34;);
      }

     // 该key有默认值, 所以未定义是允许的
      if (node[&#34;level&#34;].IsDefined())
      {
        log_def.level = node[&#34;level&#34;].as&lt;std::string&gt;();
      }

      if (node[&#34;formatter&#34;].IsDefined())
      {
        log_def.formatter = node[&#34;formatter&#34;].as&lt;std::string&gt;();
      }

      if (node[&#34;appenders&#34;].IsDefined())
      {
        // 依次处理每个日志输出器
        for (size_t i = 0; i &lt; node[&#34;appenders&#34;].size(); &#43;&#43;i)
        {
          auto appender = node[&#34;appenders&#34;][i];

          if (!appender[&#34;type&#34;].IsDefined())
          {
            std::cout &lt;&lt; &#34;log config error: appender type is null, &#34; &lt;&lt; YAML::Dump(appender) &lt;&lt; std::endl;
            continue;
          }

          auto type = appender[&#34;type&#34;].as&lt;std::string&gt;();
          velox::log::LogAppenderDefine log_app_def;
          if (type == &#34;FileLogAppender&#34;)
          {
            log_app_def.type = velox::log::AppenderType::File;

            if (!appender[&#34;file&#34;].IsDefined())
            {
              std::cout &lt;&lt; &#34;log config error: fileappender file is null, &#34; &lt;&lt; YAML::Dump(appender) &lt;&lt; std::endl;
              continue;
            }

            log_app_def.file = appender[&#34;file&#34;].as&lt;std::string&gt;();
          }
          else if (type == &#34;StdoutLogAppender&#34;)
          {
            log_app_def.type = velox::log::AppenderType::Stdout;
          }
          else
          {
            std::cout &lt;&lt; &#34;log config error: appender type is invalid, &#34; &lt;&lt; YAML::Dump(appender) &lt;&lt; std::endl;
            continue;
          }

          if (appender[&#34;level&#34;].IsDefined())
          {
            log_app_def.level = appender[&#34;level&#34;].as&lt;std::string&gt;();
          }

          if (appender[&#34;formatter&#34;].IsDefined())
          {
            log_app_def.formatter = appender[&#34;formatter&#34;].as&lt;std::string&gt;();
          }

          log_def.appenders.push_back(log_app_def);
        }
      }

      return log_def;
    }
  };

  // LogDefine -&gt; string
  template&lt;&gt;
  class TypeConverter&lt;velox::log::LogDefine, std::string&gt;
  {
   public:
    std::string operator()(const velox::log::LogDefine&amp; ld)
    {
      YAML::Node node;
      node[&#34;name&#34;] = ld.name;
      node[&#34;level&#34;] = ld.level;
      node[&#34;formatter&#34;] = ld.formatter;

      for (const auto&amp; app : ld.appenders)
      {
        YAML::Node app_node;

        if (app.type == velox::log::AppenderType::File)
        {
          app_node[&#34;type&#34;] = &#34;FileLogAppender&#34;;
          app_node[&#34;file&#34;] = app.file;
        }
        else
        {
          app_node[&#34;type&#34;] = &#34;StdoutLogAppender&#34;;
        }

        app_node[&#34;level&#34;] = app.level;
        app_node[&#34;formatter&#34;] = app.formatter;

        node[&#34;appenders&#34;].push_back(app_node);
      }

      std::stringstream ss;
      ss &lt;&lt; node;
      return ss.str();
    }
  };

}  // namespace velox::config
```

## 使用示例

### 使用默认日志器

```cpp
int main()
{
  VELOX_LOG_INIT(); // 初始化, 也可以带参数 VELOX_LOG_INIT(queue_size, n_threads)

  // 使用默认日志器, 会同时向控制台和文件进行输出
  VELOX_WARN(&#34;Test warn message from default logger&#34;);
  VELOX_ERROR(&#34;Test {} message from default logger&#34;, &#34;error&#34;);
  VELOX_CRITICAL(&#34;Test {} message from default logger&#34;, &#34;critical&#34;);

  VELOX_LOG_SHUTDOWN(); // 优雅的关闭日志系统
  return 0;
}
```

### 使用配置文件生成的日志器

假设 `test/log/log.yml`内容为：

```yaml
logs:
  # 设置输出格式, 详细可参考 https://github.com/gabime/spdlog/wiki/Custom-formatting#pattern-flags
  # 默认格式为 [2025-07-03 11:17:53.345][thread Id][fiber id][debug][Logger][main.cpp:37]: ......
  # file为相对于项目根路径的路径
  - name: test1
    level: info
    appenders:
    - type: FileLogAppender
      file: logs/test1/test1.log
  - name: test2
    level: info
    appenders:
    - type: FileLogAppender
      file: logs/test2/test2.log
```

使用代码：

```cpp
int main()
{
  VELOX_LOG_INIT();

  // 加载指定目录下的配置文件
  velox::config::Config::loadFromConfDir(&#34;test/log&#34;);
  
  // 获取指定名称的日志器
  auto logger = VELOX_GETLOG(&#34;test1&#34;);
  
  // 使用指定日志器进行输出, 只会往文件进行输出, 且日志等级为 info，
  // 所以TRACE和DEBUG日志不会输出
  VELOX_LOGGER_TRACE(logger, &#34;Test trace message from test1 logger&#34;);
  VELOX_LOGGER_DEBUG(logger, &#34;Test debug message from test1 logger&#34;);
  VELOX_LOGGER_INFO(logger, &#34;Test info message from test1 logger&#34;);
  VELOX_LOGGER_WARN(logger, &#34;Test warn message from {} logger&#34;, &#34;test1&#34;);
  VELOX_LOGGER_ERROR(logger, &#34;Test error message from test1 logger&#34;);
  VELOX_LOGGER_CRITICAL(logger, &#34;Test critical message from test1 logger&#34;);

  VELOX_LOG_SHUTDOWN();
  return 0;
}
```

更多示例可以查看`test/log/log_test.cpp`中的内容。


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/1235a2f/  


# Velox_控制模块


## 概述

配置模块的作用为提供一个类型安全、集中式管理应用程序配置项的方式，允许开发者定义任意类型的配置变量，为它们设置默认值，并通过`YAML`文件方便地加载和修改这些配置。

该模块的核心设计思想是将每一个配置项抽象为一个`ConfigVar`对象。所有这些对象都由一个单例管理的`Config`类进行统一存储和访问。这种设计避免了配置项散落在代码各处，使得配置的管理、维护和查阅变得非常方便。

该模块主要由以下几部分构成：

1. **`ConfigVarBase`**: 一个抽象基类，定义了所有配置变量的通用接口，如获取名称、描述、类型名以及与字符串的双向转换（序列化/反序列化）。
2. **`ConfigVar&lt;T&gt;`**: 继承自`ConfigVarBase`的模板类。它是配置变量的具体实现，能够存储任意类型 `T` 的值。它还实现了一个回调机制，当配置值发生变化时，可以通知所有监听者。
3. **`TypeConverter`**: 一系列模板类及其偏特化，用于在各种C&#43;&#43;类型（包括基本类型、STL容器如`vector`, `list`, `map`, `set`等）和字符串之间进行转换。该模块默认使用 `boost::lexical_cast` 进行基础类型转换，并为复杂容器类型提供了基于 `yaml-cpp` 的序列化和反序列化实现。对于**自定义类型**，只需完成对应的偏特化即可进行转换。
4. **`Config`**: 一个管理类，它包含一个静态的`unordered_map`用于存储所有的配置变量。它提供了全局的静态方法来创建、获取和加载配置项，是与本模块交互的主要入口。

本模块通过[yaml-cpp](https://github.com/jbeder/yaml-cpp)库解析[YAML](https://www.runoob.com/w3cnote/yaml-intro.html)文件，再将从`.yml`中解析出来的结构化数据更新到对应的`ConfigVar`变量中。

## 主要特性

- **类型安全**: 每个配置项在定义时都强绑定一个类型，在获取时会进行动态类型检查，防止了类型不匹配导致的运行时错误。
- **集中式管理**: 所有配置项都通过一个全局的`Config`管理器进行注册和访问，便于追踪和管理。
- **支持复杂数据结构**: 内置了对 `std::vector`, `std::list`, `std::set`, `std::unordered_set`, `std::map`, `std::unordered_map` 等常用STL容器的序列化和反序列化支持。
- **基于YAML**: 使用广泛流行的YAML格式作为配置文件，语法清晰，支持复杂数据结构。
- **动态更新与回调**: 支持在运行时通过加载YAML文件来更新配置值。当某个配置值发生变化时，可以触发预先注册的回调函数，使应用程序能够动态地响应配置变化。

## 重要函数介绍

### Config类

1. `getDatas()`

    ```cpp
    using ConfigVarMap = std::unordered_map&lt;std::string, ConfigVarBase::ptr&gt;;
    /**
     * @brief 返回所有的配置项
     */
    static ConfigVarMap&amp; getDatas()
    {
      static ConfigVarMap s_datas;
      return s_datas;
    }
    ```

    这里的数据存储的是基类指针`ConfigVarBase::ptr`，这是典型的**多态**设计。因为`ConfigVar&lt;T&gt;` 是模板类，如果用的是`std::unordered_map&lt;std::string, ConfigVar&lt;T&gt;::ptr&gt;;`则只能放一种类型的配置变量（比如 `int`、`std::string`）。

1. `getOrCreateConfigVarPtr(const std::string&amp; name, const T&amp; default_value, const std::string&amp; description = &#34;&#34;)`

    ```cpp
    /**
     * @brief 获取/创建对应参数名的配置参数
     * @param[in] name 配置参数名称
     * @param[in] default_value 参数默认值
     * @param[in] description 参数描述
     * @details 获取参数名为name的配置参数,如果存在则直接返回
     *          如果不存在, 则根据参数创建对应的配置参数
     * @return 返回对应的配置参数指针, 如果参数名存在但是类型不匹配则返回 nullptr
     * @exception 如果参数名为空或包含非法字符[^0-9a-z_.] 抛出异常 std::invalid_argument
     */
    template&lt;class T&gt;
    static typename ConfigVar&lt;T&gt;::ptr
    getOrCreateConfigVarPtr(const std::string&amp; name, const T&amp; default_value, const std::string&amp; description = &#34;&#34;)
    {
      // getDatas() 返回的是存储全部配置项的 unordered_map
      auto it = getDatas().find(name);
      if (it != getDatas().end())
      {
        auto tmp = std::dynamic_pointer_cast&lt;ConfigVar&lt;T&gt;&gt;(it-&gt;second);
        if (tmp)
        {
          VELOX_INFO(&#34;ConfigVar [name:{}, valueType:{}] exists&#34;, name, tmp-&gt;getTypeName());
          return tmp;
        }

        // 存在但类型不匹配
        VELOX_ERROR(
            &#34;ConfigVar name {} exists but type not {}, real type is {}:{}&#34;,
            name,
            velox::util::typeName&lt;T&gt;(),
            it-&gt;second-&gt;getTypeName(),
            it-&gt;second-&gt;toString());
        return nullptr;
      }

      // 创建配置参数
      if (!velox::util::isValidName(name))
      {
        VELOX_ERROR(&#34;ConfigVar name invalid: {}&#34;, name);
        throw std::invalid_argument(name);
      }

      auto var = std::make_shared&lt;ConfigVar&lt;T&gt;&gt;(name, default_value, description);
      getDatas()[name] = var;
      return var;
    }
    ```

1. `loadFromYaml(const YAML::Node&amp; root)`

    ```cpp
    /**
    * @brief 使用 YAML::Node 更新对应配置参数
    * @details 只会更新已经存在的配置参数, 未存在的配置参数不会创建, 只会在日志中
    *          记录
    * @param[in] root 表示当前要解析的 YAML::Node 结点
    */
    void Config::loadFromYaml(const YAML::Node&amp; root)
    {
        // all_nodes 用于存储解析出来的 YAML::Node
        std::vector&lt;std::pair&lt;const std::string, const YAML::Node&gt;&gt; all_nodes;
        
        /**
        * @brief 递归地将一个YAML节点扁平化一系列&#34;点分路径&#34;键和对应节点的键值对
        * @details 递归遍历YAML节点, 构造以点号连接的键路径, 并将每个路径及其对应
        *          的 YAML::Node 添加到输出列表中, 从而实现将 YAML 的层级结构扁平化. 
        *          只处理结点为 Map 的情况, 不会处理 Sequence (避免过度扁平不利于管理).
        * 第一个参数为 当前键路径的前缀
        * 第二个参数为 当前处理的 YAML 节点
        * 第三个参数为 存储扁平化结果
        */
        listAllMembers(&#34;&#34;, root, all_nodes);

        // 依次处理每个 YAML::Node
        for (const auto&amp; node_pair : all_nodes)
        {
        // 获取该配置变量指针
        const std::string&amp; key = node_pair.first;
        ConfigVarBase::ptr var_ptr = getConfigVarBasePtr(key);

        // 判断该 key 是否已经注册, 注册则更新其值
        if (var_ptr)
        {
            // 经过前面的 listAllMembers 处理, 这里的 YAML::Node 只会是标量或列表
            if (node_pair.second.IsScalar())
            {
            var_ptr-&gt;fromString(node_pair.second.Scalar());
            }
            else
            {
            std::stringstream ss;
            ss &lt;&lt; node_pair.second;
            var_ptr-&gt;fromString(ss.str());
            }
        }
        else
        {
        // 没注册的配置, 则写入日志
            VELOX_WARN(&#34;Unrecognized config key: {}&#34;, key);
        }
        }
    }
    ```

1. `loadFromConfDir(const std::string&amp; relative_dir_path, bool force = false)`

    ```cpp
    /**
    * @brief 加载指定项目子目录里面的配置文件
    * @details 根据 force 参数来判断是否强制加载全部配置文件
    *          force == false时, 只加载更新过的配置文件
    *          force == true时, 加载指定目录下的全部配置文件
    */
    void Config::loadFromConfDir(const std::string&amp; relative_dir_path, bool force)
    {
        // 获取项目根目录下的相对目录路径下的扩展为 .yml 的全部文件的绝对路径
        // 假设项目根路径为: /home/xxx/velox, relative_dir_path = config
        // 那么就会去 /home/xxx/velox/config 这个目录下找到所有 .yml 文件, 并将它们
        // 的绝对路径一起返回(以vector的形式返回).
        auto files = velox::util::listAllFilesByExt(relative_dir_path, &#34;.yml&#34;);

        // 依次处理每个配置文件
        for (const auto&amp; file : files)
        {
        // 获取该文件的最新写入时间
        std::error_code ec;
        auto last_write_time = fs::last_write_time(file, ec);
        if (ec)
        {
            VELOX_WARN(&#34;Skip config file &#39;{}&#39;: failed to get last write time: {}&#34;, file.string(), ec.message());
            continue;
        }

        auto current_timestamp = velox::util::toUnixTimestamp(last_write_time);
        auto&amp; cached_timestamp = getCachedTimestampForFile(file);

        // 在 force == false 且该文件后续没有被修改时 跳过该配置文件
        if (!force &amp;&amp; current_timestamp == cached_timestamp)
        {
            VELOX_INFO(&#34;Skip config file &#39;{}&#39;: unchanged since last load.&#34;, file.string());
            continue;
        }

        try
        {
            cached_timestamp = current_timestamp;  // 将该文件最新写入时间进行缓存
            YAML::Node root = YAML::LoadFile(file);
            loadFromYaml(root);  // 调用该函数对 YAML::Node 进行解析并更新存储的值
            VELOX_INFO(&#34;Loaded config file &#39;{}&#39;&#34;, file.string());
        }
        catch (const std::exception&amp; e)
        {
            VELOX_ERROR(&#34;Failed to load config file &#39;{}&#39;: {}&#34;, file.string(), e.what());
        }
        catch (...)
        {
            VELOX_ERROR(&#34;Failed to load config file &#39;{}&#39;: unknown error.&#34;, file.string());
        }
        }
    }
    ```

### ConfigVar类

该类用于定义配置参数结构, 除了实现 `ConfigVarBase` 的虚函数外, 还新增了一些函数, 其中比较重要的有:

1. `setValue(const T&amp; value)`

    ```cpp
        /**
        * @brief 设置当前参数的值
        * @details 当参数的值发生变化时会调用该变量所对应的变更回调函数
        */
        void setValue(const T&amp; value)
        {
        // 判断新旧值是否相同
        if (value == m_val)
        {
            return;
        }

        // 更新值
        T old_val = m_val;
        m_val = value;

        // m_cbs 是一个 unordered_map, 用于存储该配置参数的全部回调函数
        for (const auto&amp; [id, cb] : m_cbs)
        {
            // 调用注册过的回调函数
            cb(old_val, m_val);
        }
        }
    ```

    使用回调函数机制可以在该配置参数的值发生变化时进行一些处理（比如更新`log`模块的对应参数等）。

2. `addListener(on_change_cb cb)`

    ```cpp
    // using on_change_cb = std::function&lt;void(const T&amp; old_val, const T&amp; new_val)&gt;;
    // std::unordered_map&lt;uint64_t, on_change_cb&gt; m_cbs;

        /**
        * @brief 添加变化回调函数
        * @return 返回该回调函数对应的唯一 id, 用于删除回调
        */
        uint64_t addListener(on_change_cb cb)
        {
        // 自动生成该回调函数的 key
        static uint64_t s_fun_id = 0;
        &#43;&#43;s_fun_id;
        
        m_cbs[s_fun_id] = cb;
        return s_fun_id;
        }
    ```

## 使用示例

`Config`模块的使用都是通过调用`Config`类中的静态成员函数来实现。

```cpp
#include &#34;config.hpp&#34;
#include &lt;vector&gt;

// 定义一个结构体
struct ServerDefine
{
    std::vector&lt;std::string&gt; address;
    int keepalive = 0;  // 可选, 默认 0
    int timeout = 1000;
    std::string name;
    std::string accept_worker;
    std::string io_worker;
    std::string process_worker;
    std::string type;

    bool operator==(const ServerDefine&amp; other) const
    {
      return address == other.address &amp;&amp; keepalive == other.keepalive &amp;&amp; timeout == other.timeout &amp;&amp; name == other.name &amp;&amp;
             accept_worker == other.accept_worker &amp;&amp; io_worker == other.io_worker &amp;&amp;
             process_worker == other.process_worker &amp;&amp; type == other.type;
    }
};

// 实现对应的偏特化, 用于 YAML:Node 和 ServerDefine 类型之间的转换
namespace velox::config
{
  // string -&gt; ServerDefine
  template&lt;&gt;
  class TypeConverter&lt;std::string, ServerDefine&gt;
  {
   public:
    ServerDefine operator()(const std::string&amp; v)
    {
      YAML::Node node = YAML::Load(v);
      ServerDefine def;

      if (node[&#34;address&#34;].IsDefined())
      {
        def.address = node[&#34;address&#34;].as&lt;std::vector&lt;std::string&gt;&gt;();
      }
      else
      {
        VELOX_ERROR(&#34;server address list is null&#34;);
        throw std::logic_error(&#34;server address list is null&#34;);
      }

      if (node[&#34;keepalive&#34;].IsDefined())
      {
        def.keepalive = node[&#34;keepalive&#34;].as&lt;int&gt;();
      }

      if (node[&#34;timeout&#34;].IsDefined())
      {
        def.timeout = node[&#34;timeout&#34;].as&lt;int&gt;();
      }

      if (node[&#34;name&#34;].IsDefined())
      {
        def.name = node[&#34;name&#34;].as&lt;std::string&gt;();
      }
      else
      {
        VELOX_ERROR(&#34;server name is empty&#34;);
        throw std::logic_error(&#34;server name is empty&#34;);
      }

      if (node[&#34;accept_worker&#34;].IsDefined())
      {
        def.accept_worker = node[&#34;accept_worker&#34;].as&lt;std::string&gt;();
      }
      else
      {
        VELOX_ERROR(&#34;server accept worker is empty&#34;);
        throw std::logic_error(&#34;server accept worker is empty&#34;);
      }

      if (node[&#34;io_worker&#34;].IsDefined())
      {
        def.io_worker = node[&#34;io_worker&#34;].as&lt;std::string&gt;();
      }
      else
      {
        VELOX_ERROR(&#34;server io worker is empty&#34;);
        throw std::logic_error(&#34;server io worker is empty&#34;);
      }

      if (node[&#34;process_worker&#34;].IsDefined())
      {
        def.process_worker = node[&#34;process_worker&#34;].as&lt;std::string&gt;();
      }
      else
      {
        VELOX_ERROR(&#34;server process worker is empty&#34;);
        throw std::logic_error(&#34;server process worker is empty&#34;);
      }

      if (node[&#34;type&#34;].IsDefined())
      {
        def.type = node[&#34;type&#34;].as&lt;std::string&gt;();
      }
      else
      {
        VELOX_ERROR(&#34;server type is empty&#34;);
        throw std::logic_error(&#34;server type is empty&#34;);
      }

      return def;
    }
  };

  // ServerConfig -&gt; string
  template&lt;&gt;
  class TypeConverter&lt;ServerDefine, std::string&gt;
  {
   public:
    std::string operator()(const ServerDefine&amp; def)
    {
      YAML::Node node;

      node[&#34;address&#34;] = def.address;
      if (def.keepalive != 0)
      {
        node[&#34;keepalive&#34;] = def.keepalive;
      }
      node[&#34;timeout&#34;] = def.timeout;
      node[&#34;name&#34;] = def.name;
      node[&#34;accept_worker&#34;] = def.accept_worker;
      node[&#34;io_worker&#34;] = def.io_worker;
      node[&#34;process_worker&#34;] = def.process_worker;
      node[&#34;type&#34;] = def.type;

      std::stringstream ss;
      ss &lt;&lt; node;
      return ss.str();
    }
  };

}  // namespace velox::config

int main()
{
    // 先注册配置变量
  auto servers_config = Config::getOrCreateConfigVarPtr&lt;std::vector&lt;velox::test::ServerDefine&gt;&gt;(
      &#34;servers&#34;, std::vector&lt;velox::test::ServerDefine&gt;(), &#34;servers config&#34;);
    return 0;

    // 添加一个回调函数(可选)
    auto cb_id = servers_config-&gt;addListener(
      [](const std::vector&lt;ServerDefine&gt;&amp; old_val, const std::vector&lt;ServerDefine&gt;&amp; new_val)
      { std::cout &lt;&lt; &#34;servers value change.\n&#34;; });

   // 构造 YAML 内容
  std::string yaml_content = R&#34;(
servers:
  - address: [&#34;0.0.0.0:8090&#34;, &#34;127.0.0.1:8091&#34;, &#34;/tmp/test.sock&#34;]
    keepalive: 1
    timeout: 1000
    name: sylar/1.1
    accept_worker: accept
    io_worker: http_io
    process_worker: http_io
    type: http
  - address: [&#34;0.0.0.0:8062&#34;, &#34;0.0.0.0:8061&#34;]
    timeout: 1000
    name: sylar-rock/1.0
    accept_worker: accept
    io_worker: io
    process_worker: io
    type: rock)&#34;;

  // 解析 YAML
  YAML::Node root = YAML::Load(yaml_content);
  Config::loadFromYaml(root); // 这里servers_config的值会发生变化, 触发回调函数
}
```

更多使用示例，可查看`test/config/config_test.cpp`文件


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/325196d/  


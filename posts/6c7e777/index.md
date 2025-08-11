# Cmake学习笔记


该笔记只是用于记录和梳理`CMake`中比较重要的知识，并不会写得很详细（比如不会写出使用`project()`命令后会创建哪些变量）。学完本笔记**即可上手使用`CMake`**，一些很细的知识可以在遇到所用的场景时自行查阅[官方资料](https://cmake.org/cmake/help/v3.30/index.html)。

## 基础框架

最基本的`CMake`项目是由单个源代码文件构建可执行文件，对于这种简单的项目，只需要给 **`CMakeLists.txt`** 文件提供三个命令：

```CMake
cmake_minimum_required(VERSION x.xx) # 指定CMake最低版本

project(
    ProjectName     # 项目名称
    VERSION x.x.x   # 项目版本
    LANGUAGES CXX   # 项目语言, CXX表示C&#43;&#43;
)

add_executable(ExecutableName SourceCodePath) # 设置可执行文件的名称和源代码路径
```

例如：

```cmake
cmake_minimum_required(VERSION 3.15)

project(
    MBFF
    VERSION 0.1.12
    LANGUAGES CXX
)

add_executable(MBFF src/main.cpp)
```

这样我们就可以产生一个名为`MBFF`的可执行文件。

其中 `cmake_minimum_required()`和`project()`命令需要在项目的顶级`CMakeLists.txt`给出（当项目十分复杂时，会存在多个`CMakeLists.txt`文件）。

- `cmake_minimum_required()`: 该命令是`CMakeLists.txt`中**第一条**执行的命令，用于控制`CMake`的版本，禁用一些已启用的特性
- `project()`: 设置项目的相关属性，可以只设置项目名称，但推荐按上面的写法来用
- `add_executable()`: 产生可执行文件，没有该命令是不会产生可执行文件的

**注意**：`CMakeLists.txt`的命令执行顺序是从上往下的，所以要**有逻辑的编写命令**。

使用上面的命令足够处理一些基本情况了，但是当我们在代码中使用了一些`C&#43;&#43;17`或更高标准的特性后，会发现编译失败，这是因为编译器会使用默认标准（通常为 `C&#43;&#43;98/GCC` 或 `C&#43;&#43;14/Clang`， 可通过输出变量 `CMAKE_CXX_STANDARD_DEFAULT` 的值查询默认值）来进行编译，导致不存在这些特性。这时，就需要使用 **`set()`** 命令来进行相关设置：

```cmake
cmake_minimum_required(VERSION 3.15)

project(
    MBFF
    VERSION 0.1.12
    LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_executable(MBFF src/main.cpp)
```

在上面的内容中，我们使用`set()`命令为`CMAKE_CXX_STANDARD`、`CMAKE_CXX_STANDARD_REQUIRED`、`CMAKE_CXX_EXTENSIONS`三个变量进行了赋值，其中：

- `CMAKE_CXX_STANDARD`：设置使用的C&#43;&#43;标准，未设置时编译器使用默认值
- `CMAKE_CXX_STANDARD_REQUIRED`：控制是否强制要求编译器支持指定标准，若为`OFF`时编译器可能会回退到旧标准（比如指定标准为C&#43;&#43;20，但没有强制要求，clang编译器会使用有扩展的C&#43;&#43;17标准）
- `CMAKE_CXX_EXTENSIONS`：控制是否禁止使用编译器特定的C&#43;&#43;扩展，为`OFF`时强制代码遵循ISO C&#43;&#43; 标准

**注意**：CMake中存在一系列以`CMAKE_`开头的变量，它们都有特殊的用途，在创建变量时应该避免以`CMAKE_`开头。

通过上面的内容，我们可以得到一份基础的`CMakeLists.txt`：

```cmake
cmake_minimum_required(VERSION 版本要求)

project(
    项目名称
    VERSION 版本号
    LANGUAGES 语言要求
)

set(CMAKE_CXX_STANDARD 标准)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_executable(二进制文件名称 源代码路径)
```

如果有在源代码中使用CMake变量的需求，可以参考[官方文档](https://cmake.org/cmake/help/v3.30/guide/tutorial/A%20Basic%20Starting%20Point.html#exercise-3-adding-a-version-number-and-configured-header-file)。

## 生成库文件并使用

通常来说，我们都会将项目生成一个可执行文件，但是，当我们有代码复用需求、模块解耦需求或商业保护需求时，就需要将项目生成一个库文件来使用。例如，当项目需要我们自己编写一个数学库时，就可以在项目根目录下创建一个 **mylibrary** 目录，然后在该子目录下存放对应的`CMakeLists.txt`文件和源文件，如下所示：

```bash
Mylibrary  
 ┣ 3rdparty  
 ┣ include  
 ┃ ┗ math.h  
 ┣ src  
 ┃ ┗ math.cpp  
 ┗ CMakeLists.txt
```

该子目录下的`CMakeLists.txt`文件内容为：

```cmake
add_library(MathFunctions math.cpp) # 创建名为 MathFunctions 的库文件

target_include_directories(
  MathFunctions 
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include      # 公共 API
  PRIVATE  ${CMAKE_CURRENT_SOURCE_DIR}/src        # 实现代码头文件
  INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/3rdparty  # 链接者依赖但本库不直接使用
)
```

1. `add_library(&lt;name&gt; [STATIC | SHARED] [EXCLUDE_FROM_ALL] source1 [source2...])`用于生成库文件，其参数为：
   - `&lt;name&gt;`：库名称（全局唯一）
   - `STATIC`/`SHARED`：创建静态库（.a/.lib）/动态库（.so/.dll），默认为静态库
   - `EXCLUDE_FROM_ALL`：不包含在默认构建中
   - `sourceX`：源代码文件列表
2. `target_include_directories(&lt;target&gt; [PRIVATE|PUBLIC|INTERFACE] [&lt;dirs&gt;...])`用于为特定目标设置头文件搜索路径，其参数为：
   - `&lt;target&gt;`：定义的目标，在上面的例子中为 `MathFunctions`
   - `PRIVATE`、`PUBLIC`、`INTERFACE`：修饰词，用于决定这些路径的**可见性**。这些修饰词的使用场景为：
     - `PUBLIC`：库本身的`.cpp`文件和链接该库的其他目标的源文件（比如这里的`src/main.cpp`）需要`include`这个目录
     - `PRIVATE`：只有库本身的`.cpp`文件需要`include`这个目录
     - `INTERFACE`：链接者需要依赖，但库本身不需要（例如纯头文件库、宏定义、编译选项等）
   - `&lt;dirs&gt;`：一个或多个目录路径，告诉编译器在这些路径中查找头文件

**为什么该子目录只需写这两行命令？**

因为`CMake`的变量具备传递性，顶层`CMakeLists.txt`文件产生的全局变量都可以被子目录的`CMakeLists.txt`文件访问，所以无需在子目录的`CMakeLists.txt`文件重复写`project()`，`cmake_minimum_required()`等命令。

**为什么不在顶层文件中使用target_include_directories()命令？**

顶层`CMakeLists.txt` 中不推荐写任何以`target_`开头的命令，因为这会破坏模块封装性以及导致顶层`CMakeLists.txt`变复杂，逻辑耦合太重。

上面的 **`CMAKE_CURRENT_SOURCE_DIR`** 为特殊变量，存储了当前`CMakeLists.txt`文件的路径。为了使用生成的库，我们需要在顶层`CMakeLists.txt`文件添加下面两个命令：

```camke
add_subdirectory(Mylibrary)
target_link_libraries(MBFF PRIVATE MathFunctions)
```

1. `add_subdirectory()`：添加一个目录，并执行该目录下的`CMakeLists.txt`
2. `target_link_libraries()`：将定义好的库链接到目标`MBFF`中，其也有`PRIVATE`、`PUBLIC`、`INTERFACE`三个修饰词可选

完整的顶层`CMakeLists.txt`文件内容为：

```cmake
cmake_minimum_required(VERSION 3.15)

Project(
  MBFF
  VERSION 1.0
  LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_subdirectory(Mylibrary)

add_executable(MBFF src/main.cpp)
 
target_link_libraries(MBFF PRIVATE MathFunctions)
```

这样，就能够在代码中使用编写的库函数了。

### 在指定源文件中生成宏定义

有时我们需要在编译时指定一些参数，用来控制程序的行为（这在大型文件中非常常见，比如不同的平台代码有不同的处理逻辑）。例如在前面的例子中，我们希望在编译时能够指定程序调用的是标准的数学库还是我们自己编写的数学库，这时就需要使用下面的三个命令：
1. `if`命令，用于条件判断，语法为：
	```cmake
	if(&lt;condition&gt;)
		# 条件为真时执行
	elseif(&lt;condition&gt;)
		# 可选：其他条件
	else()
		# 可选：所有条件都不满足时执行
	endif()
	```

2. `option`命令，用于定义**布尔**变量（`set`命令可以定义任意类型的变量），语法为：
	```cmake
	option(&lt;option_variable&gt; &#34;Description text&#34; &lt;initial_value&gt;)
	```
3. `target_compile_definitions`命令，向**目标对应的源文件**添加预处理器宏定义，语法为：
	```cmake
	target_compile_definitions(
		&lt;target&gt;
	    [PRIVATE|PUBLIC|INTERFACE]
	    &lt;definition1&gt; &lt;definition2&gt; ...
	)
	```

通过使用上面的三个命令将顶层`CMakeLists.txt`文件修改为：

```cmake
cmake_minimum_required(VERSION 3.15)

Project(
  MBFF
  VERSION 1.0
  LANGUAGES CXX
)

option(USE_MYMATH &#34;Use custom math library instead of standard math&#34; ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# 只有开启USE_MATH时才添加数学库子目录
if(USE_MYMATH)
    message(STATUS &#34;Using custom math library&#34;) # message()命令用于输出信息
    add_subdirectory(Mylibrary)
endif()

add_executable(MBFF src/main.cpp)

if(USE_MYMATH)
    message(STATUS &#34;Linking with MathFunctions library&#34;)
    target_link_libraries(MBFF PRIVATE MathFunctions)
    target_compile_definitions(MBFF PRIVATE USE_MYMATH)
else()
    message(STATUS &#34;Using standard math library&#34;)
    target_link_libraries(MBFF PRIVATE m)
endif()
```

然后在`main.cpp`中使用预处理器指令（即`#ifdef USE_MYMATH`）即可完成上面所说的功能了。

最后使用 **`cmake .. -DUSE_MYMATH=OFF`** 为`CMake`传递参数 。

## 设置不同编译器标志

在前面的示例中，我们在顶层`CMakeLists.txt`文件设定了项目的全局编译器标准(即`C&#43;&#43;17`)，但是在实际的大型项目中，极大可能每个库所使用的标准都不一样（有的要求C&#43;&#43;11，有的要求C&#43;&#43;14）。这时，就需要使用下面的命令来声明编译目标所需的**语言特性**（如特定的C&#43;&#43;标准或关键字支持）：
`target_compile_features(&lt;target&gt; &lt;INTERFACE|PUBLIC|PRIVATE&gt; &lt;feature&gt; [...])`

修改Mylibrary目录下的`CMakeLists.txt`文件为：

```cmake
add_library(MathFunctions src/math.cpp)

target_include_directories(
  MathFunctions
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
)

# 设定编译标准
target_compile_features(MathFunctions PRIVATE cxx_std_20)
```

然后进行编译即可。

如果你使用命令 `cmake --build . --clean-first -- VERBOSE=1`打印详细的编译过程，会发现`MathFunctions`库的编译并没有使用`C&#43;&#43;20`标准，这是为什么呢？这是因为：
&gt; `target_compile_features()`命令**不直接设置编译器标志（如 `-std=c&#43;&#43;20`）**，而是让 CMake **==自动判断是否需要这些编译器选项==** 以启用这些特性。即CMake 通过检测特性需求来**隐式决定是否加上 `-std=c&#43;&#43;20` 等参数**。

想要强制编译该库时使用某些编译器选项时可使用 **`set_target_properties()`** 命令

## 生成器表达式

生成器表达式用于在**生成构建系统**的过程中，根据不同上下文（如构建配置、目标类型、编译器等）**动态**地生成内容。其基本语法为：

```cmake
$&lt;expression&gt;
```

表达式可以是条件判断、逻辑操作、语言/编译器识别、目标属性获取等。有些表达式还有嵌套结构，如：`$&lt;IF:$&lt;CONFIG:Debug&gt;,ON,OFF&gt;`

常见表达式类型及语法规则为（更多内容可查阅[官方文档](https://cmake.org/cmake/help/v3.30/manual/cmake-generator-expressions.7.html#manual:cmake-generator-expressions(7))）：

| 表达式语法                            | 类型         | 说明                     |
| ------------------------------------- | ------------ | ------------------------ |
| `$&lt;IF:cond,true_val,false_val&gt;`       | 条件选择     | 类似于三目运算符         |
| `$&lt;BOOL:value&gt;`                       | 布尔判断     | ON/1 为真，其他为假      |
| `$&lt;CONFIG:cfg&gt;`                       | 构建配置判断 | 如 Debug、Release        |
| `$&lt;COMPILE_LANG_AND_ID:lang,id1,...&gt;` | 编译器识别   | 判断是否是某类编译器     |
| `$&lt;BUILD_INTERFACE:...&gt;`              | 构建作用域   | 当前是构建而非安装       |
| `$&lt;INSTALL_INTERFACE:...&gt;`            | 安装作用域   | 当前是 install/export 后 |
| `$&lt;TARGET_PROPERTY:target,prop&gt;`      | 获取目标属性 | 如 include 路径等        |
| `$&lt;JOIN:list,delim&gt;`                  | 字符串拼接   | 将列表合并为字符串       |
| `$&lt;REMOVE_DUPLICATES:list&gt;`           | 去重列表     | 去除重复元素             |

比如，在前面的例子中，我们希望能够根据构建类型在`main.cpp`中启用宏定义以及根据编译器选择警告选项，那么可以在顶层`CMakeLists.txt`文件中添加生成器表达式：

```cmake
cmake_minimum_required(VERSION 3.15)

Project(
  MBFF
  VERSION 1.0
  LANGUAGES CXX
)

option(USE_MYMATH &#34;Use custom math library instead of standard math&#34; ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(USE_MYMATH)
    message(STATUS &#34;Using custom math library&#34;)
    add_subdirectory(Mylibrary)
endif()

add_executable(MBFF src/main.cpp)

# 根据构建类型启用宏定义
target_compile_definitions(MBFF PRIVATE &#34;$&lt;$&lt;CONFIG:Debug&gt;:DEBUG_BUILD&gt;&#34;)

# 根据编译器选择警告选项
target_compile_options(MBFF PRIVATE
  &#34;$&lt;$&lt;COMPILE_LANG_AND_ID:CXX,GNU,Clang&gt;:-Wall;-Wextra&gt;&#34;
  &#34;$&lt;$&lt;COMPILE_LANG_AND_ID:CXX,MSVC&gt;:/W3&gt;&#34;
)

if(USE_MYMATH)
    message(STATUS &#34;Linking with MathFunctions library&#34;)
    target_link_libraries(MBFF PRIVATE MathFunctions)
    target_compile_definitions(MBFF PRIVATE USE_MYMATH)
else()
    message(STATUS &#34;Using standard math library&#34;)
    target_link_libraries(MBFF PRIVATE m)
endif()
```

## 安装和测试

### 安装

在软件开发和部署过程中，仅构建可执行文件（如通过`make`或编译命令生成二进制文件）通常​**​不够​**​，还需要通过相关安装步骤完成部署。

首先我们需要在`CMakeLists.txt`文件中用 **`install()`** 命令来指定安装时运行的规则，[这里](https://blog.csdn.net/qq_38410730/article/details/102837401)有该命令的详细说明。

在顶层和Mylibrary目录下的`CMakeLists.txt`文件最后分别加上下面的内容：

```cmake
# 顶层 CMakeLists.txt
# 指定可执行文件安装目录
install(TARGETS MBFF
  RUNTIME DESTINATION bin
)

# Mylibrary下的CMakeLists.txt
# 指定可执行文件, 动态库, 静态库安装位置
install(TARGETS MathFunctions
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
)
# 指定 头文件安装位置
install(FILES include/math.h
  DESTINATION include
)
```

然后在编译完成后（即执行完`cmake --build .`命令后），执行下面的命令：
`cmake --install . --prefix ../install`

- `--instal`: 执行安装操作
- `--prefix`: 指定安装目录

### 测试

`CMake`内部集成了一个测试管理工具`CTest`，该工具用来测试**可执行文件**。如果想要更细粒化的测试推荐使用[GoogleTest](https://github.com/google/googletest)框架。

在顶层`CMakeLists.txt`文件末尾添加下面的内容：

```cmake
enable_testing() # 启动测试

add_test(NAME Runs COMMAND MBFF 7) # 验证程序能否正常工作

# 验证程序功能是否正确
add_test(NAME StandardUse COMMAND MBFF 4)
set_tests_properties(StandardUse
  PROPERTIES
    PASS_REGULAR_EXPRESSION &#34;4 sqrt is 2&#34;
)

# 定义参数化测试函数
function(do_test target arg result)
  add_test(NAME Comp${arg} COMMAND ${target} ${arg})
  set_tests_properties(Comp${arg}
    PROPERTIES
      PASS_REGULAR_EXPRESSION &#34;${result}&#34;
      OUTPUT_STRIP_TRAILING_WHITESPACE ON
  )
endfunction()

# 调用测试函数
do_test(MBFF 9 &#34;9 sqrt is 3&#34;)
do_test(MBFF 5 &#34;5 sqrt is 2&#34;)
do_test(MBFF 7 &#34;7 sqrt is 2&#34;)
do_test(MBFF 25 &#34;25 sqrt is 5&#34;)
do_test(MBFF -25 &#34;-25 sqrt is (nan|-nan|-1)&#34;)
do_test(MBFF 0.0001 &#34;0.0001 sqrt is 0.01&#34;)
```

其中：

- `add_test(NAME &lt;Name&gt; COMMAND &lt;command&gt;)`：定义一个测试用例
- `set_tests_properties(&lt;name&gt; PROPERTIES ...)`：为已定义的测试用例设置属性

然后在项目的`build`目录下执行 `ctest` 命令，即可看到下面的输出：

```bash
Test project /home/xxx/CPlusPlusProject/CMakePractice/build
    Start 1: Runs
1/8 Test #1: Runs .............................   Passed    0.00 sec
    Start 2: StandardUse
2/8 Test #2: StandardUse ......................   Passed    0.00 sec
    Start 3: Comp9
3/8 Test #3: Comp9 ............................   Passed    0.00 sec
    Start 4: Comp5
4/8 Test #4: Comp5 ............................   Passed    0.00 sec
    Start 5: Comp7
5/8 Test #5: Comp7 ............................   Passed    0.00 sec
    Start 6: Comp25
6/8 Test #6: Comp25 ...........................   Passed    0.00 sec
    Start 7: Comp-25
7/8 Test #7: Comp-25 ..........................   Passed    0.00 sec
    Start 8: Comp0.0001
8/8 Test #8: Comp0.0001 .......................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 8

Total Test time (real) =   0.01 sec
```

如果你的项目是**跨平台的，或想要监控质量指标（比如覆盖率）、历史追踪、可视化测试结果**等功能可以使用CMake中的 **`CDash`** ，该组件的详细说明可查看[官方文档](https://cmake.org/cmake/help/v3.30/guide/tutorial/Adding%20Support%20for%20a%20Testing%20Dashboard.html)。该组件的使用方法简单来说就是，将前面顶层`CMakeLists.txt`文件的`enable_testing()`改写为`include(CTest)`，然后在顶层目录下编写一个`CTestConfig.cmake`文件（该文件用来配置CDash的相关信息），最后在`build/`执行`ctest -D Experimental`命令即可。

## 编译环境探测

在实际开发中，我们很容易遇到跨平台、需适配**不同编译环境**的场景。这时，我们需要项目能够兼容多种环境、动态调整编译逻辑，**`check_cxx_source_compiles`** 命令就能派上用场了。

在使用该命令前，我们需要引入检查模块：

```cmake
include(CheckCXXSourceCompiles)
```

该命令的基本语法为：

```cmake
check_cxx_source_compiles(&lt;code&gt; &lt;resultVar&gt; [FAIL_REGEX &lt;regex1&gt; &lt;regex2&gt;...])
```

- **`&lt;code&gt;`**：必填参数，是要测试的 C&#43;&#43; 代码片段（字符串形式），需能编译生成可执行文件（包含 `main` 函数等完整入口逻辑 ）。
- **`&lt;resultVar&gt;`**：必填参数，用于存储检测结果的变量名。若代码编译成功，变量值为 `TRUE`；失败则为 `FALSE`（或空值、错误信息，依场景而定 ）。
- **`[FAIL_REGEX ...]`**：可选参数，若编译输出（如警告、错误日志）匹配指定正则表达式，即便代码编译成功，也判定为 “检测失败”。

比如，我们代码中使用了C&#43;&#43;17的特性`std::filesystem`和结构化绑定，想要确认编译器是否支持，可以这样写：

```cmake
include(CheckCXXSourceCompiles) 

set(CMAKE_REQUIRED_FLAGS &#34;-std=c&#43;&#43;17&#34;) # 指定测试命令的编译环境, 不影响项目

# 测试代码：使用结构化绑定和 std::filesystem 
set(TEST_CODE &#34;
#include &lt;filesystem&gt; 
#include &lt;tuple&gt; 

int main() 
{ 
  // 示例1：从 pair 中提取值 
  auto [a, b] = std::make_pair(1, 2.5); 
  
  // 示例2：从 filesystem 中获取路径组件 
  std::filesystem::path p = \&#34;/home/user/example.txt\&#34;; 
  auto [root, stem, ext] = std::tuple{ 
  p.root_name(),
  p.stem(), 
  p.extension() 
  }; 

  return 0; 
} 
&#34;) 

check_cxx_source_compiles(&#34;${TEST_CODE}&#34; HAVE_CPP17_STRUCTURED_BINDINGS)

unset(CMAKE_REQUIRED_FLAGS)  # 用完立即清理该变量

if(HAVE_CPP17_STRUCTURED_BINDINGS) 
  message(STATUS &#34;编译器支持 C&#43;&#43;17 结构化绑定特性&#34;) 
  target_compile_features(YourProject PRIVATE cxx_std_17) # 启用 C&#43;&#43;17 标准 
else() 
  message(STATUS &#34;编译器不支持 C&#43;&#43;17 结构化绑定特性&#34;) 
  # 这里可以添加降级策略或错误处理 
endif()
```

## 注入自定义逻辑

当我们需要在构建时动态生成头文件（如数学表、版本信息）、配置文件（`config.h`），或处理非传统文件依赖时就需要在`CMake`中使用 **`add_custom_command`** 命令在CMake构建过程中注入自定义逻辑。

该命令语法如下：

```cmake
add_custom_command(
  OUTPUT &lt;output_files&gt;...          # 生成的目标文件路径（用于判断是否执行下面的命令）
  COMMAND &lt;command&gt; [&lt;args&gt;...]     # 要执行的命令（支持多条，按顺序执行）
  [MAIN_DEPENDENCY &lt;main_dep&gt;]      # 主依赖文件（变化时强制重新生成输出）
  [DEPENDS &lt;deps&gt;...]               # 附加依赖（文件/目标变化时触发重新执行）
  [IMPLICIT_DEPENDS &lt;lang&gt; &lt;file&gt;]  # 隐式依赖（如 C&#43;&#43; 语法分析推导的依赖，需指定语言）
  [WORKING_DIRECTORY &lt;dir&gt;]         # 命令执行的工作目录（默认当前源目录）
  [COMMENT &lt;message&gt;]               # 执行时显示的提示信息（方便调试）
  [VERBATIM]                        # 保留命令原始格式（避免转义特殊字符，如 $、# ）
  [USES_TERMINAL]                   # 在终端中执行命令（Windows 下适用，保证输出可见 ）
)
```

以之前的数学库代码为例，我们想要生成一个预先计算值表，以便自定义的`sqrt`函数使用，加快计算速度。

在自定义库目录下先创建生成计算值表的代码`maketable.cpp`：

```cpp
#include &lt;cmath&gt;
#include &lt;fstream&gt;
#include &lt;iostream&gt;

int main(int argc, char* argv[])
{
  if (argc != 2)
  {
    std::cerr &lt;&lt; &#34;Error: You need to specify an output file name as a parameter&#34; &lt;&lt; std::endl;
    std::cerr &lt;&lt; &#34;Usge: &#34; &lt;&lt; argv[0] &lt;&lt; &#34; &lt;OutputFile&gt;&#34; &lt;&lt; std::endl;
    return 1;
  }

  std::ofstream fout(argv[1]);
  if (!fout)
  {
    std::cerr &lt;&lt; &#34;Error: Unable to open output file: &#34; &lt;&lt; argv[1] &lt;&lt; std::endl;
    return 1;
  }

  // 生成平方根表
  fout &lt;&lt; &#34;#ifndef SQRT_TABLE_H\n&#34;
       &lt;&lt; &#34;#define SQRT_TABLE_H\n\n&#34;
       &lt;&lt; &#34;static constexpr double sqrtTable[10] = {\n&#34;
       &lt;&lt; &#34;    0.0,  // 索引 0 未使用\n&#34;;
  
  for (int i = 1; i &lt;= 9; &#43;&#43;i)
  {
    fout &lt;&lt; &#34;    &#34; &lt;&lt; std::sqrt(i) &lt;&lt; &#34;,  // sqrt(&#34; &lt;&lt; i &lt;&lt; &#34;)\n&#34;;
  }

  fout &lt;&lt; &#34;};\n\n&#34;
       &lt;&lt; &#34;#endif // SQRT_TABLE_H\n&#34;;

  std::cout &lt;&lt; &#34;已生成 &#34; &lt;&lt; argv[1] &lt;&lt; std::endl;
  return 0;
}
```

同时，在该目录下创建对应的`MakeTable.cmake`文件：

```cmake
add_executable(MakeTable src/maketable.cpp)

# 定义生成 Table.h 的自定义命令
add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/table.h
    COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/table.h
    DEPENDS MakeTable  # 依赖 MakeTable 可执行文件
    COMMENT &#34;Generating sqrt table (Table.h)&#34;  # 构建时显示的信息
)
```

再修改自定义库下的`CMakeLists.txt`文件：

```cmake
include(MakeTable.cmake) # 包含配置文件

add_library(MathFunctions STATIC
  src/math.cpp
  ${CMAKE_CURRENT_BINARY_DIR}/table.h # 添加依赖
)

target_include_directories(
  MathFunctions
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
  PRIVATE ${CMAKE_CURRENT_BINARY_DIR} #添加生成的头文件目录
)

install(TARGETS MathFunctions
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
)

install(FILES include/math.h
  DESTINATION include
)
```

## 打包分发

当有将项目打包成可分发的安装包（二进制包、源码包）的需要时，可以使用`CPack`工具来实现。

该工具可以打包的类型为：

- **二进制包（Binary Package）**：  
    包含已编译的可执行文件、库文件、配置文件等，用户拿到后无需编译即可直接运行。  
    **常见格式**：`.tar.gz`（Linux）、`.deb`（Debian/Ubuntu）、`.rpm`（RedHat/CentOS）、`.msi`（Windows）。
- **源码包（Source Package）**：  
    包含项目的所有源文件（.cpp、.h、CMakeLists.txt 等），用户需要自行编译才能使用。  
    **常见格式**：`.tar.gz`、`.zip`。

`CPack`通常写在项目根目录下的`CMakeLists.txt`中，该工具基于`install()`命令的规则来进行打包。

该工具的使用方法可参考[链接1](https://cmake.org/cmake/help/v3.30/guide/tutorial/Packaging%20an%20Installer.html)，[链接2](https://cmake.org/cmake/help/v3.30/module/CPack.html#module:CPack)。

在进行跨平台编译时，需要注意 Windows 与 Linux/macOS 在动态库实现上有一个关键差异：

- **Windows**：动态库（DLL）默认隐藏所有函数和类，必须显式使用 `__declspec(dllexport)` 标记才能被外部访问
- **Linux/macOS**：动态库（.so/.dylib）默认公开所有函数和类，只有使用 `__attribute__((visibility(&#34;hidden&#34;)))` 才能隐藏

**这个差异导致**：

1. 在 Windows 上开发动态库时，必须为每个要公开的函数 / 类添加导出标记
2. 为了让同一个头文件既能用于编译库本身（导出），又能用于其他项目使用这个库（导入），就需要这种条件编译机制

这时就可以使用CMake提供的 **`GenerateExportHeader`** 模块来自动处理这个问题。

此外，为了方便别人`find_package()`自己的库，在导出时我需要添加导出配置，具体操作参考[此链接](https://cmake.org/cmake/help/v3.30/guide/tutorial/Adding%20Export%20Configuration.html)。

## 参考资料

1. [CMake官方教程](https://cmake.org/cmake/help/v3.30/guide/tutorial/index.html)


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/6c7e777/  


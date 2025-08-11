# C&#43;&#43;学习笔记

## 前言

由于笔者具备一定的C语言基础，所以在本文中对于C语言的语法不再会进行赘述，主要专注与C&#43;&#43;语法。为了更好的学习C&#43;&#43;，笔者找了一些题目来进行练习，所有题目与题解均可在[此仓库](https://github.com/YouZhiZheng/AUT1400-2)找到。

本学习笔记适用于 **「具备一定C语言基础，想要使用C&#43;&#43;来编写代码」**  的读者。

**本文阅读指南：**

1. 在看过示例代码后，一定要编写一个自己的示例代码 或 自己动手重写一遍。
2. 标红的注意内容一定要仔细看！！！
3. 一定要做上面仓库中的题！！！一定要做上面仓库中的题！！！一定要做上面仓库中的题！！！

**好的命名能够提高代码的可读性，本文从类章节开始采用以下命名规范：**

1. 局部变量名单词之间使用下划线隔开；
2. 类的变量成员用下划线作为前缀如 _file_name;
3. 类的函数名使用驼峰类型；如doSomething();
4. 类的成员存取使用 如get_file_name() set_file_name()；
5. 类名是PASCAL风格，即首字母大写 如MyClass；
6. 常量用k作为前缀后面是PASCAL风格如 kFileName;
7. 全局变量用g作为前缀后面是PASCAL风格如 gFileName;
8. 宏定义全大写，中间用下划线隔开 FILE_NAME。

## 从C到C&#43;&#43;

本章节主要介绍一些C&#43;&#43;的比较重要特性，例如如何申请和释放内存空间、bool和string类型、命名空间等。这些新特性可以给我们编程提供遍历，提高开发效率。

### 布尔类型(bool)

在C语言中，没有&#34;真&#34;与&#34;假&#34;的数据类型，我们通常使用一个**整形变量**的值来表示真假，其值为 1 表示真，为 0 表示假。以下为一个例子：

```cpp
int IsOddNum(int n)  //判断一个数是否是奇数
{
  int flag;
  if(n % 2 == 0) flag = 0;
  else flag = 1;

  return flag;
}
```

然而，这种做法有几个缺点：

1. **可读性差：** 使用整数来表示布尔值可能会让代码的可读性降低，因为读者需要记住0和非0值的含义。
2. **存在类型安全问题：** 整数可以进行算术运算，这可能导致意外的类型转换和错误。
3. **语义不明确：** 整数类型的使用没有明确表达出变量的布尔语义。

所以，C&#43;&#43;提供了 **`bool`** 类型来表示真假，该类型的变量只有 `true` 和 `false` 两种取值。那么上面的例子则可改为：

```cpp
bool IsOddNum(int n)
{
  bool flag;
  if(n % 2 == 0) flag = true;
  else flag = false;

  return flag;
}
```

### 输入输出

在C&#43;&#43;中，使用输入输出时，需先包含头文件 **`iostream`**。使用 `cin`从标准输入设备（通常是键盘）读取数据，使用 `cout` 向标准输出设备（通常是屏幕）发送数据，使用 `cerr`向标准错误设备（通常是屏幕）发送错误信息。

`cin`要配合 `&gt;&gt;`运算符使用，`cout`和`cerr`要配合`&lt;&lt;`运算符使用。
&gt;`cerr` 默认情况下是**无缓冲**的，这意味着发送到 `cerr` 的输出会**立即**显示在标准错误输出（通常是控制台），在程序发生严重错误时，也能保证错误信息的及时显示。而 `cout` 的输出会被缓存直到**缓冲区满**或者**遇到 `endl`** 时才刷新输出。

**PS：**`cin`、`cout`、`cerr` 都是C&#43;&#43;的内置对象，不是 C&#43;&#43; 中的关键字，其本质是函数调用，采用**运算符重载**来实现的(后面会讲解)。

一个简单示例如下：

```cpp
#include&lt;iostream&gt;
using namespace std; //使用标准命名空间

int main()
{
    int n;
    float a, b, c;
    cout &lt;&lt; &#34;Please enter an integer&#34; &lt;&lt; endl; //endl 等价于C语言中的 \n 符号
    cin &gt;&gt; n;
    cout &lt;&lt; &#34;The integer you entered is &#34; &lt;&lt; n &lt;&lt; endl;

    cout &lt;&lt; &#34;Please enter three floating point numbers&#34; &lt;&lt; endl;
    cin &gt;&gt; a &gt;&gt; b &gt;&gt; c;
    cout &lt;&lt; &#34;The three floating point numbers you entered are &#34; &lt;&lt; a &lt;&lt; &#34;,&#34;&lt;&lt; b &lt;&lt; &#34;,&#34; &lt;&lt; c &lt;&lt; endl;
    return 0;
}
```

`cin` 可以连续的从键盘读取数据，以空格、制表符、换行符作为分隔符(按下的回车键会被转换为换行符存入缓冲区)，当 `cin` 遇到这些分隔符时，它会停止为**当前变量**读取数据。

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;cin停止读入数据的几种情况&lt;/summary&gt;
  
  1. **遇到空白字符：** 默认情况下，cin 使用空白字符（如空格、制表符 \t、换行符 \n）作为字段分隔符。当 cin 遇到这些字符时，它会停止为当前变量读入数据。
  2. **输入与类型不匹配：** 当尝试将输入的字符串转换为变量类型失败时，cin 会停止向该变量读入数据。例如，如果输入包含非数字字符而程序试图将其读入一个整数变量，cin 将停止并设置错误标志。
  3. **达到输入流的末尾：** 如果输入来源（如键盘输入或文件）已经结束（EOF），cin 将停止读入数据。
  4. **手动清空输入缓冲区：** 通过调用 `cin.ignore()` 方法，可以忽略输入缓冲区中的字符直到遇到指定的分隔符或者达到忽略的字符数上限。
  5. **使用 std::getline：** 如果使用 std::getline(std::cin, str) 来读取一行文本，cin 会在遇到换行符之前读取所有字符，并将它们存储在提供的字符串变量中。遇到换行符后，cin 停止读入并丢弃换行符。
  6. **设置 cin 的错误状态：** 如果 cin 遇到一个它无法解析的输入（例如，输入的数据类型不匹配），它会设置错误状态。如果错误状态被设置，cin 将停止进一步的输入操作，直到错误状态被清除。
  7. **流的同步操作：** 在某些情况下，如果 cin 与 cout 同步（cin.tie() 返回 &amp;cout），cout 的刷新操作（如使用 std::endl 或 cout.flush()）可能会影响 cin 的行为。
  8. **外部因素：** 例如，如果从文件中读取，文件的实际结束或读取操作被外部程序或操作系统中断。

&lt;/details&gt;

`cerr`的使用方法与`cout`一致，只不过其通常用于输出错误信息。想要更详细的学习这三个对象，可浏览[此网站](https://segmentfault.com/a/1190000023902592)。

### 命名空间

C&#43;&#43;语言引入命名空间这一概念主要是为了避免命名冲突，其关键字为 **`namespace`**。

例如，两个不同的库可能都有名为 log 的函数，如果不加以区分，就会产生冲突。命名空间允许每个库将 `log` 函数放在自己的命名空间中，如 `myLib::log` 和 `yourLib::log`，这样就可以同时使用这两个函数而不会发生冲突。代码如下：

```cpp
// myLib.h
#ifndef MYLIB_H
#define MYLIB_H

namespace myLib {
    void log(const std::string&amp; message);
}

#endif // MYLIB_H

// myLib.cpp
namespace myLib {
    void log(const std::string&amp; message) {
        // 实现 myLib 库的日志功能
        std::cout &lt;&lt; &#34;MyLib: &#34; &lt;&lt; message &lt;&lt; std::endl;
    }
}

/* ---------------------------------------分割线--------------------------------------- */

// yourLib.h
#ifndef YOURLIB_H
#define YOURLIB_H

namespace yourLib {
    void log(const std::string&amp; message);
}

#endif // YOURLIB_H

// yourLib.cpp
namespace yourLib {
    void log(const std::string&amp; message) {
        // 实现 yourLib 库的日志功能
        std::cout &lt;&lt; &#34;YourLib: &#34; &lt;&lt; message &lt;&lt; std::endl;
    }
}
```

当你想要在主程序或其他代码中使用这两个库的 `log` 函数时，你可以按照 **`命名空间名称::Name`** 的格式来引用它们：

```cpp
#include &#34;myLib.h&#34;
#include &#34;yourLib.h&#34;

int main() {
    myLib::log(&#34;This is a log message from myLib.&#34;);
    yourLib::log(&#34;This is a log message from yourLib.&#34;);
    return 0;
}
```

除了 **使用域解析符`::`**，还可以使用 `using namespace 命名空间名称` 的方式来引用它们：

```cpp
#include &#34;myLib.h&#34;
#include &#34;yourLib.h&#34;

using namespace myLib;

int main() {
    log(&#34;This is a log message from myLib.&#34;); //使用的是myLib的log函数
    yourLib::log(&#34;This is a log message from yourLib.&#34;);
    return 0;
}
```

**PS：** 在头文件中，应避免使用 `using namespace XXX`，因为头文件会被其他文件所包含，这就会导致XXX命名空间被多个文件包含。对于某些程序来说，由于不经意间包含了一些名字，反而可能产生始料未及的名字冲突。

### 引用

引用可以看做是「被引用对象的存储空间的别名」，**在声明引用时，必须同时对其进行初始化**。引用的声明方法和示例如下：  

```cpp
// 格式为：类型 &amp;变量名 = 被引用对象;

#include&lt;iostream&gt;
using namespace std;

int main()
{
    int a = 7;
    int &amp;b = a;
    cout &lt;&lt; &#34;a value is: &#34; &lt;&lt; a &lt;&lt; endl;
    cout &lt;&lt; &#34;b value is: &#34; &lt;&lt; b &lt;&lt; endl;
    cout &lt;&lt; &#34;a addr = &#34; &lt;&lt; &amp;a &lt;&lt; &#34;, b addr = &#34; &lt;&lt; &amp;b &lt;&lt; &#34;.&#34; &lt;&lt; endl;
    return 0;
}

/**   运行结果如下：
 *     a value is: 7
 *     b value is: 7
 *     a addr = 0x61fe14, b addr = 0x61fe14.
 */
```

从这段程序中我们可以看出，变量 `a` 和 `b` 的地址相同，即地址为 `0x61fe14` 的存储空间拥有两个名字：`a` 和 `b`。变量 `a` 和 `b` 均可访问和修改该存储空间的存储的值。如果不想让引用变量修改值，可使用 **`const`** 关键字。

#### 函数引用参数

引用变量经常被用作函数的参数（尤其是参数为较大的结构体、对象时），这使得可以**快速传递参数**且能在被调用的函数中修改调用函数中的变量（与指针效果类似）。示例如下：

```cpp
#include&lt;iostream&gt;
using namespace std;

void swap(int &amp;a, int &amp;b);

int main()
{
    int num1 = 10;
    int num2 = 20;
    cout &lt;&lt; num1 &lt;&lt; &#34; &#34;&lt;&lt; num2 &lt;&lt; endl;
    swap(num1, num2);
    cout &lt;&lt; num1 &lt;&lt; &#34; &#34; &lt;&lt; num2 &lt;&lt; endl;
    return 0;
}

void swap(int &amp;a, int &amp;b)
{
    int temp = a;
    a = b;
    b = temp;
}
```

#### 函数引用返回值

函数的返回值也可以是引用。普通的传值返回，是将运算结果拷贝到一个**临时存储空间**，再从该临时存储空间拷贝给对应变量；当我们将函数返回值声明为引用时，会直接将运算结果拷贝给对应变量，不经过临时存储空间。

但需注意函数返回的引用**不能是函数体内的临时变量**。因为函数运行完，其申请的存储空间就会被销毁，这时我们还未进行数据的拷贝，赋值操作就不能正确执行。

```cpp
#include&lt;iostream&gt;
using namespace std;

int &amp; valplus1(int &amp;a);
int &amp; valplus2(int c);

int main()
{
    int num1 = 10;
    int num2 = 7;
    int num3;
    num3 = valplus1(num1);  // 能够正确赋值，且不经过临时存储空间
    cout&lt;&lt; num1 &lt;&lt; &#34; &#34; &lt;&lt; num3 &lt;&lt;endl;

    num3 = valplus2(num2); // 不能正确赋值，因为拷贝数据前存储空间已经被销毁
    cout&lt;&lt; num2 &lt;&lt; &#34; &#34; &lt;&lt; num3 &lt;&lt;endl;
    return 0;
}

int &amp; valplus1(int &amp;n)
{
    n &#43;=  5;
    return n;
}

int &amp; valplus2(int n)
{
    int t = n &#43; 5;
    return t;
}
```

#### 何时使用引用参数的一些指导原则

* 如果参数是**数组或基本数据类型**，则使用**指针**
* 如果参数是**类对象或结构变量**，则使用**引用**

### 常量与只读变量&lt;a id=&#34;chapter-1&#34;&gt;&lt;/a&gt;

常量在学习C语言时，就已接触，这里再进行简单的回顾一下，**常量（Constant）** 是指那些「在编译期间就能确定的值，且在运行期间这个确定的值不会发生变化」。

```cpp
42;    // 这是 int 型
42u;   // 这是 unsigned int 型
42U;   // 这也是 unsigned int 型
42l;   // 这是 long 型
42L;   // 这也是 long 型
42ll;  // 这是 long long 型
42ul;  // 这是 unsigned long 型
42ull; // 这是 unsigned long long 型
3.14f; // 这是 float 型
1e7L;  // 这是 long double 型
&#39;A&#39;; // 这是 char 型
&#34;abc&#34;; // 这是 string 型
true; // 这是 bool 型
1 &#43; 1; // 这也是常量

// 注：字面量不包括分号;，此处加上分号只为演示作用。
// 字面量（Literal）是指在 C&#43;&#43; 代码中，它的写法能直接体现它所表达的值的常量。
```

___

只读变量指的是「一旦被初始化赋值之后，其值就不能被更改的变量」。如何声明并定义一个变量为只读的？很简单，只需在类型说明符前加上 `const` 关键词修饰即可。比如：

```cpp
const int a = 37;
```

这样 a 就成为了一个 int 类型的，只读的变量。从此以后 a 的值不能发生变化，如以下行为都会导致编译错误：

```cpp
cin &gt;&gt; a; // 编译错误：无法向 a 中输入，因为 a 无法发生变化
a = 56;   // 编译错误：无法为 a 赋值
```

**PS：** 只读变量必须在定义时就完成初始化。

#### 常量与只读变量的关系

你可能已经注意到，只读变量和常量都有一个共同的特点，就是“**无法在运行期间更改它的值”**。那么能否说只读变量就是常量呢？

答案是否定的。请看下面这个例子：

```cpp
#include &lt;iostream&gt;
using namespace std;

int main()
{
    int a{0};
    cin &gt;&gt; a;
    const int b{a};
    // b = 42;     // 编译错误
}
```

第 8 行声明了一个只读变量 `b` ，因此不能在第 9 行通过赋值更改它的值。但是你也注意到，程序在编译期间是无法得知 `b` 的值是多少的。因为 `b` 是用 `a` 初始化的，但是 `a` 的值则是在第 7 行由输入提供的。所以 `b` 的值只能在运行期间确定，无法在编译期间得知； **`b` 不满足常量的定义**。

上面这个例子表示，**并非所有的只读变量都是常量**。那么什么时候只读变量可以是常量呢？条件也很简单：**只有使用常量作为初始化值初始化的只读变量才是常量**。比如：

```cpp
#include &lt;iostream&gt;
using namespace std;

int main() {
    const int a{42}; 
    const int b{a};
    const int c{a &#43; b}; 
    // 以上三个只读变量均是常量
}
```

`a` 是由 42 初始化的， 42 是常量，`a`的值在编译时期就能确定，所以 `a` 就是一个常量。同理，只读变量 `b` 由 `a` 初始化， `a` 已经是一个常量了，那么 `b` 因而也是一个常量。再来看 `c` ， `a` 和 `b` 已经是常量了，那么由常量组成的表达式也是常量；故 `a &#43; b` 也是常量。所以只读变量 `c` 也是常量。

你会发现判断一个只读变量是否是常量这件事情并不容易，尤其在更大的程序里。是不是常量这件事情有时候会显得很重要（比如将来会学的数组长度，以及模板泛型编程的时候常量与否也很关键）。因此 C&#43;&#43; 提供了一个关键字用于常量： **`constexpr`** 。

当 `constexpr` 出现在声明语句的时候，指明这个声明引入的变量是一个常量。如果不是常量的话，会导致编译错误。例如：

```cpp
#include &lt;iostream&gt;
using namespace std;

int main() {
    constexpr int a{42}; // a 是常量（当然常量必然是只读的。）
    int b;               // b 既不是只读变量，也不是常量
    const int c{b};      // c 不是常量，但它是只读变量
 // constexpr int d{b};  // 编译错误，因为要求 d 是常量，但 d 未用常量初始化
    constexpr int e{a};  // OK, e 是常量，由常量初始化
}
```

对于变量来说，关键字 `constexpr` 蕴含了 `const` 。（因为常量必然是只读的：运行时值不会发生更改。）因此在需要常量的场合，建议用 `constexpr` 代替 `const` ，避免意料之外的错误。

#### 顶层 `const` 和 底层 `const`

**顶层`const`：** 作用于变量或指针**本身**的`const`，即变量或指针的值不能修改。  
**底层`const`：** 作用于指针指向的**内容或引用的对象**的`const`，通过指针或引用不能修改那个对象。

&gt;对于**引用**来说，只存在**底层`const`**，因为引用本身的值在初始化就定了，并不能改变，所以顶层const对于引用来说没有意义。

```cpp
// 这里的 const 是底层 const，指针所指向的内容（int类型的值）不能通过 ptr 修改
void func(const int *ptr) 
{ 
    // *ptr = 100; // 错误: 不能通过 const 指针修改值
}

// x 是对一个 const int 的引用，即底层 const
void print(const int &amp;x) 
{ 
    // x = 100; // 错误: 不能修改 const 引用绑定的对象
}

// 这里的 const 是顶层 const，指针的值（一个存储int类型的空间地址）不能修改
void func(int * const ptr) 
{ 
    ......
}

//顶层const，a的值不能被修改
void func(const int a) 
{ 
    ......
}

//左边的const为底层const，右边的const为顶层const
void func(const int * const ptr) 
{ 
    ......
}
```

### 函数与const

上一小节介绍了`const`搭配变量的使用，其实`const`也能配合函数来使用，主要有三种用法：

```cpp
// 1. const参数，防止对应实参被修改
void Func(const int &amp;a, const int *b, int c) // 
{
    a = 1; // 错误，不能修改 a 的值
    *b = 2; // 错误，不能修改 *b 的值，但可修改 b 的值
}

// 2. const成员函数（即类中的函数），表明该函数不会修改任何成员变量
// class是定义类的关键字，public和private是定义访问权限的关键字，后面都会详细讲解
class MyClass
{
public:
    void set_value(int value) { _value = value; }

    // get_value()函数不会修改任何成员变量，如果修改了会报错。
    int get_value() const { return _value; }
    
    /**
     *  使用 const 修饰的成员函数就一定要确保其不会修改成员变量。所以，如果一个 const 成员函数调用了另一个
     *  成员函数，也要确保调用的成员函数也是被 const 修饰的。
     *  即const成员函数只能调用其他const成员函数，否则会报错。
     *  例如，如果 get_value() 函数不是const成员函数，那么 show() 是不能调用它的，强行调用是不能通过编译的。
     */
    void show() const { cout &lt;&lt; get_value() &lt;&lt; endl; }
private:
    int _value;
};

// 3.const返回值，防止调用者通过返回的引用或指针修改原始对象。
const MyClass&amp; getConstObject() const 
{
    return obj;
}
```

### 默认参数

默认参数指的是当调用函数时某些实参被省略，形参自动使用的一个值。直接看例子：

```cpp
#include &lt;iostream&gt;
using namespace std;

void Func(int a, int b, int c = 1) // Func函数的形参c就有默认参数1
{
    cout &lt;&lt; a &lt;&lt; &#34; &#34; &lt;&lt; b &lt;&lt; &#34; &#34; &lt;&lt; c &lt;&lt; endl;
}

int main()
{
    Func(1, 2, 3); // 正确调用，输出 1 2 3
    Func(1, 2); // 正确调用，输出 1 2 1

    return 0;
}
```

**注意：** &lt;span style=&#34;color: red;&#34;&gt;当某个参数需要指定默认值时，其右边的参数都需要指定默认参数&lt;/span&gt;

```cpp
// 错误定义，如果b要定义默认参数，则c也要定义默认参数
int Func(int a, int b = 1, int c) 
{
    ......
    return a;
}
```

### 函数重载

函数重载也叫函数多态，指的是在**同一作用域内有多个同名的函数**，它们完成类似的工作，但使用**不同**的参数列表。

函数重载的关键是**函数参数列表**——也称为**函数特征标**。如果两个函数的参数数量、参数类型、参数顺序都相同，则它们的特征标相同，在同一作用域中是不被允许的。

例如，可以定义一组原型如下的 **`print()`** 函数：

```cpp
#include &lt;iostream&gt;
using namespace std;

// 以下 print 函数形成重载
void print(const char *str, int width); // 声明 #1
void print(double d, int width);    // 声明 #2
void print(long l, int width);  // 声明 #3
void print(int i, int width);   // 声明 #4
void print(const char *str);    // 声明 #5

int main()
{
    print(&#34;Pancakes&#34;, 15);  // 调用第1个print
    print(1999.0, 10);  // 调用第2个print
    print(1999L, 15);   // 调用第3个print
    print(1999, 12);    // 调用第4个print
    print(&#34;syrup&#34;); // 调用第5个print

    return 0;
}

// 5个print函数的具体实现省略
....
```

### 基于范围的 for 循环

该语法是C&#43;&#43;11 引入的一种新的 `for` 循环语法，它提供了一种简洁且易于阅读的方式来遍历 **数组、容器**（如 std::vector、std::list 等）和其他可迭代对象。

直接看以下示例就可明白如何使用：

```cpp
// 遍历数组
int array[] = {1, 2, 3, 4, 5};
for (int num : array) {
    std::cout &lt;&lt; num &lt;&lt; &#34; &#34;;
}
// 输出：1 2 3 4 5

/* ---------------------------------分割线--------------------------------- */

// 遍历 std::vector
#include &lt;vector&gt;
#include &lt;string&gt;
#include &lt;iostream&gt;

std::vector&lt;std::string&gt; vec = {&#34;Apple&#34;, &#34;Banana&#34;, &#34;Cherry&#34;};
for (const std::string&amp; fruit : vec) {
    std::cout &lt;&lt; fruit &lt;&lt; &#34; &#34;;
}
// 输出：Apple Banana Cherry

/* ---------------------------------分割线--------------------------------- */

// 遍历字符串
std::string str = &#34;Hello&#34;;
for (char c : str) {
    std::cout &lt;&lt; c &lt;&lt; &#34; &#34;;
}
// 输出：H e l l o

/* ---------------------------------分割线--------------------------------- */

// 修改数组中的值
int array[] = {1, 2, 3, 4, 5};
for (int &amp;num : array) {
    num&#43;&#43;;
}
// 数组中的值变为：2 3 4 5 6
```

### 内联函数

内联函数是C&#43;&#43;为了 **提高程序运行速度** 所做的一项改进，内联函数会直接在调用处将调用的语句替换为函数体，而不必像常规函数一样，需要先跳转到函数地址，执行完函数后再跳转回调用处。如下图所示：  
![图1](/PostsImgs/Cplusplus_learning_note_imgs/picture1.png)

在程序设计过程中，我们通常会将一些 **频繁被调用的短小函数** 声明为内联函数。使用 **`inline`** 关键字来实现，但这只是建议编译器将该函数定义为内联函数，**编译器不一定会接受此建议**，它可能认为该函数过大或注意到函数调用了自己，因此不将其作为内联函数。

为了使得 **`inline`** 声明内联函数有效，我们必须**将其与函数体放在一起才行**，否则是不能成功将函数声明内联函数的，如下例所示：

```cpp
inline void swap(int &amp;a, int &amp;b);  // 将 inline 放在函数声明处不会起作用
void swap(int &amp;a, int &amp;b)
{
    int temp = a;
    a = b;
    b = temp;
}

/* ---------------------------------分割线--------------------------------- */

void swap(int &amp;a, int &amp;b);
inline void swap(int &amp;a, int &amp;b) // 成功将 swap 函数声明为内联函数
{
    int temp = a;
    a = b;
    b = temp;
}
```

**PS：** 学完这一小节我们应该了解到：&lt;span style=&#34;color: red;&#34;&gt;**应该将那些频繁使用且短小(一般少于10行)的函数声明为内联函数（虽然编译器不一定会将其认定为内联函数），且应将 inline 放在函数体前才会起作用**。&lt;/span&gt;

### 内存空间的申请

在 C语言中，动态分配和释放内存的函数是 `malloc`、`calloc` 和 `free`，而在 C&#43;&#43;语言中，通常使用`new`、`new[]`、`delete` 和 `delete[]` **操作符**来动态地分配内存和释放内存。  
&gt;`new`、`new[]`、`delete` 和 `delete[]` 均是C&#43;&#43;中的关键字，而非函数！！！

`new` 用于动态分配单个空间，`new []` 用于动态分配数组空间。`delete` 用于释放分配的单个空间，`delete[]` 用于释放分配的数组空间。

```cpp
int *p = new int; // 申请了一个int类型的空间，等价于 malloc(sizeof(int));
int *A = new int[10]; // 申请了一个数组空间，大小为10，用于存在int类型的值，等价于 malloc(sizeof(int) * 10);

delete p; // 释放单个的空间
delete[] A; //释放数组空间
```

**为了避免内存泄露，`new` 和 `delete`、`new[]` 和 `delete[]` 操作符应该成对出现**，并且不要将这些操作符与 C语言中动态分配内存和释放内存的几个函数一起混用。

### 命令行处理技术

在C&#43;&#43;中，命令行处理是一项基本但非常重要的技术，特别是开发命令行工具和应用程序时。处理命令行参数可以通过标准的 **`main`** 函数参数或更高级的库来实现。下面介绍一种比较常用的方法：

**使用`main`函数的参数**  
C&#43;&#43;程序的入口点是 **`main`** 函数，它可以接受两个参数：  
`int main(int argc, char* argv[])`

* **`argc`**（argument count）：表示命令行参数的数量，包括命令本身。
* **`argv`**（argument vector）：一个字符指针数组，包含命令行参数的实际值。

例如，对于命令 **`wc report1 report2 report3`**， `argc`为4，`argv[0]`为`wc`，`argv[1]`为`report1`，依次类推。

### 强制类型转换（选读）

在 C&#43;&#43;中有四个关键字用于强制类型转换： `static_cast`、`const_cast`、`reinterpret_cast` 和 `dynamic_cast`。它们相比C语言中的传统转换方式（使用括号表示的类型转换）提供了更为精细和安全的类型转换机制。

#### `static_cast`

该关键字用于**相关类型之间**进行转换，如整型与浮点型，指针类型等。它执行的是**编译时类型检查，不会做运行时的类型检查**。它的工作原理是在编译时，编译器利用已知的类型信息来进行类型兼容性检查并执行转换。所以如果类型之间的转换是不安全的或不允许的，编译器在编译时会给出错误。使用方法如下：  
**`static_cast&lt;想要转换为的类型&gt;(变量或表达式)`**

```cpp
// 基本数据类型转换
int i = 10;
float f = static_cast&lt;float&gt;(i);  // 将int转换为float

/* ---------------------------------分割线--------------------------------- */

// 指针类型转换
// 对于基本数据类型的指针 static_cast 不能在两个「具体类型」的指针之间进行转换，只能
// 将一个指针转换为 void * 类型，或将 void * 类型的指针转换为其原本的数据类型
int i = 5;
void* ptr = static_cast&lt;void *&gt;(&amp;i); // 将 int * 类型的指针转换为 void * 类型
int* intPtr = static_cast&lt;int*&gt;(ptr); // void指针转换回int指针
// float *floatPtr = static_cast&lt;float*&gt;(&amp;i)  // 这种转换是不被允许的

class Base {};
class Derived : public Base {};
Derived d;
Base* basePtr = static_cast&lt;Base*&gt;(&amp;d); // 派生类指针转换为基类指针

/* ---------------------------------分割线--------------------------------- */

// 类型向上的安全类型转换。
// 了解即可，后面会介绍类的相关知识
class Base {
public:
    virtual ~Base() {}
};

class Derived : public Base {
public:
    void func() {
        // ...
    }
};

void function(Base&amp; baseRef) {
    Derived&amp; derivedRef = static_cast&lt;Derived&amp;&gt;(baseRef); //将派生类 Derived 类型的引用转换为基类Base类型的引用
    derivedRef.func(); // 使用 Derived 类型的引用调用 Derived 的成员函数
}
```

**PS：** 一定要注意 `static_cast` 是不能用于两个具体类型指针之间的转换的，因为它们指向的数据类型在内存中的存储方式和大小不同，直接转换指针类型可能会导致未定义行为。

___

#### `dynamic_cast`

在这里认识该关键字即可，后面学完类的知识再仔细理解。

该关键字主要用于处理**含有继承关系的类之间的安全向下转型**，即从基类指针或引用转换为派生类指针或引用。它在运行时检查转换的安全性，所以具有较高的性能开销。其利用了 C&#43;&#43; 的多态性和运行时类型识别（RTTI）机制来确保转换的安全性。**`dynamic_cast` 只能用于含有虚函数的类或其派生类**，因为`RTTI` 需要虚函数表来确定对象的动态类型。

```cpp
class Base {
public:
    virtual ~Base() {} // 虚析构函数，确保类是多态的
};

class Derived : public Base {
    // 派生类内容
};

int main() {
    Derived d;
    Base* b = &amp;d;

    // 向下转型尝试
    Derived* d2 = dynamic_cast&lt;Derived*&gt;(b);
    if (d2) {
        // 转换成功，d2 是一个 Derived 类型的指针
    } else {
        // 转换失败，这在本例中不会发生，因为 b 确实指向 Derived 对象
    }

    // 错误的向下转型尝试
    Base b2;
    Derived* d3 = dynamic_cast&lt;Derived*&gt;(&amp;b2);
    if (!d3) {
        // 转换失败，b2 是 Base 类型的实例，而不是 Derived
    }

    return 0;
}
```

___

#### `const_cast`

该关键字用于去除指向常数对象的**指针或引用**的常量性。

**PS：** 这里只会简单的介绍一下，了解该关键字的作用即可。因为使用 `const_cast` 去掉指针或引用的常量性并且去修改原始变量的数值，是一种非常不好的行为，应该在程序中避免这种情况。

```cpp
#include&lt;iostream&gt;
using namespace std;

int main()
{
    const int a = 10;
    const int * p = &amp;a;
    int *q;
    q = const_cast&lt;int *&gt;(p);
    *q = 20;    //fine
    cout &lt;&lt; a &lt;&lt; &#34; &#34; &lt;&lt; *p &lt;&lt; &#34; &#34; &lt;&lt; *q &lt;&lt; endl;
    cout &lt;&lt; &amp;a &lt;&lt; &#34; &#34; &lt;&lt; p &lt;&lt; &#34; &#34; &lt;&lt; q &lt;&lt;endl;
    return 0;
}
```

输出结果如下：

```bash
10 20 20
002CFAF4 002CFAF4 002CFAF4
```

查看运行结果，问题来了，指针 `p` 和指针 `q` 都是指向 `a` 变量的，且经过调试发现 `002CFAF4` 地址内的值确实由 10 被修改成了 20，这是怎么一回事呢？为什么 a 的值打印出来还是 10 呢？

其实这是一件好事，我们要庆幸 `a` 变量最终的值没有变成 20！变量 `a` 一开始就被声明为一个**常量变量**，不管后面的程序怎么处理，**它就是一个常量**，就是不会变化的。试想一下如果这个变量 `a` 最终变成了 20 会有什么后果呢？对于这些简短的程序而言，如果最后 `a` 变成了 20，我们会一眼看出是 `q` 指针修改了，但是一旦一个项目工程非常庞大的时候，在程序某个地方出现了一个 `q` 这样的指针，它可以修改常量 `a`，这是一件很可怕的事情的，可以说是一个程序的漏洞，毕竟将变量 `a` 声明为常量就是不希望修改它，如果后面能修改，这就太恐怖了。

___

#### `reinterpret_cast`

该关键字可以在几乎任何类型的指针之间进行转换，甚至可以在指针和足够大的整数类型之间进行转换。这种转换不会尝试保留对象的值，而是简单地重新解释位模式，很容易导致错误的结果。由于这种转换的不安全性，只有在**确实必要且你非常清楚自己在做什么的情况下，才应该使用它**。

在 C&#43;&#43;中，该关键字主要有三种强制转换用途：改变指针或引用的类型、将指针或引用转换为一个足够长度的整形、将整型转换为指针或引用类型。

```cpp
    // 将整型指针转换为双精度浮点型指针
    int *a = new int;
    double *d = reinterpret_cast&lt;double *&gt;(a);

    int i = 100;
    int* p = &amp;i;

    cout &lt;&lt; &#34;p value: &#34; &lt;&lt; p &lt;&lt; endl;
    // 将整数地址转换为整数
    intptr_t address_as_int = reinterpret_cast&lt;intptr_t&gt;(p);
    cout &lt;&lt; &#34;address_as_int: &#34; &lt;&lt; address_as_int &lt;&lt; endl ;

    // 将整数重新转换回指针
    int* p2 = reinterpret_cast&lt;int*&gt;(address_as_int);
    cout &lt;&lt; &#34;p2 value: &#34; &lt;&lt; p2 &lt;&lt; endl;
```

### 异常处理（选读）

&lt;span style=&#34;color: red;&#34;&gt;异常规范是 C&#43;&#43;98 新增的一项功能，但是后来的 C&#43;&#43;11 已经将它抛弃了(因为其很难实现)，不再建议使用。&lt;/span&gt;所以，下面的内容了解一下即可(不看也行)。

在 C&#43;&#43; 中，一**个函数能够检测出异常并且将异常返回**，这种机制称为**抛出异常**。当抛出异常后，函数调用者捕获到该异常，并对该异常进行处理，我们称之为**异常捕获**。

C&#43;&#43; 新增 `throw` 关键字用于抛出异常，新增 `catch` 关键字用于捕获异常，新增 `try` 关键字尝试捕获异常。通常将可能会出现异常的语句放在`try{ }`程序块中，而将异常处理语句置于`catch{ }`语句块中。其基本语法如下：

```cpp
try
{
    //可能抛出异常的语句
}
catch (异常类型1)
{
    //异常类型1的处理程序
}
catch (异常类型2)
{
    //异常类型2的处理程序
}
// ……
catch (异常类型n)
{
    //异常类型n的处理程序
}
```

由 `try` 程序块捕获 `throw` 抛出的异常，然后依据异常类型运行对应 `catch` 程序块中的异常处理程。`catch` 程序块**顺序可以是任意的，不过均需要放在 `try` 程序块之后**。一个具体示例如下：

```cpp
#include&lt;iostream&gt;
using namespace std;

enum index{underflow, overflow};

int array_index(int *A, int n, int index);

int main()
{
    int *A = new int[10];
    for(int i=0; i &lt; 10;  i&#43;&#43;) A[i] = i;

    try
    {
        cout&lt;&lt;array_index(A,10,5)&lt;&lt;endl;
        cout&lt;&lt;array_index(A,10,-1)&lt;&lt;endl;
        cout&lt;&lt;array_index(A,10,15)&lt;&lt;endl;
    }
    catch(index e)
    {
        if(e == underflow)
        {
            cout&lt;&lt;&#34;index underflow!&#34;&lt;&lt;endl;
            exit(-1);
        }
        if(e == overflow)
        {
            cout&lt;&lt;&#34;index overflow!&#34;&lt;&lt;endl;
            exit(-1);
        }
    }

    return 0;
}

int array_index(int *A, int n, int index)
{
    if(index &lt; 0) throw underflow;  // 抛出下溢出异常
    if(index &gt; n-1) throw overflow; // 抛出上溢出异常
    return A[index];
}
```

## String类型

C&#43;&#43;增强了对字符串的支持，除了可以使用 C 语言风格的字符串，还可以使用 **`string`类** 处理字符串，且后者处理起字符串来更加方便。使用 `string` 数据类型需要包含头文件 **`&lt;string&gt;`**。

**PS：** string类型的变量本质是一个**对象**，其包含的字符串不存在C语言中的字符串结束符 **`\0`**，因为其内部通过维护一个长度计数器来获取字符串的长度，并不依赖 `\0`。

string类型变量可直接调用类中的 **`size()`** 或 **`length()`** 函数来获取字符串长度。

```cpp
string s = &#34;mystring&#34;;
int len = s.length();  // len 的值为 8
```

### string类型变量定义和转换

string类型变量常用的定义方法有：

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;
using namespace std;

int main()
{
    char c_char_array[] = &#34;Hello, World!&#34;;

    //  只定义不初始化，编译器会使用默认值（空字符串）进行赋值
    string s1;

    // 定义时直接进行初始化
    string s2 = &#34;mystring&#34;;

    // 定义时进行复制初始化，s3的内容和s2一样
    string s3 = s2;

    // 定义时使用指定字符和大小来初始化，s4 的内容为 sssssss
    string s4 (7, &#39;s&#39;);

    // 定义时使用字符数组来进行初始化，s5的内容为 Hello, World!
    string s5(c_char_array);

    return 0;
}
```

___

虽然 C&#43;&#43; 提供了 `string` 类型来替代 C 语言中的 `char*` 字符串，但程序设计过程中还是不可避免地会碰到用 `char*` 字符串的地方。为此，string类提供了一个**转换函数 `c_str()`**，该函数会返回一个**只读的字符指针**(`const char *`)，指向的内容与string对象包含的字符串相同，且以 **`\0`** 结尾。

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;

int main() 
{
    std::string str = &#34;Hello, World!&#34;;
    const char* cstr = str.c_str();  // 进行转换
    std::cout &lt;&lt; cstr &lt;&lt; std::endl; // 输出: Hello, World!
    
    return 0;
}
```

### string类型变量输入输出

在 C&#43;&#43; 中，对于C风格的字符串有三种输入方法；而对于 string 对象，只有两种输入方式：

```cpp
// C风格的字符串
char info[100];

cin &gt;&gt; info; //第一种
cin.getline(info, 100); // 第二种，读取一行，会抛弃最后的 &#39;\n&#39;
cin.get(info, 100); // 第三种，读取一行，保留最后的 &#39;\n&#39;

/* ---------------------------------分割线--------------------------------- */

// string对象
string str;

cin &gt;&gt; str; //第一种，读取一个单词，即遇到 空格或换行符就结束读取
getline(cin, str); //第二种，读取一行，即遇到 &#39;\n&#39; 结束读取

```

string对象和字符数组均使用 `cout` 进行输出。

```cpp
#include&lt;iostream&gt;
#include&lt;string&gt;
using namespace std;

int main()
{
    string mystr;
    getline(cin, mystr);

    cout &lt;&lt; mystr &lt;&lt; endl; //进行输出
    return 0;
}

```

### string类型字符串的连接

对于 `string` 类型变量，我们可以直接用 **`&#43;`** 或者 **`&#43;=`** 符号进行字符串的连接（利用了操作符重载，后面会学习）。

用“&#43;”风格字符串进行字符串连接时，操作符左右两边既可以都是 string 字符串，也可以是一个 string 字符串和一个 C 风格的字符串，还可以是一个 string 字符串和一个 char 字符。而用“&#43;=”风格字符串进行字符串连接时，操作符右边既可以是一个 string 字符串，也可以是一个 C 风格字符串或一个 char 字符。

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;

using namespace std;

int main()
{
    string s1, s2, s3;
    s1 = &#34;first&#34;;
    s2 = &#34;second&#34;;
    
    s3 = s1 &#43; s2;
    cout&lt;&lt; s3 &lt;&lt;endl; // 输出：firstsecond

    s2 &#43;= s1;
    cout&lt;&lt; s2 &lt;&lt;endl;  // 输出：secondfirst
    
    s1 &#43;= &#34;third&#34;;
    cout&lt;&lt; s1 &lt;&lt;endl; //输出：firstthird
    
    s1 &#43;= &#39;a&#39;;
    cout&lt;&lt; s1 &lt;&lt;endl; //输出：firstthirda
    
    return 0;
}
```

### 修改字符串

和字符数组一样，`string` 字符串也可以按照下标逐一访问每一个字符，起始下标仍是 **`0`** 。

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;

using namespace std;

int main()
{
    string s = &#34;1234567890&#34;;

    for(int i = 0; i &lt; s.length(); i&#43;&#43;) // 遍历字符串中的每个字符
    {
        cout&lt;&lt; s[i] &lt;&lt;&#34; &#34;;
    }
    // for循环的最终输出结果为 1 2 3 4 5 6 7 8 9 0

    cout &lt;&lt; endl; // 输出换行符

    s[7] = &#39;9&#39;;
    cout &lt;&lt; s &lt;&lt; endl; // 输出结果为1234567990

    return 0;
}
```

除了能逐个访问字符串中每个字符外，`string`类还提供了一些成员函数方便我们操作 `string` 类型变量。

#### erase()成员函数

该函数用于**删除字符串中的子字符串**。有两个参数，第一个参数为子字符串的起始下标，第二个参数为子字符串的长度(不指定此参数，则默认子串为从起始位置开始一直到最后一个字符)。

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;

using namespace std;

int main()
{
    string s1, s2, s3;
    s1 = s2 = s3 = &#34;1234567890&#34;;
   
    s2.erase(5);
    s3.erase(5, 3);
   
    cout &lt;&lt; s1 &lt;&lt; endl; // 输出 1234567890
    cout &lt;&lt; s2 &lt;&lt; endl; // 输出 12345
    cout &lt;&lt; s3 &lt;&lt; endl; // 输出  1234590
    return 0;
}
```

___

#### insert()成员函数

该函数用于**在字符串指定位置插入另一个字符串**。有两个参数，第一个参数表示插入位置的下标，第二个参数为要插入的字符串(可以是string 或 字符数组)。

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;

using namespace std;

int main()
{
    char carry[] = &#34;bbb&#34;;

    string s1, s2;
    s1 = &#34;1234567890&#34;;
    s2 = &#34;aaa&#34;;

    cout &lt;&lt; s1 &lt;&lt; endl; // 输出 1234567890

    s1.insert(5, s2);
    cout &lt;&lt; s1 &lt;&lt; endl; // 输出 12345aaa67890

    s1.insert(5, carry);
    cout &lt;&lt; s1 &lt;&lt; endl; // 12345bbbaaa67890

    return 0;
}
```

___

#### replace()成员函数

该函数用于**使用指定的字符串来替换一个指定的子字符串**。有三个参数，第一个参数为被替换的子字符串的起始下标，第二个参数为被替换的子字符串长度，第三个参数为用来替换的字符串(可以是 string 或 字符数组)。

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;

using namespace std;

int main()
{
    string s1, s2, s3;
    s1 = s2 = &#34;1234567890&#34;;
    s3 = &#34;aaa&#34;;

    cout &lt;&lt; s1 &lt;&lt; endl; // 输出 1234567890
    s1.replace(5, 4, s3);
    cout &lt;&lt; s1 &lt;&lt; endl; // 输出 12345aaa0

    cout &lt;&lt; s2 &lt;&lt; endl; // 输出 1234567890
    s2.replace(5, 2, &#34;aaa&#34;);
    cout&lt;&lt; s2 &lt;&lt;endl; // 输出 12345aaa890
    return 0;
}
```

___

#### swap()成员函数

该函数用于**交换两个 string 类型变量的值**。

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;

using namespace std;

int main()
{
    string s1 = &#34;string&#34;;
    string s2 = &#34;aaaaaa&#34;;
   
    s1.swap(s2); // 互换值
    
    cout &lt;&lt; s1 &lt;&lt; endl; // 输出 aaaaaa
    cout &lt;&lt; s2 &lt;&lt; endl; // 输出 string
    return 0;
}
```

### 获取子字符串

使用成员函数 **`substr()`** 来获取string字符串中的一个子字符串。有两个参数，第一个参数为子字符串的起始下标，第二个参数为子字符串的长度(不指定此参数，则默认子字符串为从起始下标的字符开始一直到最后一个字符)。

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;
using namespace std;

int main()
{
    string s1 = &#34;first second third&#34;;
    string s2, s3;

    s2 = s1.substr(6, 6);
    s3 = s1.substr(6);

    cout &lt;&lt; s2 &lt;&lt; endl; // 输出second
    cout &lt;&lt; s3 &lt;&lt; endl; // 输出second third

    return 0;
}
```

### 查找字符串

使用成员函数 **`find()`** 在一个字符串中查找指定字符串。有两个参数，第一个参数为要查找的字符串，第二个参数为查找的起始位置(不指定此参数，则默认从0开始，即从字符串首开始查找)。

找到了会返回起始下标，没找到会返回一个 **`std::string::npos`** (这是一个静态成员常量，表示没有找到子字符串的占位符值)。

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;
using namespace std;

int main()
{
    string s1 = &#34;first second third&#34;;
    string s2 = &#34;second&#34;;

    int index1 = s1.find(s2, 6);
    int index2 = s1.find(s2, 7);

    // 输出 Found at index : 6
    if(index1 != std::string::npos)
        cout&lt;&lt;&#34;Found at index : &#34;&lt;&lt; index1 &lt;&lt;endl;
    else
        cout&lt;&lt;&#34;Not found&#34;&lt;&lt;endl;
    
    // 输出 Not found
    if(index2 != std::string::npos)
        cout&lt;&lt;&#34;Found at index : &#34;&lt;&lt; index2 &lt;&lt;endl;
    else
        cout&lt;&lt;&#34;Not found&#34;&lt;&lt;endl;

    return 0;
}
```

### 字符串的比较

**`==`、`!=`、`&lt;=`、`&gt;=`、`&lt;` 和 `&gt;`** 操作符都可以用于进行 `string` 类型字符串的比较（是从第一个字符开始逐个比较的），这些操作符两边都可以是 `string` 字符串，也可以一边是 `string` 字符串另一边是字符串数组。

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;
using namespace std;

int main()
{
    string s1 = &#34;secondsecondthird&#34;;
    string s2 = &#34;secondthird&#34;;
    
    if( s1 == s2 ) cout &lt;&lt; &#34; == &#34; &lt;&lt; endl;
    if( s1 != s2 ) cout &lt;&lt; &#34; != &#34; &lt;&lt; endl;
    if( s1 &lt; s2 ) cout &lt;&lt; &#34; &lt; &#34; &lt;&lt; endl;
    if( s1 &gt; s2 ) cout &lt;&lt; &#34; &gt; &#34; &lt;&lt; endl;

    return 0;
}
// 最终输出 != 和 &lt;
```

## 类和对象

C&#43;&#43;是一门 **面向对象** 的编程语言，学习 C&#43;&#43;，必须要搞清楚类和对象的概念。

类是C&#43;&#43;面向对象编程的实现方式，类可以看做是结构体的升级版，它既可以包含变量，也可以包含函数。C&#43;&#43;中的对象，可以理解为**通过类定义出来的变量**。和结构体一样，类是我们自定义的一种数据类型，而对象就是类这种数据类型的一个变量。一个类可以创建多个对象，每个对象都是类的一个具体实例，拥有类中的变量和函数。

### 类和对象的定义

定义一个类与定义一个结构体的语法相似，只不过将 **`struct`** 关键字换位了 **`class`** 关键字。定义完后就可以像使用`int`那样来使用 `Student`定义对象、数组、指针。示例如下：

```cpp
class Student
{
    // 在这里定义成员变量和成员函数
};

int main()
{
    Student xiao_ming; // 定义了一个 Student类型的对象 xiao_ming
    Student all_student[1000]; // 定义了一个Student类型的数组，大小为1000
    Student *stuptr; // 定义了一个Student类型的指针
    return 0;
}
```

### 类的成员变量和成员函数

前面说了类不仅拥有成员变量，还有成员函数。下面是一个`Student`类的定义:

```cpp
class Student
{
    // 定义对应的成员变量
    string _name;
    int _id_num;
    int _age;
    char _sex;

    // 定义对应的成员函数
    void set_age(int age);
    int get_age();
};
```

你可能发现了在定义类的时候我们并没有定义成员函数，只是对其进行了声明，这是因为有两种方法可以给出成员函数的定义：

1. 在定义类，对成员函数进行声明时就给出定义，这种方法被称为**内联定义**(即编译器会自动在函数前加上 `inline` 关键字)。这种定义方法直接在头文件(`.h`)中写函数定义。
2. 在类外部给出函数定义，即在对应的`.cpp`文件中给出函数定义（需要使用与解析符 **`::`**）。

**PS1：** 推荐使用第二种方法给出函数定义，即在头文件中给出类的定义（只对成员函数进行声明），在对应的 `.cpp` 文件中给出函数定义。只有那些一两行就可以搞定的成员函数才使用内联定义的方法。

**PS2：** 其实不只成员函数，构造函数、析构函数等也推荐使用第二种方式给出定义（除非函数定义特别短）。

___

说了那么多，你可能有点迷糊，那就来看看具体示例吧：

```cpp
class Student
{
    // 定义对应的成员变量
    string _name;
    int _id_num;
    int _age;
    char _sex;

    // 使用内联定义
    void set_age(int age) { _age = age; }
    int get_age() const { return _age; }  //这里的const表示该函数只会读取变量值，不会修改变量值，如果修改了变量值编译器会报错。
};

/* ---------------------------------分割线--------------------------------- */

// 在头文件 example.h 中给出类定义
class Student
{
    // 定义对应的成员变量
    string _name;
    int _id_num;
    int _age;
    char _sex;

    // 只声明成员函数
    void set_age(int age);
    int get_age() const;
};

// 在对应的 example.cpp 文件给出成员函数的定义
void Student::set_age(int age)  // 需要使用 类名&#43;域解析符:: 告诉编译器这是哪个类的成员函数
{
    _age = age;
}

int Student::get_age() const
{
    return _age;
}
```

### 成员访问符

通过前面的知识，我们已经学会了如何编写一个类、如何定义一个类对象、如何定义一个类对象指针等，那么我们该如何访问类成员呢？

和结构体一样，**类对象**通过 `.` 符号访问成员，**类对象指针**通过`-&gt;`符号访问成员。示例如下：

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;
using namespace std;

class Student
{
    string _name;
    int _id_num;
    int _age;
    char _sex;
public:
    void set_age(int age) { _age = age; }
    int get_age() const { return _age; }
};

int main()
{
    Student xiao_ming;
    Student *xiao_li_ptr = new Student;

    xiao_ming.set_age(20);
    cout &lt;&lt; xiao_ming.get_age() &lt;&lt; endl;

    xiao_li_ptr-&gt;set_age(22);
    cout &lt;&lt; xiao_li_ptr-&gt;get_age() &lt;&lt; endl;
    return 0;
}
```

### 类成员的访问权限以及类的封装

在上一小节中，你可能注意到了我在定义类时使用了 `public`，这是一个关键字，表示该关键字下的类成员是具有 **公开的** 访问权限。这一节就来详细讲解。

在C&#43;&#43;中，通过 **`public`、`protected`、`private`** 三个关键字来控制成员变量和成员函数的**访问权限**，它们分别表示公有的、受保护的、私有的。通俗来说，就是&lt;span style=&#34;color: red;&#34;&gt;**使用这三个关键字来限制成员的访问方式**&lt;/span&gt;，以实现**类的封装完成，完成数据隐藏**。这三个访问权限由高到低依次为： `public` → `protected` → `private`。

在这里主要讲解 `public` 和 `private` 关键字，`protected` 将会在继承和派生那一章进行详细讲解。

**你主要需要记住的是：**

1. 类对象和类对象指针只能访问 `public` 成员
2. `private`成员只能被该类的其他成员访问
3. 将尽可能多的成员设置为私有，以隐藏类的内部实现细节，只提供必要的公共接口（即函数）供外部访问。
4. 若某个私有成员需要被派生类访问，则将其设置为 `protected` 成员

示例如下：

```cpp
#include &lt;iostream&gt;
using namespace std;

//类的声明
class Student
{
private:
    // 私有成员，只能被该类的其他成员访问
    string _name;
    int _age;
    float _score;

public:
    // 公有成员
    void set_name(const string &amp;name);
    void set_age(const int age);
    void set_score(const float score);
    void showInfo() const;
};

// 成员函数的定义
void Student::set_name(const string &amp;name)
{
    _name = name;
}

void Student::set_age(const int age)
{
    _age = age;
}

void Student::set_score(const float score)
{
    _score = score;
}

void Student::showInfo() const
{
    cout &lt;&lt; _name &lt;&lt; &#34;的年龄是&#34; &lt;&lt; _age &lt;&lt; &#34;，成绩是&#34; &lt;&lt; _score &lt;&lt; endl;
}

int main()
{
    Student stu;
    stu.set_name(&#34;小明&#34;);
    stu.set_age(15);
    stu.set_score(92.5f);
    stu.showInfo();

    Student *pstu = new Student;
    pstu -&gt; set_name(&#34;李华&#34;);
    pstu -&gt; set_age(16);
    pstu -&gt; set_score(96);
    pstu -&gt; showInfo();

    return 0;
}
```

### 类(class)和结构体(struct)的区别

 在C语言中，`struct` 是只能定义成员变量，而不能定义成员函数的。而在 C&#43;&#43; 中，`struct` 与 `class` 相似，既可以定义成员变量，又可以定义成员函数。

`struct` 和 `class`的区别有：

1. **当不指定成员的访问权限时，`struct`中的成员默认是 `public` 属性，而`class`中的成员默认是 `private` 属性**。
2. `class` 继承默认是 `private` 继承，而 `struct` 继承默认是 `public` 继承
3. `class` 可以使用模板，而 `struct` 不能

**在编写C&#43;&#43;代码时，强烈建议使用 `class` 来定义类，而使用 `struct` 来定义结构体，这样做语义更加明确。**

### 通过引用来传递和返回类对象

在讲引用时，我们就说了推荐使用**引用** 来传递类对象，这是因为采用传值的方式需要经历对象间的拷贝操作，一定程度上会降低程序运行的效率。当然也可以使用指针来传递，但是使用引用更加简练直观。

示例如下：

```cpp
#include &lt;iostream&gt;
using namespace std;

class Student
{
private:
    string _name;
    int _age;
    float _score;

public:
    void set_name(const string &amp;name);
    void set_age(const int age);
    void set_score(const float score);

    string get_name()const {return _name;}
    int get_age()const {return _age;}
    float get_score()const {return _score;}
};

void Student::set_name(const string &amp;name)
{
    _name = name;
}

void Student::set_age(const int age)
{
    _age = age;
}

void Student::set_score(const float score)
{
    _score = score;
}

const Student&amp; show(const Student &amp;mstu) // 使用引用来传递和返回对象
{
    cout &lt;&lt; mstu.get_name() &lt;&lt; &#34;的年龄是&#34; &lt;&lt; mstu.get_age() &lt;&lt; &#34;，成绩是&#34; &lt;&lt; mstu.get_score() &lt;&lt; endl;
    return mstu;
}

int main()
{
    Student stu;
    stu.set_name(&#34;小明&#34;);
    stu.set_age(15);
    stu.set_score(92.5f);

    const Student &amp;t_stu = show(stu);
    cout &lt;&lt; t_stu.get_name() &lt;&lt; endl; // 输出小明
    return 0;
}
```

**PS：** 虽然前面已经说过，但是还是需要提醒的是 &lt;span style=&#34;color: red;&#34;&gt;**不要返回临时变量和局部变量的引用！！！**&lt;/span&gt;

### 构造函数及其调用

在前面的例子中，我们为每个成员变量都编写了初始化函数(set_name、set_age等)，但是，这很麻烦，有没有什么简单的方法可以来完成初始化呢？当然有，那就是使用**构造函数**！有了它就不用编写类似于set_name、set_age的函数了，偷懒星人狂喜（*＾▽＾）/

构造函数是类中一种**特殊的成员函数**，其特殊之处有三点：

* 构造函数的函数名必须**与类名相同**
* 构造函数**无返回值**
* 创建类对象的时候，构造函数会被**自动调用**，而无需我们主动调用

构造函数的作用就是**初始化对象，并处理对象创建时需要处理的其它事务**。

所以，前面的代码我们就可以简化为：

```cpp
#include &lt;iostream&gt;
using namespace std;

class Student
{
private:
    string _name;
    int _age;
    float _score;

public:
    Student(const string &amp;name, int age, float score);

    string get_name()const {return _name;}
    int get_age()const {return _age;}
    float get_score()const {return _score;}
    void showInfo() const;
};

Student::Student(const string &amp;name, int age, float score)
{
    _name = name;
    _age = age;
    _score = score;
}

void Student::showInfo() const
{
    cout &lt;&lt; _name &lt;&lt; &#34;的年龄是&#34; &lt;&lt; _age &lt;&lt; &#34;，成绩是&#34; &lt;&lt; _score &lt;&lt; endl;
}

int main()
{
    Student stu(&#34;小明&#34;, 15, 92.5f); // 隐式调用对应的构造函数完成初始化

    // Student stu1;   // 该代码是错误的，因为没有匹配的构造函数
    stu.showInfo();
    return 0;
}
```

需要注意的是，对于编写了构造函数的类，编译器就不会为其生成一个**访问权限为public的无参的默认构造函数**（该函数的函数体为空），所以上面注释的代码是错误的（没有匹配的构造函数）。如果要使用无参的构造函数就需要自己手动编写了。
&gt;既然有隐式调用构造函数，那么肯定就有显示调用构造函数的方法，但是并不推荐。  
&gt;
&gt;显示调用为：`Student stu = Student(&#34;小明&#34;, 15, 92.5f);`

**PS：** 构造函数既然是函数，那它就可以**重载和使用默认参数**。

### 初始化列表

在前面的例子中，我们是在类外部给出构造函数的定义来进行**成员变量的初始化**，还是太麻烦了（要写的代码太多了！），还有更简单的方法吗？

直接在声明构造函数时给出定义进行初始化？No，这样的初始化代码一点不简洁不优雅。这时 **初始化列表** 就出场了！直接看例子：

```cpp
#include &lt;iostream&gt;
using namespace std;

class Student
{
private:
    string _name;
    int _age;
    float _score;

public:
    // 使用初始化列表来进行成员变量的初始化
    Student(const string &amp;name, int age, float score):_name(name), _age(age), _score(score){}

    string get_name()const {return _name;}
    int get_age()const {return _age;}
    float get_score()const {return _score;}
    void showInfo() const;
};

void Student::showInfo() const
{
    cout &lt;&lt; _name &lt;&lt; &#34;的年龄是&#34; &lt;&lt; _age &lt;&lt; &#34;，成绩是&#34; &lt;&lt; _score &lt;&lt; endl;
}

int main()
{
    Student stu(&#34;小明&#34;, 15, 92.5f);
    stu.showInfo();
    return 0;
}
```

如果构造函数**只进行初始化操作**，那么墙裂推荐使用初始化列表。如果构造函数除了初始化成员变量，还需要进行其他操作，操作较短可直接写在函数体内，操作较长那还是建议不使用初始化列表在类外部编写构造函数的定义。

&lt;span style=&#34;color: red;&#34;&gt;注意，成员变量的初始化顺序与初始化列表中列出的变量的顺序无关，它只与成员变量在类中声明的顺序有关。&lt;/span&gt;

```cpp
class Student
{
private:
    string _name;
    int _age;
    float _score;

public:
    // 初始化顺序只跟变量声明顺序有关，这里的初始化顺序为 _name, _age, _score
    Student(const string &amp;name, int age, float score): _score(score), _age(age),  _name(name) {}
    ......
};
```

**PS：&lt;span style=&#34;color: red;&#34;&gt;const成员变量只能使用初始化列表来进行初始化！&lt;/span&gt;**

### 转型构造函数

构造函数一般划分为两类：默认构造函数（不带参数）和带参构造函数。在带参构造函数中有两种比较常见的构造函数：转型构造函数和复制构造函数。在这一节先介绍**转型构造函数**。

转型构造函数用于 **「隐式类型转换，隐式地将其他类型的对象转换为该类的对象」**。当转型构造函数只有一个参数时，当这个参数为`int`类型，则可用于将`int`类型的对象转变为该类对象；当这个参数为`char *`类型，则可用于将`char *`类型的对象转变为该类对象。

&gt; 这里举一个参数的转型构造函数只是方便讲解，转型构造函数当然可以有多个参数，通过学习下面的例子自己就能举一反三。

直接看一个简单的例子：

```cpp
#include &lt;iostream&gt;
using namespace std;

class Age
{
public:
    Age(int a):_age(a){} // 定义了一个转型构造函数，用于隐式类型转换
private :
    int _age;
};

void func(Age a)
{
    cout &lt;&lt; &#34;The function is called.&#34; &lt;&lt; endl;
}

int main()
{
    int num = 7;
    func(num);
    return 0;
}
```

明明`func`函数的参数是一个`Age`类型的变量，为什么我们传入一个`int`类型的变量也可以成功调用该函数呢？  
这是因为执行代码 `func(num);` 时，编译器发现形参与实参类型不匹配且在形参的类定义中发现了一个用于将`int`类型对象转换为`Age`类型对象的转型构造函数，这时编译器就会自动调用该转型构造函数将`int`类型对象转换为`Age`类型对象，然后再使用转换出的`Age`类型对象去调用`func`函数。

&gt;**之所以说是隐式的，是因为这个转型过程完全由编译器完成，无需程序设计人员来显示的转换。**

___

隐式类型转换给我们带来了一定的便利，但更可能会给我们设计的程序带来一些难以觉察的细微错误。有时候我们希望关闭掉这种隐式类型转换，这时就需使用
**`explicit`** 关键字。修改上面的例子：

```cpp
#include &lt;iostream&gt;
using namespace std;

class Age
{
public:
    explicit Age(int a):_age(a){} // 使用 explicit 关键字，禁止此构造函数用于隐式类型转换
private :
    int _age;
};

void func(Age a)
{
    cout &lt;&lt; &#34;The function is called.&#34; &lt;&lt; endl;
}

int main()
{
    int num = 7;
    func(num);  // 编译时，此代码就会报错了，因为实参与形参类型不匹配且无法转换
    return 0;
}
```

### 复制（拷贝）构造函数

复制构造函数也被称为拷贝构造函数，顾名思义就是 **「创建一个现有对象的副本」**。标准的复制构造函数格式如下：

```cpp
class MyClass {
public:
    // 标准复制构造函数
    MyClass(const MyClass&amp; other)  // 使用 const 防止修改原始对象，也可去掉const，但是引用符号是万万不可去掉的
    {
        // ...初始化代码...
    }
};
```

&gt;C&#43;&#43;11标准引入了一种特殊的复制构造函数，称为“完美转发”构造函数，它可以有多个参数，并且这些参数可以通过模板参数完美转发给其他构造函数或函数。但一般标准复制构造函数就能满足需求了。如有需要，请自行学习“完美转发”构造函数。

复制构造函数的参数必须是&lt;span style=&#34;color: red;&#34;&gt;**该类对象的引用**&lt;/span&gt;。这是因为如果不使用引用传递，那就是通过值传递，而值传递需要通过复制的方式创建一个[临时对象](https://www.cnblogs.com/weikongtao/p/18040671)，再将该临时对象传递给复制构造函数。而临时对象的复制构造函数也是值传递，这又需要创建一个临时对象2来传递，依次类推，会创建无限多的临时对象，导致爆栈。

你可能觉点抽象，没关系，直接看例子：

```cpp
class Student
{
public:
    Student(const Student s) // 创建了一个参数不是引用的复制构造函数
    {
        ......
    }
private:
    ......
};

int main()
{
    Student a; // 创建了一个Student类的对象a

    Student b(a); // 创建了一个新的对象b，用a来进行初始化
    /**
     *  由于Student类的复制构造函数是值传递的，所以会创建一个临时对象（假设为 temp1）并使用a来进行初始化，再
     *  将该临时对象传递给b的复制构造函数，即 Student temp1(a);
     *  而创建temp1对象又需要创建一个临时对象 temp2 来传递值给其复制构造函数，即 Student temp2(a);
     *  这样就会无限递归下去，直到爆栈
     */

    return 0;
}
```

当你没有编写复制构造函数时，编译器会自动生成一个复制构造函数，生成的复制构造函数只能进行**浅复制**。
&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;浅复制与深复制的定义&lt;/summary&gt;

* **浅复制**是复制对象的数据成员的**值**。如果数据成员包括指向动态分配内存的**指针**，**浅复制不会复制内存本身**，而是只复制指针的值。这意味着原始对象和复制对象的指针成员将**指向相同的内存地址**
* **深复制**不仅复制非指针数据成员的值，还会复制指针指向的内存空间，即申请一块新的内存空间，在该内存空间存储与原来的内存空间一样的值，并将新内存空间的地址赋值给副本对象对应的指针成员。这意味着原始对象和复制对象的指针成员将**指向不同的内存地址**，每个对象都有自己的独立拷贝

简单来说，浅复制和深复制的差别就在于指针类型成员的处理，浅复制只会复制初始对象的指针成员存储的地址值，不会开辟新空间；而深复制则会开辟新的内存空间，将初始对象的指针成员指向的内存空间中存储的值复制到新内存空间中。

&lt;/details&gt;

既然编译器会自动生成复制构造函数，那么什么时候才需要我们自己手写呢？

1. 当类中有动态资源需要进行深拷贝
2. 默认的复制构造函数可能会导致资源管理问题

### 析构函数

在创建对象的时系统会自动调用构造函数来进行初始化，在对象需要被销毁时系统同样会自动调用一个函数来释放相关资源（例如申请的内存空间等），这个函数被称之为**析构函数**。

析构函数也是一个成员函数，与普通成员函数相比，有如下特征：

* 无返回值
* 不接受任何参数
* 函数名必须为 **`~类名`**
* 不能重载，一个类有且仅有一个析构函数
* 必须是 `public` 访问权限

示例如下：

```cpp
class Array
{
public:
    Array(): length(0), num(nullptr){}  // nullptr 表示空指针
    Array(int * A, int n);
    Array(const Array &amp;a);
    void setIndexValue(int value, int index);
    int * getArray() const;
    int get_length() const {return length;}
    ~Array(); // 定义析构函数

private:
    int length;
    int *num;
};

// 这里只需关注析构函数，其他函数就不写了
Array::~Array()
{
    if(num != NULL) delete[] num;  // 释放申请的内存空间
    cout&lt;&lt;&#34;destructor&#34;&lt;&lt;endl;
}
```

&gt; 析构函数的调用顺序与构造函数的调用顺序相反，即先调用构造函数的对象后调用析构函数，后调用构造函数的对象先调用析构函数。

**PS：** 虽然编译器会生成默认析构函数，但还是推荐自己编写析构函数，确保资源的正确释放。

### 常量指针this

在C&#43;&#43;中，`this` 指针是一个特殊的指针，它在每个**非静态**成员函数中都隐含地存在。`this` 指针存储的是调用成员函数的对象的地址。使用 `this` 指针可以访问调用对象的成员变量和成员函数，这在处理对象内部数据或实现链式调用时特别有用。

**PS：** 静态成员函数是没有 `this` 指针的，学了后续章节 类与 static 关键字 就可明白。

```cpp
#include &lt;iostream&gt;
using namespace std;

class Box
{
public:
    Box(int length, int width): _length(length), _width(width) {}  // 使用初始化列表直接初始化成员变量

    void set_length(int length)
    {
        this-&gt;_length = length; // 使用this指针访问成员变量，这只是举例，实际上直接写 _length = length; 即可
    }

    // 返回 *this 允许链式调用
    Box&amp; set_width(int width)
    {
        this-&gt;_width = width;
        return *this;
    }

    void display() const
    {
        std::cout &lt;&lt; &#34;Length: &#34; &lt;&lt; _length &lt;&lt; &#34;, Width: &#34; &lt;&lt; _width &lt;&lt; std::endl;
    }

private:
    int _width;
    int _length;
};

int main()
{
    Box box(20, 10);
    box.display(); // 输出 Length: 20, Width: 10

    box.set_width(20).set_length(10);  // 链式调用
    box.display(); // 输出 Length: 10, Width: 20
    return 0;
}
```

### 类与 new 和 delete 关键字

当我们需要为类对象动态分配内存空间和释放内存空间时，应该使用 C&#43;&#43;语言提供的 `new`、`new[]`、`delete`、`delete []` 关键字，不要使用 C语言提供的 `malloc()`、`free()` 函数。

这是因为 `new`、`new[]` 在申请到内存空间后会调用类的构造函数，而`malloc()`不会；同理，`delete`、`delete []` 在释放内存空间前会先调用类的析构函数释放相关资源，而`free()`不会。

```cpp
#include&lt;iostream&gt;
using namespace std;

class test
{
public:
    test(int num = 1):_num(num){cout &lt;&lt; _num &lt;&lt; &#34; Constructor&#34; &lt;&lt; endl;}
    ~test(){cout &lt;&lt; _num &lt;&lt; &#34; Destructor&#34; &lt;&lt; endl;}
private:
    int _num;
};

int main()
{
    test * t0 = new test(0); // 输出：0 Constructor
    test * t1 = new test[3]; // 输出3次：1 Constructor
    test * t2 = (test *)malloc(sizeof(test)); // 不会有输出

    delete t0; // 输出：0 Destructor
    delete[] t1; // 输出3次：1 Destructor
    free(t2); // 不会有输出

    return 0;
}

```

### 类与 const 关键字&lt;a id=&#34;chapter-2&#34;&gt;&lt;/a&gt;

在[前面](#chapter-1)我们已经详介绍过`const`关键字了，`const`当然也可以和成员变量和成员函数结合使用（和普通变量、普通函数结合使用的方法一样），除此之外，类对象也可以结合`const`关键字（即在定义类对象时在类名前加上`const`关键字）。

需要注意的是：`const`成员变量只能使用**初始化列表**来进行初始化；`const`成员函数可以访问任何成员变量，但**只能访问`const`成员函数**；`const`对象只能访问`const`成员函数。

**PS:** 对于`const`成员函数，编译器会使用`const`来修饰其`this`指针，确保其不会修改实列对象的状态（即改变属于对象的成员变量）。

### 类与 mutable 关键字（选读）

`mutable`的含义是“可变的，易变的”，与常量关键词`const`的含义相反。那么什么时候才需要使用该关键词呢？什么时候才需要将一个成员变量声明为可变的呢？

没错！就是需要在`const`成员函数中被修改的成员变量！当一个成员函数被`const`修饰时，说明该函数不会去修改成员变量的值。然而，在某些情况下，我们需要在`const`成员函数中修改某个与**类对象关系不大的变量**。比如，统计某个`const`成员函数调用次数，这时就需要将存储调用次数的变量声明为 `mutable`。

```cpp
class Counter 
{
private:
    mutable int callCount;  // 将其声明为可变的

public:
    Counter() : callCount(0) {}

    void someConstMethod() const
    {
        &#43;&#43;callCount; // 可以在 const 函数中被修改
        // 执行其他逻辑
    }

    int getCallCount() const {
        return callCount;
    }
};
```

### 类与 static 关键字

#### 静态成员变量

到目前为止，我们设计的类的所有成员变量都属于**该类对象**，并不属于类。例如，我们前面设计的`Student`类

```cpp
class Student
{
private:
    string _name;
    int _age;
    float _score;

public:
    // 使用初始化列表来进行成员变量的初始化
    Student(const string &amp;name, int age, float score):_name(name), _age(age), _score(score){}

    string get_name()const {return _name;}
    int get_age()const {return _age;}
    float get_score()const {return _score;}
    void showInfo() const;
};
```

每个`Student`对象都有自己的存储空间，用来存储自己的`_name`、`_age`、`_score`变量，你修改 `a._age` 值并不影响 `b._age` 值。

可有时我们希望**在多个对象之间共享数据**，对象`a`改变了某个数据后对象`b`可以检测到。这时就可以使用**静态成员变量**来实现数据共享，即在变量类型前使用 **`static`** 关键字修饰。

能实现数据共享的原因在于 **「静态成员变量属于类，不属于该类对象」**，即使创建多个对象，也只为静态成员变量分配一个内存空间，所有对象都使用这个内存空间的数据。当某个对象修改了其值，也会影响到其他对象。

```cpp
class Student
{
public:
    Student(){_count&#43;&#43;;}
    ~Student(){_count--;}
private:
    static int _count;
    //其它成员变量
};
```

在上面的简单例子中，我们定义了一个`_count`静态成员变量用来统计学生人数。你可能发现了，在定义`_count`时我们并没有进行初始化，这是因为 **「静态成员变量只能在类外部进行初始化」**，具体格式为：  
**`类型 类名::变量名 = 值`**

那么就将上面的例子完成初始化吧：

```cpp
class Student
{
public:
    Student(){_count&#43;&#43;;}
    ~Student(){_count--;}
private:
    static int _count;
    //其它成员变量
};

int Student::_count = 0; // 不能再加上 static
```

&lt;span style=&#34;color: red;&#34;&gt; **注意：** &lt;/span&gt;`static` 成员变量的内存既不是在定义类时分配，也不是在创建对象时分配，而是在**初始化时分配**。反过来说，没有在类外进行初始化的 `static` 成员变量是不能使用的。且静态成员变量是属于类的，那么在初始化后就可使用 **`类名::静态成员变量名`** 的方式对访问权限为 **`public`的静态成员变量** 进行访问。

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;静态成员变量的内存空间在哪？&lt;/summary&gt;

静态成员变量的内存空间和普通静态变量、全局变量一样，都是在内存的全局数据区进行分配，在该静态成员变量进行初始化时分配，到程序结束时才释放。

&lt;/details&gt;

___

#### 静态成员函数

既然可以声明静态成员变量，那么可不可以声明静态成员函数呢？当然可以！同样的在函数返回值前加上`static`关键字即可声明一个**静态成员函数**。

在前面我们说：非静态成员变量是属于对象的，而静态成员变量是属于类的。那么由此可知静态成员函数也是属于类的，那非静态成员函数呢？

没错！**非静态成员函数也是「属于类」的！**

非静态成员函数和静态成员函数的根本区别在于：**「非静态成员函数有`this`指针，可以访问类中任意成员；而静态成员函数没有`this`指针，只能访问类的静态成员（静态成员变量和静态成员函数）」**。

那么就来把上面的`Student`类完善一下吧，

```cpp
#include &lt;iostream&gt;
using namespace std;

class Student
{
public:
    Student(char *name, int age, float score);
    void show() const;

public:  //声明静态成员函数
    static int getTotal();
    static float getPoints();

private:
    static int _total;  //总人数
    static float _points;  //总成绩

private:
    char *_name;
    int _age;
    float _score;
};

// 静态成员变量初始化
int Student::_total = 0;
float Student::_points = 0.0;

Student::Student(char *name, int age, float score): _name(name), _age(age), _score(score)
{
    _total&#43;&#43;;
    _points &#43;= score;
}

void Student::show() const
{
    cout &lt;&lt; _name &lt;&lt; &#34;的年龄是&#34; &lt;&lt; _age &lt;&lt; &#34;，成绩是&#34; &lt;&lt; _score &lt;&lt; endl;
}

//定义静态成员函数
int Student::getTotal()
{
    return _total;
}

float Student::getPoints()
{
    return _points;
}

int main()
{
    (new Student(&#34;小明&#34;, 15, 90.6)) -&gt; show();
    (new Student(&#34;李磊&#34;, 16, 80.5)) -&gt; show();
    (new Student(&#34;张华&#34;, 16, 99.0)) -&gt; show();
    (new Student(&#34;王康&#34;, 14, 60.8)) -&gt; show();

    int total = Student::getTotal();
    float points = Student::getPoints();

    cout &lt;&lt; &#34;当前共有&#34; &lt;&lt; total &lt;&lt; &#34;名学生，总成绩是&#34; &lt;&lt; points &lt;&lt; &#34;，平均分是&#34; &lt;&lt; points/total &lt;&lt; endl;

    return 0;
}
```

既然非静态成员函数和静态成员函数都属于类，那为什么还要把一个成员函数声明为静态的呢？前面已经说了，静态成员函数只能访问静态成员变量，当一个成员函数只会去访问静态成员变量时，我们就可将其声明为静态成员函数，这样可以更加明确该函数的功能，使语义更加清晰。

&lt;span style=&#34;color: red;&#34;&gt; **注意：** &lt;/span&gt; 静态成员函数不能被`const`修饰，在[前面](#chapter-2)的学习中，我们已经知道对于`const`成员函数，编译器会使用`const`来修饰其`this`指针，确保其不会修改实列对象的状态。但静态成员函数并没`this`指针，本就不会修改实列对象的状态，自然就不能使用`const`修饰。

### 友元函数和友元类

我们在前面说只有该类的成员函数才能访问该类的`private`成员，只有该类的成员函数及其派生类的成员函数可以访问该类的`protected`成员。现在，我们来介绍一种例外情况——**友元（friend）**。「通过`friend`关键字可以使得其他类的成员函数以及全局范围内的函数访问当前类的`private`和`protected`成员」。

#### 友元函数

当该类外的函数（指其他类的成员函数和全局范围内的函数）需要访问该类的`private`和`protected`成员时，就可在该类中将此函数声明为友元函数。

```cpp
#include &lt;iostream&gt;
using namespace std;

class Student{
public:
    Student(char *name, int age, float score);
public:
    friend void show(const Student &amp;stu);  // 将全局范围内的函数 show()声明为友元函数
private:
    char *_name;
    int _age;
    float _score;
};

Student::Student(char *name, int age, float score): _name(name), _age(age), _score(score){ }


void show(const Student &amp;stu)
{
    cout &lt;&lt; stu._name &lt;&lt; &#34;的年龄是 &#34; &lt;&lt; stu._age &lt;&lt; &#34;，成绩是 &#34; &lt;&lt; stu._score &lt;&lt; endl;
}

int main()
{
    Student stu(&#34;小明&#34;, 15, 90.6);
    show(stu);  // 调用友元函数

    return 0;
}
```

&lt;span style=&#34;color: red;&#34;&gt; **注意：** &lt;/span&gt; 友元函数毕竟不是该类的函数，在友元函数中不能直接访问该类成员，必须 **「借助该类对象或该类对象指针」**。友元函数的在该类中的声明位置没有要求，但是推荐在 **`public`** 下声明。

___

#### 友元类

不仅可以将一个函数声明为一个类的 &#34;朋友&#34;，还可以将**整个类**声明为另一个类的 &#34;朋友&#34;，这就是友元类。友元类中的所有成员函数都是另一个类的友元函数。

例如将类 `B` 声明为类 `A` 的友元类，那么类 `B` 中的所有成员函数都是类 `A` 的友元函数，可以访问类 `A` 的所有成员，包括 `public`、`protected`、`private` 属性的。

```cpp
// 提前声明Address类，告诉编译器存在Address类
class Address;

//声明Student类
class Student{
public:
    Student(char *name, int age, float score);
public:
    void show(Address *addr); // 在这里还未进行Address类的声明，所以需要在前面进行提前声明存在Address类
private:
    char *_name;
    int _age;
    float _score;
};

//声明Address类
class Address
{
public:
    Address(char *province, char *city, char *district);
public:
    //将Student类声明为Address类的友元类，Student类的成员函数均可访问该类的成员
    friend class Student;
private:
    char *_province;
    char *_city;
    char *_district;
};
```

&lt;span style=&#34;color: red;&#34;&gt; **注意：** &lt;/span&gt;除非有必要，一般不建议把整个类声明为友元类，而只将某些成员函数声明为友元函数，这样更安全一些。且友元关系是**不能传递**的。比如类`B`是类`A`的友元类，类`C`是类`B`的友元类，但类`C`不是类`A`的友元。

## 继承和派生

### 继承的概念与语法

在C&#43;&#43;中继承是一个很简单很直观的概念，描述的是 **「类与类之间的关系」**。与现实世界中的继承类似，例如儿子继承父亲的财产。B继承于A可以理解为「B类获取了A类的成员变量和成员函数」。

在C&#43;&#43;中继承和派生是**同一个概念，只是站的角度不同**。继承是儿子接收父亲的产业，派生是父亲把产业传承给儿子。

&lt;span style=&#34;color: red;&#34;&gt;被继承的类称为父类或基类，继承的类称为子类或派生类。通常“子类”和“父类”放在一起称呼，“基类”和“派生类”放在一起称呼。在上面的例子中，你把A类称为父类的话，就得把B类称为子类（当然也可以将其称为派生类，但是不协调）。&lt;/span&gt;

派生类除了天然拥有基类的成员，还可以**定义自己的新成员**，以增强类的功能。

**常见的继承使用场景有：**

1. 创建的新类与某个已有类很相似，只是多出若干成员，可以让新类继承于该已有类。
2. 当需要创建多个类时，这些类拥有很多相似成员，可以将这些相似成员提取出来，定义为一个基类，然后从该基类派生出这些类。

基类的定义方法与普通类一样，派生类的定义语法为：

```cpp
class 派生类类名: 继承方式 基类名
{
    ......
};
```

说了那么多，直接来看例子：

```cpp
#include&lt;iostream&gt;
using namespace std;

//定义一个基类 Pelple
class People
{
public:
    void set_name(char *name);
    void set_age(int age);
    char *get_name() const;
    int get_age() const;
private:
    char *_name;
    int _age;
};
void People::set_name(char *name){ _name = name; }
void People::set_age(int age){ _age = age; }
char* People::get_name()const { return _name; }
int People::get_age()const { return _age;}

//派生类 Student，采用public继承方式，后面会详细讲解继承方式
class Student: public People
{
public:
    void set_score(float score);
    float get_score() const;
private:
    float _score;
};
void Student::set_score(float score){ _score = score; }
float Student::get_score() const { return _score; }

int main(){
    Student stu;
    // 继承了People类的对应成员
    stu.set_name(&#34;小明&#34;);
    stu.set_age(16);

    stu.set_score(95.5f);
    cout &lt;&lt; stu.get_name() &lt;&lt; &#34;的年龄是 &#34; &lt;&lt; stu.get_age() &lt;&lt; &#34;，成绩是 &#34; &lt;&lt; stu.get_score() &lt;&lt; endl;
    return 0;
}
```

### 继承方式

派生类继承基类的继承方式有三种：`public`、`protected`、`private`。没错，又是它们三兄弟！在没有指定继承方式时，编译器默认继承方式为`private`。

这三种继承方式有什么区别呢？**不同的继承方式会影响基类成员在派生类中的访问权限**。

1. **`public`继承方式**  
   * 基类中的`public`成员在派生类中仍然是`public`访问权限
   * 基类中的`protected`成员在派生类中仍然是`protected`访问权限
2. **`protected`继承方式**  
   * 基类中的`public`成员在派生类中是`protected`访问权限
   * 基类中的`protected`成员在派生类中仍然是`protected`访问权限
3. **`private`继承方式**  
   * 基类中的`public`成员在派生类中是`private`访问权限
   * 基类中的`protected`成员在派生类中是`private`访问权限

你可能发现了，继承方式是用来 **「指定基类成员在派生类中的最高访问权限的」**。例如，当继承方式为`protected`时，那么基类成员在派生类中的最高访问权限也为`protected`，高于`protected`的会降级为`protected`，但低于的不会升级。

为什么没有谈论基类的`private`成员呢？因为我们在前面说过了一个类的`private`成员只能被该类的其他成员访问。所以，基类的`private`成员虽然被派生类所继承（仍然是`private`访问权限），且占用派生类对象的内存，但它**在派生类中是不可见的**，派生类只能通过基类的成员函数去访问它。`private` 成员的这种特性，能够很好的对派生类隐藏基类的实现，以体现面向对象的封装性。

![图2](/PostsImgs/Cplusplus_learning_note_imgs/picture2.png)

&lt;span style=&#34;color: red;&#34;&gt;**注意：**&lt;/span&gt;由于 `private` 和 `protected` 继承方式会改变基类成员在派生类中的访问权限，导致继承关系复杂，**所以实际开发中我们一般使用 `public`继承方式**。

### 修改基类成员在派生类中的访问权限

根据实际情况确定好类的继承方式后，是不是基类成员在派生类中的访问权限就无法修改了呢？并不是，使用 `using`关键字可以修改基类成员在派生类中的访问权限，在派生类中  对应访问权限下进行以下声明：  
**`using 基类名::成员名;`**

&lt;span style=&#34;color: red;&#34;&gt;**注意：**&lt;/span&gt;函数成员也只需要写函数名即可，不必加上`()`符号。且`using`只能改变基类的 `public` 和 `protected` 成员的访问权限，不能改变 `private` 成员的访问权限，因为基类的 `private` 成员在派生类中是不可见的。

```cpp
#include&lt;iostream&gt;
using namespace std;

// 基类People
class People
{
public:
    void show();
protected:
    char *_name;
    int _age;
};
void People::show() {
    cout &lt;&lt; _name &lt;&lt; &#34;的年龄是&#34; &lt;&lt; _age &lt;&lt; endl;
}

// 派生类Student
class Student : public People
{
public:
    void learning();
public:
    using People::_name;  //将protected改为public
    using People::_age;  //将protected改为public
    float _score;
private:
    using People::show;  //将public改为private
};
void Student::learning() {
    cout &lt;&lt; &#34;我是&#34; &lt;&lt; _name &lt;&lt; &#34;，今年&#34; &lt;&lt; _age &lt;&lt; &#34;岁，这次考了&#34; &lt;&lt; _score &lt;&lt; &#34;分！&#34; &lt;&lt; endl;
}

int main() {
    Student stu;
    stu._name = &#34;小明&#34;;
    stu._age = 16;
    stu._score = 99.5f;
    //stu.show();  //编译错误，因为在派生类中show()函数被声明为私有成员了
    stu.learning();

    return 0;
}
```

### 继承时的名字遮蔽问题

如果派生类中的成员（包括成员变量和成员函数）和基类中的成员**重名**，那么就会遮蔽从基类继承过来的成员。所谓遮蔽，就是在派生类中使用该成员时，使用的是派生类自己的成员，而不是从基类继承过来的成员。如果要使用从基类继承过来的成员，就得采用 **`基类名::成员名`** 的方式。

&lt;span style=&#34;color: red;&#34;&gt;**注意：**&lt;/span&gt;派生类的成员函数与基类的成员函数不构成重载（作用域不同），所以只要同名就会造成遮蔽。

```cpp
#include&lt;iostream&gt;
using namespace std;

class People
{
public:
    People(char *name, int age): _name(name), _age(age){}
    void show();
protected:
    char *_name;
    int _age;
};
void People::show()
{
    cout &lt;&lt; &#34;嗨，大家好，我叫&#34; &lt;&lt; _name &lt;&lt; &#34;，今年&#34; &lt;&lt; _age &lt;&lt; &#34;岁&#34; &lt;&lt; endl;
}

class Student: public People
{
public:
    // 别急，下小节会解释为什么派生类的构造函数这样写
    Student(char *name, int age, float score):People(name, age), _score(score){}
public:
    void show();  //遮蔽基类的show()
private:
    float _score;
};

void Student::show()
{
    cout &lt;&lt; _name &lt;&lt; &#34;的年龄是&#34; &lt;&lt; _age &lt;&lt; &#34;，成绩是&#34; &lt;&lt; _score &lt;&lt; endl;
}

int main()
{
    Student stu(&#34;小明&#34;, 16, 90.5);
    // 调用的是派生类新增的成员函数，而不是从基类继承的
    stu.show();

    // 调用的是从基类继承来的成员函数
    stu.People::show();

    return 0;
}
```

### 基类和派生类的构造函数

经过前面的学习，你肯定以为派生类能够继承基类的全部成员函数吧！No，No，No，这里能被继承的成员函数仅限于那些普通成员函数，**「构造函数（包括复制构造函数、转型构造函数等）、析构函数是不能被继承的」**。

不能被继承是因为这些函数与类是紧紧相关的，例如，如果基类的构造函数被派生类继承了，但该构造函数与派生类的类名并不相同，它并不能成为派生类的构造函数，更不可能成为派生类的普通函数。所以，它并不能被继承。

派生类既然继承了基类的成员变量，那么这些变量的初始化工作也应由派生类的构造函数完成，但是大部分基类的成员变量都是`private`访问权限的，它们在派生类中无法访问，更不能使用派生类的构造函数来初始化。&lt;span style=&#34;color: red;&#34;&gt;解决这个问题的思路是：在派生类的构造函数中调用基类的构造函数。&lt;/span&gt;

在上一节的示例代码中，有这样一行 `Student(char *name, int age, float score):People(name, age), _score(score){}`  
`People(name, age)`就是在调用基类的构造函数，并将name和age作为实参传给基类的构造函数。

也可以将基类构造函数的**调用**放在参数初始化列表后：  
`Student(char *name, int age, float score): _score(score), People(name, age){}`  
不管顺序如何，「派生类的构造函数都会先调用基类的构造函数再执行其他代码」。

&lt;span style=&#34;color: red;&#34;&gt;**注意：**&lt;/span&gt;只能在派生类构造函数的头部调用基类的构造函数，而不能在派生类构造函数的函数体内调用。因为基类构造函数不会被继承，不能当做普通的成员函数来调用。

___

从上面的学习可以得出一个结论：**派生类总是先调用基类的构造函数，再调用派生类的构造函数**。即先执行基类的构造函数，再执行派生类的构造函数。

那么继承关系有多层时，构造函数的执行顺序是怎样的呢？**「构造函数的执行顺序是按照继承的层次自顶向下」**。  
例如：C继承于B，B继承于A，那么构造函数的执行顺序为 A的构造函数→B的构造函数→C的构造函数。  
&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;直接基类和间接基类的定义&lt;/summary&gt;
  顾名思义，一个类直接继承的类被称为直接基类（简称基类），间接继承的类被称为间接基类。在上面的例子中，B是C的直接基类，A是C的间接基类。
&lt;/details&gt;

&lt;span style=&#34;color: red;&#34;&gt;**注意：**&lt;/span&gt;**派生类只能且必须调用直接基类的构造函数**。要理解这句话必须明白两点：1. 如果你没有在派生类的构造函数头部调用基类的构造函数，那么编译器就会自动调用基类的默认构造函数，如果基类不存在默认构造函数，则编译时会报错。2. 通过第一点可知，直接基类会调用它的基类的构造函数，如果派生类再调用其间接基类的构造函数，那么就会重复调用，造成资源的浪费，所以派生类禁止调用间接基类的构造函数（如上面的C类不能调用A类的构造函数）。

### 基类和派生类的析构函数&lt;a id=&#34;chapter-3&#34;&gt;&lt;/a&gt;

和构造函数类似，析构函数也不能被继承。与构造函数不同的是，在派生类的析构函数中**不用显式地调用**基类的析构函数，因为每个类只有一个析构函数，编译器知道如何选择，无需程序员干涉。

析构函数的执行顺序与构造函数的执行顺序是相反的：**「先执行派生类的析构函数，再执行基类的析构函数」**。

```cpp
#include &lt;iostream&gt;
using namespace std;

class A
{
public:
    A(){cout&lt;&lt;&#34;A constructor&#34;&lt;&lt;endl;}
    ~A(){cout&lt;&lt;&#34;A destructor&#34;&lt;&lt;endl;}
};

class B: public A
{
public:
    B(){cout&lt;&lt;&#34;B constructor&#34;&lt;&lt;endl;}
    ~B(){cout&lt;&lt;&#34;B destructor&#34;&lt;&lt;endl;}
};

class C: public B
{
public:
    C(){cout&lt;&lt;&#34;C constructor&#34;&lt;&lt;endl;}
    ~C(){cout&lt;&lt;&#34;C destructor&#34;&lt;&lt;endl;}
};

int main()
{
    C test;
    return 0;
}
```

输出结果为：

```bash
A constructor
B constructor
C constructor
C destructor
B destructor
A destructor
```

### 多继承

在前面的例子中，派生类都只有一个基类，称为单继承。除此之外，C&#43;&#43;也支持多继承，即**一个派生类可以有两个或多个基类**。
&gt;多继承容易让代码逻辑复杂、思路混乱，一直备受争议，中小型项目中较少使用。使用多继承时一定要确保代码逻辑正确。

多继承的语法很简单，将多个基类用逗号隔开即可。例如已声明了类A、类B和类C，那么可以这样来声明派生类D：

```cpp
class D: public A, private B, protected C
{
    ......
}
```

D 是多继承形式的派生类，它以公有的方式继承 A 类，以私有的方式继承 B 类，以保护的方式继承 C 类。D 根据不同的继承方式获取 A、B、C 中的成员，确定它们在派生类中的访问权限。

___

多继承形式下的构造函数和单继承形式基本相同，只是要在派生类的构造函数中调用多个基类的构造函数。以上面的 A、B、C、D 类为例，D 类构造函数的写法为：

```cpp
// 这里的声明顺序决定了后面基类构造函数的调用顺序
class D: public A, private B, protected C
{
    D(形参列表):A(实参列表),B(实参列表),C(实参列表){ /*其他操作*/ }
    ......
}
```

&lt;span style=&#34;color: red;&#34;&gt;**注意：**&lt;/span&gt;基类构造函数的调用顺序和和它们在派生类构造函数中出现的顺序无关，而是和声明派生类时基类出现的顺序相同。

___

使用多继承时，当两个或多个基类中有同名的成员时，如果直接访问该成员，就会产生命名冲突，编译器不知道使用哪个基类的成员。这个时候需要在成员名字前面加上**类名和域解析符`::`**，以显式地指明到底使用哪个类的成员，消除二义性。

### 虚继承和虚基类

在多继承时很容易产生命名冲突问题，如果我们很小心地将所有类中的成员变量及成员函数都命名为不同的名字时，命名冲突依然有可能发生，比如非常经典的菱形继承结构。

所谓菱形继承，举个例子，类 A 派生出类 B 和类 C，类 D 继承自类 B 和类 C，这个时候类 A 中的成员变量和成员函数继承到类 D 中变成了两份，一份来自 A → B → D 这条路，另一份来自 A → C → D 这一条路。  
![图3](/PostsImgs/Cplusplus_learning_note_imgs/picture3.png)

```cpp
class A
{
public:
    void set_x(int a){x = a;}
    int get_x(){return x;}
private:
    int x;
};

class B: public A
{
public:
    void set_y(int a){y = a;}
    int get_y(){return y;}
private:
    int y;   
};

class C: public A
{
public:
    void set_z(int a){z = a;}
    int get_z(){return z;}
private:
    int z;
};

class D: public B, public C
{
    //......
};
```

上面这个例子即为典型的菱形继承结构，类 A 中的成员变量及成员函数继承到类 D 中均会**产生两份**，这样的命名冲突非常的棘手，通过域解析操作符已经无法分清这些成员分别来自谁了。为此，C&#43;&#43; 提供了**虚继承**这一方式来解决这种情况下的命名冲突问题。虚继承只需要在中间类（在这个例子中是B、C类）的继承属性前加上 `virtual` 关键字，该关键字后面的类被称为虚基类。

```cpp
class A
{
public:
    void set_x(int a){x = a;}
    int get_x(){return x;}
private:
    int x;
};

class B: virtual public A
{
public:
    void set_y(int a){y = a;}
    int get_y(){return y;}
private:
    int y;   
};

class C: virtual public A
{
public:
    void set_z(int a){z = a;}
    int get_z(){return z;}
private:
    int z;
};

class D: public B, public C
{
    //......
};
```

在本例中，B 和 C 都以虚继承的方式继承虚基类 A，如此操作之后，类 D 只会得到**一份来自类 A 的数据**，这样就解决了刚刚棘手的命名冲突问题。

&lt;span style=&#34;color: red;&#34;&gt;**注意：**&lt;/span&gt;在这一小节和上一小节中，我们只是简单的介绍了一下多继承和命名冲突时的解决办法，但也可以看到，使用多继承经常会出现二义性问题，必须十分小心。上面的例子是简单的，如果继承的层次再多一些，关系更复杂一些，程序的编写、调试和维护工作都会变得更加困难，因此**不提倡在程序中使用多继承，只有在比较简单和不易出现二义性的情况或实在必要时才使用多继承，能用单一继承解决的问题就不要使用多继承**。

### 向上转型和向下转型

在 `C/C&#43;&#43;` 中经常会发生数据类型的转换，例如将 `int` 类型的数据赋值给 `float` 类型的变量时，编译器会先把 `int` 类型的数据转换为 `float` 类型再赋值；反过来，`float` 类型的数据在经过类型转换后也可以赋值给 `int` 类型的变量。

**类其实也是一种数据类型**，也可以发生数据类型转换，不过这种转换只有在基类和派生类之间才有意义。「将派生类赋值给基类称为**向上转型**（Upcasting），将基类赋值给派生类称为**向下转型**（Downcasting）」。

向上转型非常安全，可以由编译器自动完成；向下转型有风险，需要程序员手动干预。所以，在实际项目中向下转型的使用频率远远小于向上转型，因此，我们在这里只详细介绍向上转型，向下转型请自己查阅资料学习。

#### 将派生类对象赋值给基类对象

直接看例子：

```cpp
#include &lt;iostream&gt;
using namespace std;

//基类
class A
{
public:
    A(int a):_a(a){};
public:
    void display();
public:
    int _a;
};
void A::display()
{
    cout &lt;&lt; &#34;Class A: a=&#34; &lt;&lt; _a &lt;&lt; endl;
}

//派生类
class B: public A{
public:
    B(int a, int b):A(a), _b(b){};
public:
    void display();
public:
    int _b;
};
void B::display()
{
    cout &lt;&lt; &#34;Class B: a=&#34; &lt;&lt; _a &lt;&lt; &#34;, b=&#34; &lt;&lt; _b &lt;&lt; endl;
}


int main(){
    A a(10);
    B b(66, 99);

    cout &lt;&lt; &#34;赋值前&#34; &lt;&lt; endl;
    a.display();
    b.display();

    cout&lt;&lt;&#34;------------------------&#34;&lt;&lt;endl;

    a = b;
    cout &lt;&lt; &#34;赋值后&#34; &lt;&lt; endl;
    a.display();
    b.display();

    return 0;
}

/*
运行结果为：
赋值前
Class A: a=10
Class B: a=66, b=99
------------------------
赋值后
Class A: a=66
Class B: a=66, b=99

 */
```

在本例中我们分别定义了基类对象`a`和派生类对象`b`，`a`的内存空间只存储了`_a`的值，`b`的内存空间存储了`_a`和`_b`的值（通过对前面学习可知**对象的存储空间只存放非静态成员变量**）。

在赋值操作`a = b`后，`a`的内存空间中是否也会存在`_b`的值了呢？并没有，因为在`a`的内存空间的大小在定义时就已经确定好了，不会再更改了，`a`的内存空间只能存放`_a`的值，所以在进行赋值操作`a = b`时，会将`b`的内存空间中的`_a`的值赋值给`a`的内存空间中的`_a`。&lt;span style=&#34;color: red;&#34;&gt;即派生类对象赋值给基类对象时，会将派生类独有的成员变量值抛弃，只将派生类继承而来的成员变量值赋值给基类对象（因为基类对象只能存放这些值）&lt;/span&gt;。

有人会想：为什么赋值后`a`调用的`display()`函数仍然是A类的？这是因为赋值操作并不会改变`a`的类型，`a`仍然是A类对象，所以它调用的肯定是A类的`display()`函数。
___

#### 将派生类指针赋值给基类指针

修改上面的示例代码：

```cpp
#include &lt;iostream&gt;
using namespace std;

class A
{
public:
    A(int a):_a(a){};
    void display();
    int _a;
};
void A::display()
{
    cout &lt;&lt; &#34;Class A: a=&#34; &lt;&lt; _a &lt;&lt; endl;
}

class B
{
public:
    B(int b):_b(b){};
    void display();
    int _b;
};
void B::display()
{
    cout &lt;&lt; &#34;Class B: b=&#34; &lt;&lt; _b &lt;&lt; endl;
}

class C: public A, public B
{
public:
    C(int a, int b, int c):A(a), B(b), _c(c){};
    void display();
    int _c;
};
void C::display()
{
    cout &lt;&lt; &#34;Class C: a=&#34; &lt;&lt; _a &lt;&lt; &#34;, b=&#34; &lt;&lt; _b &lt;&lt;  &#34;, c=&#34; &lt;&lt; _c &lt;&lt; endl;
}

int main(){
    A *a = new A(3);
    B *b = new B(7);
    C *c = new C(10, 11, 12);

    cout &lt;&lt; &#34;赋值前&#34; &lt;&lt; endl;
    a-&gt;display();
    b-&gt;display();
    c-&gt;display();

    cout&lt;&lt;&#34;------------------------&#34;&lt;&lt;endl;

    a = c;
    b = c;
    cout &lt;&lt; &#34;赋值后&#34; &lt;&lt; endl;
    a-&gt;display();
    b-&gt;display();
    c-&gt;display();

    cout &lt;&lt; &#34;a的地址为&#34; &lt;&lt; a &lt;&lt; endl;
    cout &lt;&lt; &#34;b的地址为&#34; &lt;&lt; b &lt;&lt; endl;
    cout &lt;&lt; &#34;c的地址为&#34; &lt;&lt; c &lt;&lt; endl;
    return 0;
}

/* 
运行结果如下：
赋值前
Class A: a=3
Class B: b=7
Class C: a=10, b=11, c=12
------------------------
赋值后
Class A: a=10
Class B: b=11
Class C: a=10, b=11, c=12
a的地址为0x6b1aa0
b的地址为0x6b1aa4
c的地址为0x6b1aa0
*/
```

该例子是一个多继承的例子，C类分别继承于A类和B类，看完这个例子后，你可能会有两个疑问：为什么赋值后A类和B类指针调用的还是自己的`display()`函数？为什么赋值操作后只有a和c存储的地址值相同，b的值要大于a、c的值？让我们依次来思考解决这两个问题。

**1.为什么赋值后A类和C类指针调用的还是自己的`display()`函数？**  
对于这个问题，首先要明白成员函数是属于类的而不是类对象，类对象空间并不存储成员函数。在进行 `a = c; b = c;` 操作后，`a`和`b`的类型并没有改变，只是指向了派生类C的对象而已。所以，执行`a-&gt;display(); b-&gt;display();`操作时，编译器会去对应的A类和B类中找到`display()`来执行。`a`，`b`指向C类对象，调用`display()`时传入的`this`肯定是指向C类对象的，所以最终在`display()`中使用的是C类对象存储的成员变量值。

&lt;span style=&#34;color: red;&#34;&gt;简单概括就是：编译器通过指针类型来访问成员函数（虚函数除外，后面会讲），通过指针指向的对象来访问成员变量&lt;/span&gt;

**2.为什么赋值操作后只有a和c存储的地址值相同，b的值要大于a、c的值？**  
这是因为在 C&#43;&#43; 中，当一个类通过多重继承从多个基类派生时，派生类对象的内存空间中包含了所有基类的成员变量。每个基类的成员在内存空间中占据不同的位置。为了 &lt;span style=&#34;color: red;&#34;&gt;**「使基类指针能够正确指向其对应的基类成员变量，编译器在进行指针转换时需要进行地址调整」**&lt;/span&gt;。

本例的`c`的内存空间如下图所示：  
![图4](/PostsImgs/Cplusplus_learning_note_imgs/picture4.png)

执行`a = c;`操作时，因为`A`类成员变量的起始位置就是`0x6b1aa0`，无需调整，所以 `a`的值与`c`的值一样；执行`b = c;`操作时，编译器发现`B`类成员变量的起始位置并不是`0x6b1aa0`，所以会将`0x6b1aa0`加上对应偏移量调整为`0x6b1aa4`后再赋值给`b`。所以，`b`的值大于`a`、`c`的值。

派生类存储基类成员的顺序与声明继承关系时的顺序一致。在下面的例子中就会先存储`_b`再存储`_a`，使得`c`和`b`的值相同，而`a`的值大于它们两的值。

```cpp
class C: public B, public A
{
public:
    C(int a, int b, int c):A(a), B(b), _c(c){};
    void display();
    int _c;
};
```

___

#### 将派生类引用赋值给基类引用

引用在本质上是通过指针的方式实现的，既然基类的指针可以指向派生类的对象，那么我们就有理由推断：基类的引用也可以指向派生类的对象，并且**它的表现和指针是类似的**。

修改前面的代码，验证推断正确：

```cpp
#include &lt;iostream&gt;
using namespace std;

class A
{
public:
    A(int a):_a(a){};
    void display();
    int _a;
};
void A::display()
{
    cout &lt;&lt; &#34;Class A: a=&#34; &lt;&lt; _a &lt;&lt; endl;
}

class B
{
public:
    B(int b):_b(b){};
    void display();
    int _b;
};
void B::display()
{
    cout &lt;&lt; &#34;Class B: b=&#34; &lt;&lt; _b &lt;&lt; endl;
}

class C: public A, public B
{
public:
    C(int a, int b, int c):A(a), B(b), _c(c){};
    void display();
    int _c;
};
void C::display()
{
    cout &lt;&lt; &#34;Class C: a=&#34; &lt;&lt; _a &lt;&lt; &#34;, b=&#34; &lt;&lt; _b &lt;&lt;  &#34;, c=&#34; &lt;&lt; _c &lt;&lt; endl;
}

int main(){
    C c(10, 11, 12);

    A &amp;a = c;
    B &amp;b = c;

    a.display();
    b.display();
    c.display();

    return 0;
}

/*
运行结果：
Class A: a=10
Class B: b=11
Class C: a=10, b=11, c=12
*/
```

## 多态与虚函数

面向对象程序设计语言有 **「封装、继承和多态」** 三种机制，这三种机制能够有效提高程序的可读性、可重用性和可扩展性。前面已经学习过封装、继承了，我们将在这一节介绍多态。

### 多态的简单介绍

&#34;多态&#34;按字面来说就是「多种形态」，指的是同一名字的事物有多种形态，可以完成不同的功能。

多态可以分为**编译时的多态和运行时的多态**。前者主要是指函数的重载（包括运算符的重载）、对重载函数的调用，在编译时就能根据实参确定调用哪个函数，因此叫编译时的多态；而后者则和继承、虚函数等概念有关，是本章要讲述的内容，在后面提及的多态都是指**运行时的多态**。

那多态有什么用呢？在向上转型的示例中，我们发现将派生类的对象指针赋值给基类对象的指针后，基类对象的指针只能使用派生类的成员变量，而不能使用派生类的成员函数。这并不符合人们的思维习惯，因为我们在直观上认为：**如果指针指向了派生类对象，那么就应该使用派生类的成员变量和成员函数**。而多态就可以实现这样的效果，**使得基类指针指向派生类对象时调用的是派生类的成员函数**。

要想形成多态必须具备以下三个条件：

1. 存在继承关系
2. 继承关系中必须有&lt;span style=&#34;color: red;&#34;&gt;**函数签名相同的虚函数**&lt;/span&gt;
3. 存在基类类型的指针或引用，通过该指针或引用调用虚函数

下一小节将会详细介绍为什么必须具备这三个条件才能形参多态。

___

多态的使用场景是什么呢？来看下面这个例子，我们假设你正在玩一款军事游戏，敌人突然发动了地面战争，于是你命令陆军、空军及其所有现役装备进入作战状态。具体的代码如下所示：

```cpp
#include &lt;iostream&gt;
using namespace std;

//军队
class Troops
{
public:
    virtual void fight(){ cout&lt;&lt;&#34;Strike back!&#34;&lt;&lt;endl; }
};

//陆军
class Army: public Troops
{
public:
    void fight(){ cout&lt;&lt;&#34;--Army is fighting!&#34;&lt;&lt;endl; }
};

//99A主战坦克
class _99A: public Army
{
public:
    void fight(){ cout&lt;&lt;&#34;----99A(Tank) is fighting!&#34;&lt;&lt;endl; }
};

//武直10武装直升机
class WZ_10: public Army
{
public:
    void fight(){ cout&lt;&lt;&#34;----WZ-10(Helicopter) is fighting!&#34;&lt;&lt;endl; }
};

//长剑10巡航导弹
class CJ_10: public Army
{
public:
    void fight(){ cout&lt;&lt;&#34;----CJ-10(Missile) is fighting!&#34;&lt;&lt;endl; }
};

//空军
class AirForce: public Troops
{
public:
    void fight(){ cout&lt;&lt;&#34;--AirForce is fighting!&#34;&lt;&lt;endl; }
};

//J-20隐形歼击机
class J_20: public AirForce
{
public:
    void fight(){ cout&lt;&lt;&#34;----J-20(Fighter Plane) is fighting!&#34;&lt;&lt;endl; }
};
//CH5无人机

class CH_5: public AirForce
{
public:
    void fight(){ cout&lt;&lt;&#34;----CH-5(UAV) is fighting!&#34;&lt;&lt;endl; }
};
//轰6K轰炸机
class H_6K: public AirForce
{
public:
    void fight(){ cout&lt;&lt;&#34;----H-6K(Bomber) is fighting!&#34;&lt;&lt;endl; }
};

int main(){
    Troops *p = new Troops;
    p -&gt;fight();
    //陆军
    p = new Army;
    p -&gt;fight();
    p = new _99A;
    p -&gt; fight();
    p = new WZ_10;
    p -&gt; fight();
    p = new CJ_10;
    p -&gt; fight();

    //空军
    p = new AirForce;
    p -&gt; fight();
    p = new J_20;
    p -&gt; fight();
    p = new CH_5;
    p -&gt; fight();
    p = new H_6K;
    p -&gt; fight();

    return 0;
}
```

这个例子中的派生类比较多，如果不使用多态，那么就需要定义多个指针变量，很容易造成混乱；而有了多态，只需要一个指针变量 p 就可以调用所有派生类的虚函数。

从这个例子中也可以发现，对于具有复杂继承关系的大中型程序，多态可以增加其灵活性，让代码更具有表现力。

### 虚函数

虚函数指的就是虚成员函数（**构造函数和静态成员函数除外**），因为普通函数（非成员函数）是无法声明为虚函数的。而普通函数无法声明为虚函数的原因是 **「虚函数的作用就是形成多态」**，而形成多态的第一个条件就是存在继承关系，普通函数很明显是不存在继承关系的，所以无法声明为虚函数。

只需在声明成员函数时在函数的返回值前使用 **`virtual`** 关键字，就可将一个成员函数声明为虚函数。跟静态成员函数一样，在类外定义虚函数时无需再加上`virtual`关键字。虚函数被派生类继承后仍然是虚函数，当你需要重写继承的虚函数时，只能重写其函数体，而不能修改其函数签名（即返回值、函数名、参数列表）。且建议在该虚函数的参数列表后使用 **`override`** 关键字，告诉编译器此函数是重写的虚函数，以便编译器在编译时检查重写是否出错。如果某个基类不允许派生类重写某个虚函数时，使用关键字 **`final`** 来实现。

直接来看例子：

```cpp
#include&lt;iostream&gt;
using namespace std;

class Base
{
public:
    virtual void display(); // 将此函数声明为虚函数
};

class Derived: public Base
{
public:
    void display() override; //使用 override 关键字告诉编译器此函数是重写基类的虚函数

};

void Base::display()
{
    cout &lt;&lt; &#34;I&#39;m Base class!&#34; &lt;&lt; endl;
}

void Derived::display()
{
    cout &lt;&lt; &#34;I&#39;m Derived class!&#34; &lt;&lt; endl;
}

int main()
{
    Base *Bp = new Base;
    Bp-&gt;display();

    Derived *Dp = new Derived;
    Dp-&gt;display();

    cout &lt;&lt; &#34;赋值后：&#34; &lt;&lt; endl;
    Bp = Dp;
    Bp-&gt;display();

    return 0;
}

/*
运行结果：
I&#39;m Base class!
I&#39;m Derived class!
赋值后：
I&#39;m Derived class!
*/
```

使用多态后，当我们将派生类指针赋值给基类指针后，可以发现该基类指针也可以调用派生类的成员函数了！

在这里再总结一下：**虚函数的唯一作用就是构成多态，而使用多态的目的就是使得基类指针（引用）能够访问派生类的成员函数**。

### 虚函数表vtable

在最开始时将派生类指针赋值给基类指针后，基类指针并不能访问派生类的成员函数，而使用虚函数后为什么就可以了呢？

这是因为使用了虚函数后，在创建对象时会额外地增加一个**虚函数表**和一个**虚函数表指针**，当通过基类指针调用虚函数时，程序会在运行时通过虚函数表指针找到该对象的虚函数表，在表中找到对应的虚函数的地址来调用该虚函数。

虚函数表简称 **`vtable`**，其本质就是一个**数组**，该数组存储了该类每一个虚函数的入口地址。虚函数表与对象是分开存储的，为了将虚函数表和对象关联起来，编译器会在对象中添加一个指针（**该指针放在内存空间起始位置**），该指针指向该对象的虚函数表的起始位置。

我们将通过下面这个例子来进行详细讲解：

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;
using namespace std;

class People
{
public:
    People(const string name, const int age): _name(name), _age(age){}
    virtual void display() const;
    virtual void studying();
protected:
    string _name;
    int _age;
};
void People::display() const
{
    cout &lt;&lt; &#34;Class People：&#34; &lt;&lt; _name &lt;&lt; &#34;今年&#34; &lt;&lt; _age &lt;&lt; &#34;岁了。&#34; &lt;&lt; endl;
}
void People::studying()
{
    cout &lt;&lt; &#34;Class People：我正在学习，请不要打扰我！！！&#34; &lt;&lt; endl;
}

class Student: public People
{
public:
    Student(const string name, const int age, const float score):People(name, age), _score(score){}
public:
    void display() const override;
    virtual void examing();
protected:
    float _score;
};
void Student::display() const
{
    cout &lt;&lt; &#34;Class Student：&#34; &lt;&lt; _name &lt;&lt; &#34;今年&#34; &lt;&lt; _age &lt;&lt; &#34;岁了，考了&#34; &lt;&lt; _score &lt;&lt; &#34;分。&#34; &lt;&lt; endl;
}
void Student::examing()
{
    cout &lt;&lt; &#34;Class Student：&#34; &lt;&lt; _name &lt;&lt; &#34;正在考试，请不要打扰Ta啊！&#34; &lt;&lt; endl;
}

class Senior: public Student
{
public:
    Senior(const string name, const int age, const float score, const bool hasJob):
        Student(name, age, score), _hasJob(hasJob){}
    void display() const override;
    virtual void partying();
private:
    bool _hasJob;
};

void Senior::display() const
{
    if(_hasJob)
    {
        cout &lt;&lt; &#34;Class Senior：&#34; &lt;&lt; _name &lt;&lt; &#34;以&#34; &lt;&lt; _score &lt;&lt; &#34;的成绩从大学毕业了，并找到了工作，Ta今年&#34; &lt;&lt; _age &lt;&lt; &#34;岁。&#34; &lt;&lt; endl;
    }
    else
    {
        cout &lt;&lt; &#34;Class Senior：&#34; &lt;&lt; _name &lt;&lt; &#34;以&#34; &lt;&lt; _score &lt;&lt; &#34;的成绩从大学毕业了，但没找到工作，Ta今年&#34; &lt;&lt; _age &lt;&lt; &#34;岁。&#34; &lt;&lt; endl;
    }
}
void Senior::partying()
{
    cout &lt;&lt; &#34;Class Senior：快毕业了，大家正在吃散伙饭...&#34; &lt;&lt; endl;
}

int main(){
    People *p = new People(&#34;李一&#34;, 29);
    p -&gt; display();

    p = new Student(&#34;李二&#34;, 16, 84.5);
    p -&gt; display();

    p = new Senior(&#34;李三&#34;, 24, 99.9, true);
    p -&gt; display();

    return 0;
}

/*
运行结果：
Class People：李一今年29岁了。
Class Student：李二今年16岁了，考了84.5分。
Class Senior：李三以99.9的成绩从大学毕业了，并找到了工作，Ta今年24岁。
*/
```

上面这个例子的各个类的对象的内存空间如下图所示：  
![图5](/PostsImgs/Cplusplus_learning_note_imgs/picture5.png)

仔细观察虚函数表，可以发现基类的虚函数在`vtable`中的**位置是固定的**，不会随着继承层次的增加而改变，如果派生类重写了基类的虚函数，那么将使用派生类的虚函数替换基类对应的虚函数，且派生类新增的虚函数会添加到虚函数表的底部。

当通过指针调用虚函数时，会先根据指针找到 `vptr`，再根据 `vfptr` 找到虚函数的入口地址。以虚函数 `display()` 为例，它在 `vtable` 中的索引为0，通过 `p -&gt; display();` 调用时，编译器内部会发生类似下面的转换：  
**`( *( *(p&#43;0) &#43; 0 ) )(p);`**

`0`是`vptr`在对象内存空间中的偏移，`p&#43;0`表示`vptr`的地址，`*(p&#43;0)`表示vptr的值，即虚函数表的起始地址。`display()`在虚函数表中的下标为`0`，所以`( *(p&#43;0) &#43; 0 )`就是`display()`的地址，`*( *(p&#43;0) &#43; 0 )(p)`就是对`display()`的调用，这里的p就是调用`display()`时使用的实参，它会赋值给`this`指针。

那么对应`p -&gt; studying();`就会被转换为 **`( *( *(p&#43;0) &#43; 1 ) )(p);`**

&lt;span style=&#34;color: red;&#34;&gt; **注意：** &lt;/span&gt;别忘了`p`是`People`对象的指针，它只能调用`display()`和`studying()`函数，虚函数只是使得它能调用派生类对应的成员函数。对于有需要的函数才将其设置为虚函数，其他函数请不要设置为虚函数，这会降低程序运行效率。

以上是针对单继承进行的讲解。当存在多继承时，虚函数表的结构就会变得复杂，尤其是有虚继承时，还会增加虚基类表，更加让人抓狂，这里我们就不分析了，有兴趣的读者可以自行研究。

### 虚析构函数

**构造函数是不能被声明为虚函数的**，因为调用构造函数时虚函数表尚不存在，此时无法通过查询虚函数表来找到要调用的函数是哪个。

析构函数的作用为在销毁对象时进行清理工作，此时虚函数表早已存在，所以可以声明为虚函数，而且有时候**必须要声明为虚函数**。

直接看例子：

```cpp
#include&lt;iostream&gt;
using namespace std;

class Base
{
public:
    Base();
    ~Base();
private:
    int * a;
};

Base::Base()
{
    cout &lt;&lt; &#34;Base constructor!&#34; &lt;&lt; endl;
    a = new int[10];
}

Base::~Base()
{
    cout &lt;&lt; &#34;Base destructor!&#34; &lt;&lt; endl;
    delete[] a;
}

class Derived: public Base
{
public:
    Derived(); // 这里没有显示调用基类构造函数，编译器会自动调用基类的默认构造函数
    ~Derived();
private:
    int * b;
};

Derived::Derived()
{
    cout &lt;&lt; &#34;Derived constructor!&#34; &lt;&lt; endl;
    b = new int[1000];
}

Derived::~Derived()
{
    cout &lt;&lt; &#34;Derived destructor!&#34; &lt;&lt; endl;
    delete[] b;
}

int main()
{
    Base *bp = new Derived;
    delete bp;
    cout &lt;&lt; &#34;-------------------------&#34; &lt;&lt; endl;
    Derived *dp = new Derived;
    delete dp;
    return 0;
}

/*
运行结果：
Base constructor!
Derived constructor!
Base destructor!
-------------------------
Base constructor!
Derived constructor!
Derived destructor!
Base destructor!
*/
```

在本例中，`bp`、`dp`分别表示基类指针和派生类指针，它们都指向派生类对象。最后使用`delete bp`只调用了基类的析构函数，没有调用派生类的析构函数，导致派生类申请的1000个`int`空间未被释放，造成了内存泄漏；使用`delete dp`则正确的先调用了派生类的析构函数，再调用基类的析构函数，申请的内存空间都正确释放掉了。

**1）为什么`delete bp`不会调用派生类的析构函数？**  
因为这里的析构函数不是虚函数，通过指针访问非虚函数时，编译器会根据指针类型来确定要调用的函数。所以，无论`bp`指向谁，调用的始终都是基类的析构函数。

**2）为什么`delete dp`能够调用基类的析构函数？**  
在[前面](#chapter-3)我们已经说了，在派生类的析构函数中编译器会自动调用基类的析构函数，且调用顺序为派生类的析构函数→基类的析构函数。

哪该怎么让基类指针指向派生类对象时，执行`delete bp`可以调用派生类的析构函数呢？没错，就是**将基类的析构函数声明为虚函数**，这样派生类的析构函数也会自动成为虚函数。这个时候编译器就会根据指针的指向来调用析构函数啦！修改上面的示例，将基类析构函数声明为虚函数：

```cpp
class Base
{
public:
    Base();
    virtual ~Base();  // 只修改了这里
private:
    int * a;
};

/*
运行结果：
Base constructor!
Derived constructor!
Derived destructor!
Base destructor!
-------------------------
Base constructor!
Derived constructor!
Derived destructor!
Base destructor!
*/
```

在实际开发中，自己定义析构函数就是希望在对象销毁时用它来进行清理工作，比如释放内存、关闭文件等，如果这个类又是一个基类，那么我们就必须将该析构函数声明为虚函数，否则就有内存泄露的风险。也就是说，&lt;span style=&#34;color: red;&#34;&gt;大部分情况下都应该将**基类**的析构函数声明为虚函数&lt;/span&gt;。

### 纯虚函数和抽象类

在C&#43;&#43;中，可以将虚函数声明为纯虚函数，语法为：  
**`virtual 返回值 函数名(参数列表) = 0;`**

纯虚函数指的就是**没有函数体**的虚函数，包含纯虚函数的类称为**抽象类**。之所以说它抽象，是因为它无法实例化。原因很明显，纯虚函数没有函数体，不是完整的函数，无法调用，也无法为其分配内存空间。

抽象类通常是作为**基类**，让派生类去实现纯虚函数，**派生类必须实现纯虚函数才能被实例化**。一般将虚函数声明为纯虚函数的情况有：  

1. 该函数并不是基类所需要的，但派生类必须要实现
2. 基类无法实现该函数的功能，需要派生来实现

### typeid运算符

在多态环境下，如果你需要在运行时检查某个对象的实际类型，可以使用 `typeid` 来实现。这在处理复杂的继承层次或实现特定逻辑时可能有用。

```cpp
if (typeid(*basePtr) == typeid(Derived)) {
    // Perform some operation specific to Derived type
}
```

## 运算符重载

### 运算符重载基础教程

运算符重载也被称为操作符重载，和函数重载一样，运算符重载的目的是 **「使得同一个运算符具有多种功能」**。运算符重载是一种优雅的对象操作技术，通过重载运算符，可以让自定义类型的使用更加自然和符合直觉。

实际上，我们已经在不知不觉中使用了运算符重载。例如，`&#43;`号可以对不同类型（int、float 等）的数据进行加法操作；`&lt;&lt;` 既是位移运算符，又可以配合 `cout` 向控制台输出数据。C&#43;&#43; 本身已经对这些运算符进行了重载。C&#43;&#43; 也允许程序员自己重载运算符，这给我们带来了很大的便利。

运算符重载其实就是**定义一个函数**，在函数体内实现想要的功能，当用到该运算符时，编译器会自动调用这个函数。所以，&lt;span style=&#34;color: red;&#34;&gt;**运算符重载的本质就是函数重载**&lt;/span&gt;。

**运算符重载的语法为：**

```cpp
返回值类型 operator运算符(参数列表)
{
    ......
}
```

**运算符重载的基本规则为：**

1. 只能重载已经存在的运算符，不能创造新的运算符（比如 @）。
2. 至少有一个操作数必须是用户定义的类型（防止重载基本数据类型的运算符）。
3. 不能违法运算符原来的语法规则。例如%本来有两个操作数，不能重载为只有一个。
4. 不能修改运算符优先级。
5. 不能重载以下运算符：`::`（作用域解析）、`.` `*`（指向成员的指针）、`sizeof`（对象或类型的大小）、`typeid`（对象或类型的信息）、`?:`（三目运算符）

**运算符重载的方式：**

运算符可以通过成员函数或非成员函数（通常是友元函数）来重载。一般情况下，选择任意一种方式都行，但单目运算符最好重载为成员函数，双目运算符最好重载为友元函数。

但这些运算符只能通过**成员函数**来重载： **`=`**、**`()`**、**`[]`**、**`→`**。

例如重载了 `&#43;` 操作符时， 对于 **`a &#43; b`** 不同的重载方法等价的结果不同

* **重载为成员函数**：`a &#43; b` 等价于 `a.operator&#43;(b)`
* **重载为友元函数**：`a &#43; b` 等价于 `operator&#43;(a, b)`

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;推荐双目运算符重载为友元函数的原因&lt;/summary&gt;

对于一个时间类的对象a来说 `a * 3.7` 和 `3.7 * a` 是等价的，但是如果将 `*` 重载为该类的成员函数， `3.7 * a` 是无法正常处理的。

&lt;/details&gt;

下面我们将举一些重载运算符的示例，其他运算符如：`new`、`delete`、`()`等也可以重载，有需要时请自行了解。

### 重载数学运算符

四则运算符（&#43;、-、*、/、&#43;=、-=、*=、/=）和关系运算符（&gt;、&lt;、&lt;=、&gt;=、==、!=）都是数学运算符，它们在实际开发中非常常见，被重载的几率也很高，并且有着相似的重载格式。本节以复数类 `Complex` 为例对它们进行重载，重在演示运算符重载的语法以及规范。

复数能够进行完整的四则运算，但不能进行完整的关系运算：我们只能判断两个复数是否相等，但不能比较它们的大小，所以在该类中不能对 `&gt;`、`&lt;`、`&lt;=`、`&gt;=` 进行重载。下面是具体的代码：

```cpp
#include &lt;iostream&gt;
#include &lt;cmath&gt;
using namespace std;

class Complex{
public:
    Complex(double real = 0.0, double imag = 0.0): _real(real), _imag(imag){ }
public:  // 运算符重载
    // 以友元函数的形式重载
    friend Complex operator&#43;(const Complex &amp;c1, const Complex &amp;c2);
    friend Complex operator-(const Complex &amp;c1, const Complex &amp;c2);
    friend Complex operator*(const Complex &amp;c1, const Complex &amp;c2);
    friend Complex operator/(const Complex &amp;c1, const Complex &amp;c2);
    friend bool operator==(const Complex &amp;c1, const Complex &amp;c2);
    friend bool operator!=(const Complex &amp;c1, const Complex &amp;c2);

    // 以成员函数的形式重载
    Complex &amp; operator&#43;=(const Complex &amp;c);
    Complex &amp; operator-=(const Complex &amp;c);
    Complex &amp; operator*=(const Complex &amp;c);
    Complex &amp; operator/=(const Complex &amp;c);
public:  //成员函数
    double get_real() const{ return _real; }
    double get_imag() const{ return _imag; }
private:
    double _real;  // 实部
    double _imag;  // 虚部
};

Complex operator&#43;(const Complex &amp;c1, const Complex &amp;c2)
{
    Complex c;
    c._real = c1._real &#43; c2._real;
    c._imag = c1._imag &#43; c2._imag;
    return c;
}

Complex operator-(const Complex &amp;c1, const Complex &amp;c2)
{
    Complex c;
    c._real = c1._real - c2._real;
    c._imag = c1._imag - c2._imag;
    return c;
}

// (a&#43;bi) * (c&#43;di) = (ac-bd) &#43; (bc&#43;ad)i
Complex operator*(const Complex &amp;c1, const Complex &amp;c2)
{
    Complex c;
    c._real = c1._real * c2._real - c1._imag * c2._imag;
    c._imag = c1._imag * c2._real &#43; c1._real * c2._imag;
    return c;
}

// (a&#43;bi) / (c&#43;di) = [(ac&#43;bd) / (c²&#43;d²)] &#43; [(bc-ad) / (c²&#43;d²)]i
Complex operator/(const Complex &amp;c1, const Complex &amp;c2)
{
    Complex c;
    c._real = (c1._real * c2._real &#43; c1._imag * c2._imag) / (pow(c2._real, 2) &#43; pow(c2._imag, 2));
    c._imag = (c1._imag * c2._real - c1._real * c2._imag) / (pow(c2._real, 2) &#43; pow(c2._imag, 2));
    return c;
}


bool operator==(const Complex &amp;c1, const Complex &amp;c2)
{
    if( c1._real == c2._real &amp;&amp; c1._imag == c2._imag )
    {
        return true;
    }
    else
    {
        return false;
    }
}

bool operator!=(const Complex &amp;c1, const Complex &amp;c2)
{
    if( c1._real != c2._real || c1._imag != c2._imag )
    {
        return true;
    }
    else
    {
        return false;
    }
}

Complex &amp; Complex::operator&#43;=(const Complex &amp;c)
{
    this-&gt;_real &#43;= c._real;
    this-&gt;_imag &#43;= c._imag;
    return *this;
}

Complex &amp; Complex::operator-=(const Complex &amp;c)
{
    this-&gt;_real -= c._real;
    this-&gt;_imag -= c._imag;
    return *this;
}

Complex &amp; Complex::operator*=(const Complex &amp;c)
{
    this-&gt;_real = this-&gt;_real * c._real - this-&gt;_imag * c._imag;
    this-&gt;_imag = this-&gt;_imag * c._real &#43; this-&gt;_real * c._imag;
    return *this;
}

Complex &amp; Complex::operator/=(const Complex &amp;c)
{
    this-&gt;_real = (this-&gt;_real*c._real &#43; this-&gt;_imag*c._imag) / (pow(c._real, 2) &#43; pow(c._imag, 2));
    this-&gt;_imag = (this-&gt;_imag*c._real - this-&gt;_real*c._imag) / (pow(c._real, 2) &#43; pow(c._imag, 2));
    return *this;
}

int main()
{
    Complex c1(25, 35);
    Complex c2(10, 20);
    Complex c3(1, 2);
    Complex c4(4, 9);
    Complex c5(34, 6);
    Complex c6(80, 90);

    Complex c7 = c1 &#43; c2;
    Complex c8 = c1 - c2;
    Complex c9 = c1 * c2;
    Complex c10 = c1 / c2;

    cout &lt;&lt; &#34;c7 = &#34; &lt;&lt; c7.get_real() &lt;&lt; &#34; &#43; &#34; &lt;&lt; c7.get_imag() &lt;&lt; &#34;i&#34; &lt;&lt; endl;
    cout &lt;&lt; &#34;c8 = &#34; &lt;&lt; c8.get_real() &lt;&lt; &#34; &#43; &#34; &lt;&lt; c8.get_imag() &lt;&lt; &#34;i&#34; &lt;&lt;endl;
    cout &lt;&lt; &#34;c9 = &#34; &lt;&lt; c9.get_real() &lt;&lt; &#34; &#43; &#34; &lt;&lt; c9.get_imag() &lt;&lt; &#34;i&#34; &lt;&lt;endl;
    cout &lt;&lt; &#34;c10 = &#34;&lt;&lt; c10.get_real() &lt;&lt; &#34; &#43; &#34; &lt;&lt; c10.get_imag() &lt;&lt; &#34;i&#34; &lt;&lt;endl;

    c3 &#43;= c1;
    c4 -= c2;
    c5 *= c2;
    c6 /= c2;
    cout &lt;&lt; &#34;c3 = &#34; &lt;&lt; c3.get_real() &lt;&lt; &#34; &#43; &#34; &lt;&lt; c3.get_imag() &lt;&lt; &#34;i&#34; &lt;&lt; endl;
    cout &lt;&lt; &#34;c4 = &#34; &lt;&lt; c4.get_real() &lt;&lt; &#34; &#43; &#34; &lt;&lt; c4.get_imag() &lt;&lt; &#34;i&#34; &lt;&lt; endl;
    cout &lt;&lt; &#34;c5 = &#34; &lt;&lt; c5.get_real() &lt;&lt; &#34; &#43; &#34; &lt;&lt; c5.get_imag() &lt;&lt; &#34;i&#34; &lt;&lt; endl;
    cout &lt;&lt; &#34;c6 = &#34; &lt;&lt; c6.get_real() &lt;&lt; &#34; &#43; &#34; &lt;&lt; c6.get_imag() &lt;&lt; &#34;i&#34; &lt;&lt; endl;

    if(c1 == c2)
    {
        cout &lt;&lt; &#34;c1 == c2&#34; &lt;&lt; endl;
    }
    if(c1 != c2)
    {
        cout &lt;&lt; &#34;c1 != c2&#34; &lt;&lt; endl;
    }

    return 0;
}

/*
运行结果：
c7 = 35 &#43; 55i
c8 = 15 &#43; 15i
c9 = -450 &#43; 850i
c10 = 1.9 &#43; -0.3i
c3 = 26 &#43; 37i
c4 = -6 &#43; -11i
c5 = 220 &#43; 4460i
c6 = 5.2 &#43; 1.592i
c1 != c2
*/
```

在这个例子中我们以友元函数的形式重载了 `&#43;、-、*、/、==、!=`，以成员函数的形式重载了 `&#43;=、-=、*=、/=`，而且应该坚持这样做，不能一股脑都写作成员函数或者全局函数。

### 重载 `&lt;&lt;` 和 `&gt;&gt;`

在C&#43;&#43;中，标准库本身已经对左移运算符`&lt;&lt;`和右移运算符`&gt;&gt;`分别进行了重载，使其能够用于不同数据的输入输出，但是输入输出的对象只能是 C&#43;&#43; 内置的数据类型（例如 bool、int、double 等）和标准库所包含的类类型（例如 string、complex、ofstream、ifstream 等）。如果我们自己定义了一种新的数据类型，需要用输入输出运算符去处理，那么就必须对它们进行重载。

`cout` 是 `ostream` 类的对象，`cin` 是 `istream` 类的对象，要想达到这个目标，就必须以友元函数的形式重载`&lt;&lt;`和`&gt;&gt;`，否则就要修改标准库中的类，这显然不是我们所期望的。

在刚刚的`Complex`类添加以下两个成员函数：

```cpp
class Complex
{
    ......
    friend istream &amp; operator&gt;&gt;(istream &amp; in, complex &amp; A) // 返回引用是以便可以进行连续读入操作，如 cin &gt;&gt; a &gt;&gt; b;
    {
        in &gt;&gt; A.real &gt;&gt; A.imag;
        return in;
    }
    friend ostream &amp; operator&lt;&lt;(ostream &amp; out, complex &amp; A) // 同样返回引用是以便进行连续输出操作
    {
        out &lt;&lt; A.real &lt;&lt;&#34; &#43; &#34;&lt;&lt; A.imag &lt;&lt;&#34; i &#34;;
        return out;
    }
};
```

### 重载`[]`

C&#43;&#43; 规定，下标运算符`[ ]`必须以**成员函数**的形式进行重载。该重载函数在类中的声明格式如下：  
**`返回值类型 &amp; operator[ ] (参数);`**  
或者：  
**`const 返回值类型 &amp; operator[ ] (参数) const;`**

在实际开发中，我们应该同时提供这两种以上两种形式，这样做是为了适应`const`对象，因为`const`对象只能调用`const`成员函数。

**注意：** 当你需要自定义类的下标访问行为时，才重载`[]`，否则无需重载该运算符。

### 重载`&#43;&#43;`和`--`

自增`&#43;&#43;`和自减`--`都是一元运算符，它的前置形式（类似`&#43;&#43;i`）和后置形式（类似`i&#43;&#43;`）都可以被重载。前置形式的重载参数列表为空，且返回对象的引用；后置形成的重载参数列表为一个`int`变量（一般命名为`dummy`，无实际作用，只是为了区分前置版本）、且返回操作前的对象副本。

如下所示：

```cpp
#include &lt;iostream&gt;
#include &lt;cmath&gt;
using namespace std;

class Counter
{
public:
    Counter(int value) : _count(value) {}

    // 自增前置版本
    Counter&amp; operator&#43;&#43;()
    {
        _count&#43;&#43;;
        return *this;
    }

    // 自增后置版本
    Counter operator&#43;&#43;(int dummy)
    {
        Counter temp = *this;  // 保存当前状态
        _count&#43;&#43;;
        return temp;  // 返回增加前的副本
    }

    // 自减前置版本
    Counter&amp; operator--()
    {
        _count--;
        return *this;
    }

    // 自减后置版本
    Counter operator--(int dummy)
    {
        Counter temp = *this;  // 保存当前状态
        _count--;
        return temp;  // 返回减少前的副本
    }

    friend ostream&amp; operator&lt;&lt; (ostream &amp;out, Counter &amp;c)
    {
        out &lt;&lt; c._count;
    }
private:
    int _count;
};

int main() 
{
    Counter c(1);

    cout &lt;&lt; &#34;start c =&#34; &lt;&lt; c &lt;&lt; endl;
    &#43;&#43;c;// 调用前置版本
    c&#43;&#43;;// 调用后置版本
    --c;
    c--;
    cout &lt;&lt; &#34;end c =&#34; &lt;&lt; c &lt;&lt; endl;
    return 0;
}
```

## 模板

 **泛型程序设计**是一种算法在实现时**不指定具体要操作的数据类型**的程序设计方法。所谓“泛型”，指的是 **「算法只要实现一遍，就能适用于多种数据类型」**。泛型程序设计方法的优势在于能够减少重复代码的编写。在C&#43;&#43;中泛型程序设计是使用模板来实现的，模板又分为函数模板和类模板。

### 函数模板

当我们需要编写多个**参数类型不同但完成的工作都相同**的函数时，可以使用函数模板。函数模板本身是一个**蓝图**，并不是函数，是编译器用来产生拥有具体类型参数的函数的模板（蓝图、设计图）。

比如，当我们想要编写 **`swap()`** 函数用于交换两个变量的值时，就需要为 `int`、`double`、`char`等变量类型都编写对应的 **`swap()`** 函数，这样效率是低下的、难以维护且不优雅，这时就可以使用函数模板来实现这一功能。

定义一个函数模板的基本语法如下：

```cpp
template &lt;typename T1, typename T2, ......&gt;
void functionName(T1 parameter1, T2 parameter2, ......)
{
    // 函数体
}
```

* **`template`** 是用于定义模板的关键字。
* **&lt;typename T1, typename T2&gt;** 定义模板参数`T1`和`T2`，跟函数参数一样（`T1`，`T2`是推荐的参数名，是可修改的）。`typename`可以使用`class`来替换，但不推荐。
* **`T1`、`T2`** 均是模板参数类型，在编写函数时使用，用于取代具体的数据类型。

例如，一个交换两个同类型变量的值的函数模板可以写为：

```cpp
template &lt;typename T&gt;
void swap(T &amp;a, T &amp;b)
 {
    T temp = a;
    a = b;
    b = temp;
}
```

**请注意：** 模板函数（指由函数模板生成的函数）与模板函数、模板函数与常规函数均能实现重载，请看示例代码：

```cpp
#include &lt;iostream&gt;

// 常规函数
void func(int x) {
    std::cout &lt;&lt; &#34;Non-template function called with int: &#34; &lt;&lt; x &lt;&lt; std::endl;
}

// 函数模板1
template&lt;typename T&gt;
void func(T x) {
    std::cout &lt;&lt; &#34;Template function1 called with value: &#34; &lt;&lt; x &lt;&lt; std::endl;
}

// 函数模板2
template&lt;typename T&gt;
void func(T x, int n) {
    std::cout &lt;&lt; &#34;Template function2 called with value: &#34; &lt;&lt; x &lt;&lt; std::endl;
}

int main() 
{
    func(10);       // 调用常规函数
    func(10.5);     // 调用由函数模板1实例化的模板函数
    func(10.3, 7);  // 调用由函数模板2实例化的模板函数
}
```

___

有时对于某种特定类型，我们不想使用通用的函数模板，而是想要有些**特定**的操作时，可以使用**显示具体化**来完成此要求。比如在上述例子中，对于两个结构体 `job` 变量`a`, `b`（该结构体包含 姓名、薪资、职位 成员），我们只想交换两个变量的薪资和职位的值，这时就可以采用显示具体化来实现，代码如下：

```cpp
typedef struct job
{
    string name;
    double salary;
    string position;
}job;

template &lt;typename T&gt;
void Swap(T &amp;a, T &amp;b)
{
    T temp = a;
    a = b;
    b = temp;
}

//显示具体化
template &lt;&gt; void Swap&lt;job&gt;(job &amp;j1, job &amp;j2)
{
    string position_temp = j1.position;
    double salary_temp = j1.salary;

    j1.position = j2.position;
    j1.salary = j2.salary;

    j2.position = position_temp;
    j2.salary = salary_temp;
}

int main()
{
    job a, b;
    ······
    Swap(a, b); //调用的是显示具体化创建的函数，只交换这两个变量的薪资和职位的值
}
```

### 类模板

C&#43;&#43; 除了支持函数模板，还支持**类模板**。函数模板中定义的类型参数可以用在函数声明和函数定义中，类模板中定义的类型参数可以用在**类声明和类实现中**。类模板的目的同样是将数据的类型参数化。

声明类模板的语法为：

```cpp
template &lt;typename T1, typename T2, ...&gt;
class 类名
{
    ...
};
```

一旦声明了类模板，就可以将类型参数用于成员变量和成员函数了。换句话说，就可以把`T1`、`T2`、...当作`int`、`double`等来用。

假如我们现在要定义一个类来表示坐标，要求坐标的数据类型可以是整数、小数和字符串，这个时候就可以使用类模板：

```cpp
template&lt;typename T1, typename T2&gt;
class Point
{
public:
    Point(T1 x, T2 y): _x(x), _y(y){ }
public:
    T1 get_x() const;  //获取x坐标
    void set_x(T1 x);  //设置x坐标
    T2 get_y() const;  //获取y坐标
    void set_y(T2 y);  //设置y坐标
private:
    T1 _x;  //x坐标
    T2 _y;  //y坐标
};
```

`x` 坐标和 `y` 坐标的数据类型不确定，借助类模板可以将数据类型参数化，这样就不必定义多个类了。

上面的代码仅仅是类的声明，我们还需要在类外定义成员函数。**在类外定义成员函数时仍需带上模板头**，格式为：

```cpp
template&lt;typename T1, typename T2, ...&gt;
返回值类型 类名&lt;类型参数1 , 类型参数2, ...&gt;::函数名(形参列表)
{
    ......
}
```

下面就对Point类的成员函数进行定义：

```cpp
template&lt;typename T1, typename T2&gt;  //模板头
T1 Point&lt;T1, T2&gt;::get_x() const
{
    return _x;
}

template&lt;typename T1, typename T2&gt;
void Point&lt;T1, T2&gt;::set_x(T1 x)
{
    _x = x;
}

template&lt;typename T1, typename T2&gt;
T2 Point&lt;T1, T2&gt;::get_y() const
{
    return _y;
}

template&lt;typename T1, typename T2&gt;
void Point&lt;T1, T2&gt;::set_y(T2 y)
{
    _y = y;
}
```

完成了类模板的编写就可以使用其来创建对象，将上面的代码综合起来再加上创建对象的代码就可以得到完整的代码：

```cpp
#include &lt;iostream&gt;
using namespace std;

template&lt;typename T1, typename T2&gt;
class Point
{
public:
    Point(T1 x, T2 y): _x(x), _y(y){ }
public:
    T1 get_x() const;  //获取x坐标
    void set_x(T1 x);  //设置x坐标
    T2 get_y() const;  //获取y坐标
    void set_y(T2 y);  //设置y坐标
private:
    T1 _x;  //x坐标
    T2 _y;  //y坐标
};

template&lt;typename T1, typename T2&gt;  //模板头
T1 Point&lt;T1, T2&gt;::get_x() const
{
    return _x;
}

template&lt;typename T1, typename T2&gt;
void Point&lt;T1, T2&gt;::set_x(T1 x)
{
    _x = x;
}

template&lt;typename T1, typename T2&gt;
T2 Point&lt;T1, T2&gt;::get_y() const
{
    return _y;
}

template&lt;typename T1, typename T2&gt;
void Point&lt;T1, T2&gt;::set_y(T2 y)
{
    _y = y;
}

int main()
{
    // 类模板实例化时必须指定具体的数据类型
    Point&lt;int, int&gt; p1(10, 20);
    cout &lt;&lt; &#34;x=&#34; &lt;&lt; p1.get_x() &lt;&lt; &#34;, y=&#34; &lt;&lt; p1.get_y() &lt;&lt; endl;

    Point&lt;int, char*&gt; p2(10, &#34;东经180度&#34;);
    cout &lt;&lt; &#34;x=&#34; &lt;&lt; p2.get_x() &lt;&lt; &#34;, y=&#34; &lt;&lt; p2.get_y() &lt;&lt; endl;

    // 实例化对象指针时，赋值号两边都要指明具体的数据类型，且要保持一致。
    Point&lt;char*, char*&gt; *p3 = new Point&lt;char*, char*&gt;(&#34;东经180度&#34;, &#34;北纬210度&#34;);
    cout &lt;&lt; &#34;x=&#34; &lt;&lt; p3-&gt;get_x() &lt;&lt; &#34;, y=&#34; &lt;&lt; p3-&gt;get_y() &lt;&lt; endl;

    return 0;
}

/*
运行结果：
x=10, y=20
x=10, y=东经180度
x=东经180度, y=北纬210度
*/
```

&lt;span style=&#34;color: red;&#34;&gt;**注意：**&lt;/span&gt;类模板实例化对象时必须指定具体的数据类型；实例化对象指针时必须在赋值号两边都指定具体的数据类型，具体请参考示例代码。

### decltype关键字（C&#43;&#43;11）

当我们在编写模板时，可能会遇到不知道该定义什么类型的变量的情况，如下所示：

```cpp
    template &lt;typename T1, typename T2&gt;
    void func(T1 x, T2, y)
    {
        ...
        xpy = x &#43; y; // 这里的 xpy该定义为什么类型呢？
        ...
    }
```

这里的 `xpy` 在定义时应该定义为什么类型呢？由于无法预先知道`x`，`y`的类型，所以 `xpy` 的类型也就无法确定。这时，就可以使用关键字 `decltype` 来解决此问题，其用法如下：  
**` decltype(expression) var_name = expression; `**

编译器会根据 `expression` 的运算结果正确推导出 `var_name` 的类型，所以上述代码可以改为：  
**`decltype(x &#43; y) xpy = x &#43; y;`**

___

我们对于模板的介绍就到此为止了，虽然模板还有很多内容，但在我们实际编程中，上面的模板知识大多数情况下都是够用的了。对模板感兴趣的可以自行查阅相关资料进行学习。

## STL（标准模板库）

STL（Standard Template Library）标准模板库是 C&#43;&#43; 标准库中的一部分，标准模板库为 C&#43;&#43; 提供了完善的数据结构及算法。

STL 标准模板库包括三部分：容器、算法和迭代器。容器是对象的集合，STL 的容器有：vector、stack、queue、deque、list、set 和 map 等。STL 算法是对容器进行处理，比如排序、合并等操作。迭代器则是访问容器的一种机制。

### 容器

容器用于存放数据的**类模板**。可变长数组、大根堆等数据结构在 STL 中都被实现为容器。使用容器时，即将容器类模板实例化为容器类时，会指明容器中存放的元素是什么类型的。

容器中可以存放基本类型的变量，也可以存放对象。对象或基本类型的变量被插入容器中时，实际插入的是对象或变量的一个**复制品**。

`STL` 中的许多算法（即函数模板），如排序、查找等算法，在执行过程中会对容器中的元素进行比较。这些算法在比较元素是否相等时通常用运算符进行，比较大小通常用 `&lt;` 运算符进行，因此，被放入容器的对象所属的类最好重载 **`==`和`&lt;`** 运算符，以使得两个对象用`==`和`&lt;`进行比较是有定义的。

容器可分为顺序容器和关联容器两种，同类型的容器对象可以使用`&lt;、&lt;=、&gt;、&gt;=、==、!=`进行比较。

#### 顺序容器

顺序容器的特点为：存储的元素在容器中的位置与元素值无关，元素在插入时可以指定插入位置。

常用的顺序容器只有三种：动态数组 `vector`、双端队列 `deque`、双向链表 `list`。
___

#### 关联容器

关联容器的特点为：默认情况下，存储的元素按照关键字从小到大进行排序（使用`&lt;`运算符来进行比较大小），插入时不能指定插入位置。因为是排好序的，所以关联容器在查找时具有非常好的性能。

常用的关联容器有：`set`、`multiset`、`map`、`multimap`。关联容器内的元素是排好序的。

除了以上两类容器外，`STL` 还在两类容器的基础上屏蔽一部分功能，突出或增加另一部分功能，实现了三种容器适配器：栈 `stack`、队列 `queue`、优先级队列 `priority_queue`。

为称呼方便起见，本教程后面将容器和容器适配器统称为容器。

### 迭代器

使用“迭代器（iterator）”是访问容器元素的通用方法。迭代器就是一个**变量**，指向容器中的某个元素，通过迭代器就可以读写它指向的元素。从这一点上看，迭代器和指针类似。

**迭代器按照定义方式可以分为以下四种：**

1. 正向迭代器：**`容器类名::iterator  迭代器名;`**
2. 常量正向迭代器：**`容器类名::const_iterator  迭代器名;`**
3. 反向迭代器：**`容器类名::reverse_iterator  迭代器名;`**
4. 常量反向迭代器：**`容器类名::const_reverse_iterator  迭代器名;`**

**注意：** 所有容器（不包括容器适配器）都能定义正向迭代器和常量正向迭代器，但不是所有容器都能定义反向迭代器和常量反向迭代器。

**`*迭代器名`** 就可访问迭代器指向的元素，通过非常量迭代器还能修改其指向的元素。

迭代器都可以进行`&#43;&#43;`操作，正向迭代器`&#43;&#43;`，就会指向后一个元素；反向迭代器`&#43;&#43;`就会指向前一个元素。

直接来看例子吧：

```cpp
#include &lt;iostream&gt;
#include &lt;vector&gt;
using namespace std;

int main()
{
    vector&lt;int&gt; v;  // 定义一个动态数组v

    for (int n = 0; n &lt; 5; &#43;&#43;n) v.push_back(n);  //push_back成员函数用于在容器尾部添加一个元素

    vector&lt;int&gt;::iterator i;  //定义正向迭代器

    for (i = v.begin(); i != v.end(); &#43;&#43;i) //用迭代器遍历容器
    {
        cout &lt;&lt; *i &lt;&lt; &#34; &#34;;  //*i 就是迭代器i指向的元素
        (*i) *= 2;  // 每个元素变为原来的2倍
    }
    cout &lt;&lt; endl;

    // 用反向迭代器遍历容器
    for (vector&lt;int&gt;::reverse_iterator j = v.rbegin(); j != v.rend(); &#43;&#43;j)
        cout &lt;&lt; *j &lt;&lt; &#34; &#34;;

    return 0;
}

/*
运行结果：
0 1 2 3 4
8 6 4 2 0
*/
```

___

迭代器按照功能强弱分为输入、输出、正向、双向、随机访问五种，这里是介绍常用的三种（假设p为对应类型的迭代器）。  
1）正向迭代器：支持`&#43;&#43;p`、`p&#43;&#43;`、`*p`操作。此外，两个正向迭代器可以互相赋值，还可以使用 `==` 和`!=`来进行比较。  
2）双向迭代器：除了支持正向迭代器的全部功能外，还支持`--p`和`p--`操作。  
3）随机访问迭代器：除了支持双向迭代器的全部功能外，还支持以下操作：  

* `p &#43;= i;` 使得 p 往后移动 i 个元素
* `p -= i;` 使得 p 往前移动 i 个元素
* `p &#43; i;` 返回 p 后面第 i 个元素的迭代器
* `p - i;` 返回 p 前面第 i 个元素的迭代器
* `p[i];` 返回 p 后面第 i 个元素的引用

下表为各个容器的迭代器功能：

| 容器           | 迭代器功能   |
| :------------- | :----------- |
| vector         | 随机访问     |
| string         | 随机访问     |
| deque          | 随机访问     |
| list           | 双向         |
| set/multiset   | 双向         |
| map/multimap   | 双向         |
| stack          | 不支持迭代器 |
| queue          | 不支持迭代器 |
| priority_queue | 不支持迭代器 |

**注意：** 容器适配器是没有迭代器的！！！

### STL算法

STL 提供能在各种容器中通用的算法（大约有70种），如插入、删除、查找、排序等。**算法就是函数模板**。算法通过迭代器来操纵容器中的元素。

许多算法操作的是容器上的一个区间（也可以是整个容器），因此需要两个参数，一个是区间起点元素的迭代器，另一个是区间终点元素的**后面一个元素的迭代器**。例如，排序和查找算法都需要这两个参数来指明待排序或待查找的区间。

有的算法返回一个迭代器。例如，`find` 算法在容器中查找一个元素，并返回一个指向该元素的迭代器。

有的算法会改变其所作用的容器。例如：
copy：将一个容器的内容复制到另一个容器。
remove：在容器中删除一个元素。
random_shuffle：随机打乱容器中的元素。
fill：用某个值填充容器。

有的算法不会改变其所作用的容器。例如：
find：在容器中查找元素。
count_if：统计容器中符合某种条件的元素的个数。

**算法可以处理容器，也可以处理普通的数组。**

STL 中的大部分常用算法都在头文件 **`algorithm`** 中定义。此外，头文件 `numeric` 中也有一些算法。

**`algorithm`下常用的函数：**

```cpp
max(x, y)
min(x, y)
abs(x) //x必须为int类型
fabs(x) //x可为浮点型
swap(x, y)
reverse(it1, it2)  //反转区间[it1, it2) 内的元素
fill(a, a &#43; len, 0) //将数组a的元素全部赋值为0，和memset功能一样
sort(a, a &#43; x, cmp) //cmp可选

//在[first, last)范围内寻找第一个值大于等于 val的元素的位置，如果是数组返回指针，容器返回迭代器
lower_bound(first, last, val) 
//在[first, last)范围内寻找第一个值大于val的元素的位置，如果是数组返回指针，容器返回迭代器
upper_bound(first, last, val)

next_permutation(a, a &#43; len) // 给出一个序列在全排列中的下一个序列，可以搭配 do while 来使用
```

### STL中的大、小和相等概念

STL 中关联容器内部的元素是排序的。STL 中的许多算法也涉及排序、查找。这些容器和算法都需要对元素进行比较，有的比较是否相等，有的比较元素大小。

在 STL 中，默认情况下，比较大小是通过**`&lt;`**运算符进行的，和`&gt;`运算符无关。在STL中提到“大”、“小”的概念时，以下三个说法是等价的：

* `x` 比 `y` 小。
* 表达式`x&lt;y`为真。
* `y` 比 `x` 大。

一定要注意，y比x大意味着`x&lt;y`为真，而不是`y&gt;x`为真。`y&gt;x`的结果如何并不重要，甚至`y&gt;x`是没定义的都没有关系。

在 STL 中，x和y相等也往往不等价于`x==y`为真。对于在未排序的区间上进行的算法，如顺序查找算法 find，查找过程中比较两个元素是否相等用的是==运算符；但是对于在排好序的区间上进行查找、合并等操作的算法（如折半查找算法 binary_search，关联容器自身的成员函数 find）来说，x和y相等是与`x&lt;y`和`y&lt;x`同时为假等价的，与==运算符无关。看上去`x&lt;y`和`y&lt;x`同时为假就应该和`x==y`为真等价，其实不然。例如下面的 class A：

```cpp
class A
{
    int v;
public:
    bool operator&lt; (const A &amp; a)const {return false;}
};
```

可以看到，对任意两个类 A 的对象 x、y，`x&lt;y`和`y&lt;x`都是为假的。也就是说，对 STL 的关联容器和许多算法来说，任意两个类 A 的对象都是相等的，这与`==`运算符的行为无关。

综上所述，使用 STL 中的**关联容器和许多算法**时，往往需要对`&lt;`运算符进行适当的重载，使得这些容器和算法可以用`&lt;`运算符对所操作的元素进行比较。最好将`&lt;`运算符重载为友元函数，因为在重载为成员函数时，在有些编译器上会出错（由其 STL 源代码的写法导致）。

### vector

`vector` 是顺序容器的一种，是可变长的动态数组，支持随机访问迭代器，所有 STL 算法都能对 `vector` 进行操作。要使用 `vector`，需要包含头文件 `vector`。

**注意：**`vector`数组是在**堆空间**开的。而普通数组如果是在局部区域内开（比如函数体内），则是在栈空间开的，栈空间较小。所以，如果在局部区域开的普通数组不易过大（不能超过$10^6$），而对`vector`数组则没有限制。

**`vector`的定义如下：**

```cpp
// 定义一个vector，初始化为空
vector&lt;typename&gt; name;  //typename为容器内元素类型，name为容器名称

// 定义一个vector，初始化有 n 个元素
vector&lt;typename&gt; name(n);

// 定义一个vector，初始化有 n 个元素，每个元素值均为 val
vector&lt;typename&gt; name(n, val);

// 使用其他迭代器来初始化 vector
vector&lt;typename&gt; name(frist, last);

// 和数组一样的初始化方式
vector&lt;int&gt; numbers1 = {1, 2, 3, 4, 5};
vector&lt;int&gt; numbers2{1, 2, 3, 4, 5};

// 拷贝初始化
vector&lt;int&gt; a(n, 0);
vector&lt;int&gt; b(a);
vector&lt;int&gt; c = a;

// 容器元素类型如果为容器时，定义如下
vector&lt; vector&lt;typename&gt; &gt; name; //类似二维数组，只不过是可变长的，&gt;&gt;之间需要有空格
```

**`vector`常用成员函数（vi为数组名称）：**

| 代码                  | 算法复杂度 | 返回值类型 | 含义                                                                                                                                                             |
| :-------------------- | :--------- | :--------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| vi.front()            | O(1)       | 引用       | 返回容器中的第一个数据                                                                                                                                           |
| vi.back()             | O(1)       | 引用       | 返回容器中的最后一个数据                                                                                                                                         |
| vi.at(idx)            | -          | 引用       | 返回`vi[idx]`, 会进行边界检查，如果越界会报错，比直接使用`[]`更好一些，常在项目中使用                                                                            |
| vi.size()             | O(1)       | -          | 返回实际数据个数（unsigned类型）                                                                                                                                 |
| vi.begin()            | O(1)       | 迭代器     | 返回首元素的迭代器                                                                                                                                               |
| vi.end()              | O(1)       | 迭代器     | 返回最后一个元素后一个位置的迭代器                                                                                                                               |
| vi.empty()            | O(1)       | bool       | 判断是否为空，为空返回真，反之返回假                                                                                                                             |
| vi.reserve(sz)        | -          | -          | 为数组提前分配`sz`的内存大小，即改变了`capacity`的大小，主要是为了防止在`push_back`过程中多次的内存拷贝                                                          |
| vi.assign(beg, end)   | -          | -          | 将另外一个容器`[x.begin(),x.end())`里的内容拷贝到`vi`中                                                                                                          |
| vi.assign(n, val)     | -          | -          | 将`n`个`val`值拷贝到`vi`数组中，这会清除掉容器中以前的内容，`vi`数组的`size`将变为`n`，`capacity`不会改变                                                        |
| vi.pop_back()         | O(1)       | -          | 删除最后一个数据                                                                                                                                                 |
| vi.push_back(element) | O(1)       | -          | 在尾部加一个数据                                                                                                                                                 |
| vi.emplace_back(ele)  | O(1)       | -          | 在数组中加入一个数据，和`push_back`功能基本一样，在某些情况下比它效率更高，支持传入多个构造参数                                                                  |
| vi.clear()            | O(N)       | -          | 清除容器中的所有元素                                                                                                                                             |
| vi.resize(n, v)       | -          | -          | 改变数组大小为`n`。如果`n`小于以前的大小，则保留前n个元素`n`，`v`参数不起作用；如果`n`大于以前的大小，则新增`n-pre`个元素，新增元素值为`v`，不指定`v`时默认为`0` |
| vi.insert(pos, x)     | O(N)       | -          | 向迭代器`pos`指向的位置插入元素`x`                                                                                                                               |
| vi.erase(first, end)  | O(N)       | -          | 删除 [first, end) 的所有元素                                                                                                                                     |

___

访问vector数组中的元素有三种方法：

1. 和普通数组一样，使用下标来访问
2. 使用迭代器
3. 使用基于范围的`for`循环（前面介绍过）

### list

`list` 是顺序容器的一种。`list` 是一个**双向链表**，使用 list 需要包含头文件 `list`。

`list` 的构造函数和许多成员函数的用法都与 `vector` 类似，此处不再列举。除了顺序容器都有的成员函数外，`list` 容器还独有如下表 所示的成员函数（此表不包含全部成员函数，且有些函数的参数较为复杂，表中只列出函数名）。

| 成员函数或成员函数模板                                               | 作用                                                                                                                                                    |
| :------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `void push_front(const T &amp;val)`                                      | 将 `val` 插入链表最前面                                                                                                                                 |
| `void pop_front()`                                                   | 删除链表最前面的元素                                                                                                                                    |
| `void sort()`                                                        | 将链表从小到大排序                                                                                                                                      |
| `void remove(const T &amp;val)`                                          | 删除和 `val` 相等的元素                                                                                                                                 |
| `remove_if`                                                          | 删除符合某种条件的元素                                                                                                                                  |
| `void unique()`                                                      | 删除所有和前一个元素相等的元素                                                                                                                          |
| `void merge(list&lt;T&gt; &amp;x)`                                             | 将链表 `x` 合并进来并清空 `x`。要求链表自身和 `x` 都是有序的                                                                                            |
| `void splice(iterator i, list&lt;T&gt; &amp;x, iterator first, iterator last)` | 在位置 `i` 前面插入链表 `x` 中的区间 `[first,last)`,并在链表 `x` 中删除该区间。链表自身和链表 `x` 可以是同一个链表，只要 `i` 不在 `[first,last)` 中即可 |

STL 中的算法 `sort` 可以用来对 `vector` 和 `deque` 排序，它需要**随机访问迭代器**的支持。因为 `list` 不支持随机访问迭代器，所以不能用算法 `sort` 对 `list` 容器排序。因此，`list` 容器引入了 **`sort` 成员函数**以完成排序。

### deque

`deque` 也是顺序容器的一种，同时也是一个可变长数组。要使用 `deque`，需要包含头文件 `deque`。**所有适用于 `vector` 的操作都适用于 `deque`**。

`deque` 和 `vector` 有很多类似的地方。在 `deque` 中，随机存取任何元素都能在常数时间内完成（但慢于vector）。它相比于 `vector` 的优点是，`vector` 在头部删除或添加元素的速度很慢，在尾部添加元素的性能较好，而 `deque` 在头尾增删元素都具有较好的性能（大多数情况下都能在常数时间内完成）。它有两种 vector 没有的成员函数：

```cpp
void push_front (const T &amp; val);  //将 val 插入容器的头部
void pop_front();  //删除容器头部的元素
```

### pair类模板

在学习关联容器之前，首先要了解 STL 中的 `pair` 类模板，因为关联容器的一些成员函数的返回值是 `pair` 对象，而且 `map` 和 `multimap` 容器中的元素都是 `pair` 对象。

`pair`实例化出来的类都有两个成员变量，一个是 **`first`**, 一个是 **`second`**。`pair`的主要作用就是将两个元素绑定成一个，`pair` 的定义方式如下所示：  

```cpp
pair&lt;typename1, typename2&gt; p;

pair&lt;string, int&gt; p; // 调用无参构造函数
p.first = &#34;ABC&#34;;
p.second = 3;

// 也可以定义时就赋值
pair&lt;string, int&gt; p(&#34;ABC&#34;, 3); // 调用对应的构造函数
```

**注意：**`pair`类型的变量可以进行**比较**，先比较`first`，如果`first`相同，则比较`second`

### set

`set`为集合，是一个**内部自动有序（默认递增排序，可修改排序规则，请自行了解）且元素不重复**的容器。可用`set`来实现**元素的去重**。使用需要包含头文件`set`。

**「不能直接修改 `set` 容器中元素的值」**。因为元素被修改后，容器并不会自动重新调整顺序，于是容器的有序性就会被破坏，再在其上进行查找等操作就会得到错误的结果。因此，如果要修改 `set` 容器中某个元素的值，正确的做法是**先删除该元素，再插入新元素**。

`set`常用成员函数：

```cpp
st.insert(x);   //插入元素x
st.find(x);   //查找元素x，查找成功返回该元素的迭代器，查找失败返回st.end()的值
st.erase(it);   //删除迭代器it指向的元素
st.erase(x);   //删除元素x
st.erase(it_first, it_end);   //删除区间[it_first~it_end]的元素
st.size();   //获取元素个数
st.clear();   //删除全部元素
```

除了`set`以外，还有：  
`multiset`:元素可以重复，且元素有序  
`unordered_set` ：元素无序且只能出现一次  
`unordered_multiset` ： 元素无序可以出现多次

但实际使用频率较低，就不介绍了，有需要的请自行了解

### map

`map` 是关联容器的一种，`map`容器主要用以**映射**，比如将一个`string`映射为一个`int`。`map` 的每个元素都分为**关键字和值**两部分，容器中的元素是按关键字排序的 **（默认递增排序，可修改排序规则，请自行了解）**，并且不允许有多个元素的关键字相同（即`key`是唯一的）。使用需要包含头文件`map`。

**`map`容器的定义：**

```cpp
map&lt;key, value&gt; mp; //mp为将key类型的变量映射为value类型的变量

map&lt;string, int&gt; mp;
map&lt;set&lt;int&gt;, string&gt; mp;
```

___
**`map`容器内元素的访问：**  
（1）通过下标，即key访问      **mp[key]**  
（2）通过迭代器，迭代器的定义和其他容器一样，map容器内的每个元素都包括`key`和`value`，故 **`it→first` 访问key**，**`it→second` 访问value**，`it`为迭代器
___

**`map`常用函数：**  

```cpp
mp.find(key)  //返回键为key的元素的迭代器，如果不存在则返回 mp.end()

mp.erase(key) //删除键为key的元素
mp.erase(first, end) //删除区间[first,end)的元素

mp.size()
mp.clear()
```

### stack

`stack`即栈容器（其实是容器适配器，没有迭代器）。使用需要包含头文件`stack`。

定义方式为：`stack&lt;typename&gt; stackname;`

常用成员函数：

```cpp
st.push(x) //将x入栈
st.pop() //弹出栈顶元素
st.top() //获取栈顶元素

st.empty()
st.size()
```

### queue

`queue`即队列容器（其实是容器适配器，没有迭代器）。使用需要包含头文件`queue`。

定义方式为：`queue&lt;typename&gt; qname;`

常用成员函数：

```cpp
q.push(x) //将x入队
q.pop() //出队

q.front() //获取队首元素
q.back()  //获取队尾元素

q.empty()
q.size()
```

### priority_queue

优先队列，底层采用**堆**来实现的。**队列元素顺序并不是按照入队顺序排列，而是按照优先级进行排列**。队首元素为**优先级最高**的元素。

优先队列与队列的**不同之处**在于：优先队列不存在 `front()`和`back()`函数，只能通过 **`top()`** 函数来访问队首（堆顶）元素。使用`top()`函数前一定要**先判断队列是否为空**。

___

元素优先级设置：  
**1）基本数据类型的优先级设置**  
优先队列对基本数据类型的优先级设置一般是 **越大的优先级越高**，基本数据类型的优先级设置如下：

```cpp
//采用默认优先级设定的优先队列
priority_queue&lt;type&gt; pq;

//自定义优先级的优先队列
priority_queue&lt;type, vector&lt;type&gt;, less&lt;type&gt; &gt; pq;
priority_queue&lt;type, vector&lt;type&gt;, greater&lt;type&gt; &gt; pq;
```

第一个参数为队列元素类型，第二个参数为承载堆的容器，第三个参数为第一个参数的比较类，`less&lt;type&gt;` 表示type越大优先级越高，`greater&lt;type&gt;`表示type越小优先级越高。

```cpp
//该优先队列表示数字越小的优先级越高
priority_queue&lt;int, vector&lt;int&gt;, greate&lt;int&gt; &gt; pri_q; 
```

**2）结构体的优先级设置**  
使用频率较低，有需要请自行了解

## 智能指针模板类

使用指针时，一定要 `new` 与 `delete` 成对出现，但有时我们会忘记 `delete` 又或者 在 `delete` 前就出现了异常转而去执行对于的 `catch` 块去了。

如果指针是对象就好，这样就可以在其过期时，自动调用析构函数来释放其指向的空间。这正是智能指针模板类 **`auto_ptr`**、 **`unique_ptr`**、 **`shared_ptr`** 背后的思想，只需要将申请到的空间地址赋值给其对象就行，其对象的析构函数会自动释放对应空间，不必手动 `delete`。

使用这些智能指针需要包含头文件 **`memory`**。

### unique_ptr

**`std::unique_ptr`** 是一种 **独占所有权** 的智能指针。它确保指针能独占一个申请的内存空间，即该指针不能将自己的值赋值给其他指针，当该 **`unique_ptr`** 指针被销毁时，它管理的空间也会被释放。

**特点：**

* **独占所有权**：不能复制，只能移动。
* **轻量级**：没有额外的引用计数开销。

**主要操作：**

* **创建和初始化**：使用 **`std::make_unique`** 创建。
* **移动所有权**：使用 **`std::move`** 将所有权转移给另一个 **`unique_ptr`**。

**示例：**

```cpp
#include &lt;iostream&gt;
#include &lt;memory&gt;

class MyClass 
{
public:
    MyClass() { std::cout &lt;&lt; &#34;MyClass constructor&#34; &lt;&lt; std::endl; }
    ~MyClass() { std::cout &lt;&lt; &#34;MyClass destructor&#34; &lt;&lt; std::endl; }
    void sayHello() const { std::cout &lt;&lt; &#34;Hello from MyClass&#34; &lt;&lt; std::endl; }
};

int main() {
    std::unique_ptr&lt;MyClass&gt; ptr1 = std::make_unique&lt;MyClass&gt;();
    ptr1-&gt;sayHello();

    // std::unique_ptr&lt;MyClass&gt; ptr2 = ptr1; // 错误：unique_ptr 不能复制
    std::unique_ptr&lt;MyClass&gt; ptr2 = std::move(ptr1); // 移动所有权
    if (!ptr1) 
    {
        std::cout &lt;&lt; &#34;ptr1 is now empty&#34; &lt;&lt; std::endl;
    }
    ptr2-&gt;sayHello();
    
    return 0;
}
```

**注意：** `make_unique`模板函数在C&#43;&#43;14及以后的标准中才有。如果要在C&#43;&#43;11及以前的标准中使用，需要自己实现。

### shared_ptr

**`std::shared_ptr`** 是一种 **共享所有权** 的智能指针。多个 **`shared_ptr`** 可以 **「共享同一个空间」**，并使用 **引用计数** 来管理空间的生命周期。当最后一个 **`shared_ptr`** 指针被销毁时，管理的空间才会被释放。

**特点：**

* **共享所有权**：多个 **`shared_ptr`** 可以指向同一个空间。
* **引用计数**：内部维护引用计数，控制空间的生命周期。

**主要操作：**

* **创建和初始化**：使用 **`std::make_shared`** 创建。
* **拷贝和赋值**：可以拷贝和赋值，增加引用计数。
* **重置和释放**：**`reset()`** 方法用于释放对象或更换对象。
* **获取原始指针**：**`get()`** 方法来获取原始指针，通过这种方法不会增加引用计数。
* **查看对象的引用计数**：**`use_count()`** 方法来查看一个对象的引用计数。

**示例：**

```cpp
#include &lt;iostream&gt;
#include &lt;memory&gt;

class MyClass {
public:
    MyClass() { std::cout &lt;&lt; &#34;MyClass constructor&#34; &lt;&lt; std::endl; }
    ~MyClass() { std::cout &lt;&lt; &#34;MyClass destructor&#34; &lt;&lt; std::endl; }
    void sayHello() const { std::cout &lt;&lt; &#34;Hello from MyClass&#34; &lt;&lt; std::endl; }
};

int main() {
    std::shared_ptr&lt;MyClass&gt; ptr1 = std::make_shared&lt;MyClass&gt;();
    std::shared_ptr&lt;MyClass&gt; ptr2 = ptr1; // 共享所有权
    MyClass *temp = ptr1.get(); // 获取原始指针，这不会增加引用计数

    std::cout &lt;&lt; &#34;-------------------------------------------------&#34; &lt;&lt; std::endl;
    // 输出当前引用计数，在此为 2，但 temp，ptr1，ptr2都指向同一片内存空间
    std::cout &lt;&lt; &#34;Reference count: &#34; &lt;&lt; ptr1.use_count() &lt;&lt; std::endl;
    std::cout &lt;&lt; &#34;ptr1: &#34; &lt;&lt; ptr1 &lt;&lt; &#34;, &#34; &lt;&lt; &#34;ptr2: &#34; &lt;&lt; ptr2 &lt;&lt; &#34;, &#34; &lt;&lt; &#34;temp: &#34; &lt;&lt; temp &lt;&lt; std::endl;
    std::cout &lt;&lt; &#34;-------------------------------------------------&#34; &lt;&lt; std::endl;
    ptr1-&gt;sayHello();
    ptr2-&gt;sayHello();
    temp-&gt;sayHello();

    std::cout &lt;&lt; &#34;-------------------------------------------------&#34; &lt;&lt; std::endl;
    ptr1.reset(); // 释放 ptr1 的所有权

    // 引用计数为 1
    std::cout &lt;&lt; &#34;Reference count after reset: &#34; &lt;&lt; ptr2.use_count() &lt;&lt; std::endl;

    ptr2-&gt;sayHello();
    // 不用 delete temp，因为这三个指针指向同一个空间，该空间已经被智能指针释放掉了
    return 0;
}
```

### weak_ptr

**`std::weak_ptr`** 是一种弱引用智能指针，它不参与对象的引用计数管理，**不能直接访问对象**。它的主要作用是解决 `std::shared_ptr` 的循环引用问题。  
&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt; std::shared_pt 的循环引用问题 &lt;/summary&gt;

考虑以下情况，存在两个类A，B；A类里面有一个指向B类对象的shared_ptr指针，B类里面有一个指向A类对象的shared_ptr指针（后面简称指针）。现在通过一个指向A类对象的指针，让该对象中的指针指向B类对象；再让一个指向B类对象的指针，让该对象中的指针指向A类对象。此时，就构成了循环引用，无法自动释放这两个空间，因为引用无法归零。

```cpp
#include &lt;iostream&gt;
#include &lt;memory&gt;

class B; // 前向声明

class A {
public:
    std::shared_ptr&lt;B&gt; b_ptr; 
    ~A() { std::cout &lt;&lt; &#34;A destroyed&#34; &lt;&lt; std::endl; }
};

class B {
public:
    std::shared_ptr&lt;A&gt; a_ptr;
    ~B() { std::cout &lt;&lt; &#34;B destroyed&#34; &lt;&lt; std::endl; }
};

int main() {
    auto a = std::make_shared&lt;A&gt;(); // auto是C&#43;&#43;11新增的关键字，可以自动推断变量的类型
    auto b = std::make_shared&lt;B&gt;();

    a-&gt;b_ptr = b;
    b-&gt;a_ptr = a;

    // 此时 a 和 b 离开作用域，但不会调用析构函数，因为存在循环引用

    return 0;
}
```

为了避免这种情况出现，就应将 **类中的指针定义为 `weak_ptr` 类型** 的指针

&lt;/details&gt;

**特点：**

* **弱引用**：不增加引用计数，不影响对象的生命周期。
* **转换**：需要转换为 **`std::shared_ptr`** 才能访问对象。

**主要操作：**

* **创建和初始化**：通过 **`std::shared_ptr`** 创建。
* **锁定和访问**：使用 **`lock`** 方法转换为 **`std::shared_ptr`**。

**示例：**

```cpp
#include &lt;iostream&gt;
#include &lt;memory&gt;

class MyClass
{
public:
    MyClass() { std::cout &lt;&lt; &#34;MyClass constructor&#34; &lt;&lt; std::endl; }
    ~MyClass() { std::cout &lt;&lt; &#34;MyClass destructor&#34; &lt;&lt; std::endl; }
    void sayHello() const { std::cout &lt;&lt; &#34;Hello from MyClass&#34; &lt;&lt; std::endl; }
};

int main()
{
    std::shared_ptr&lt;MyClass&gt; sp1 = std::make_shared&lt;MyClass&gt;();
    std::weak_ptr&lt;MyClass&gt; wp = sp1; // 创建弱引用

    std::shared_ptr&lt;MyClass&gt; sp2 = wp.lock(); // 转换为 shared_ptr后赋值
    sp2-&gt;sayHello();
    std::cout &lt;&lt; &#34;Reference count: &#34; &lt;&lt; sp2.use_count() &lt;&lt; std::endl;

    sp1.reset(); // 释放 sp1 的所有权
    sp2.reset(); // 释放 sp2 的所有权，此时引用计数为0，该空间被释放掉

    // 在空间被释放后，尝试转换，转换失败会返回一个空的 shared_ptr
    std::shared_ptr&lt;MyClass&gt; sp3 = wp.lock();
    if(sp3 == nullptr) std::cout &lt;&lt; &#34;Conversion failed&#34; &lt;&lt; std::endl;

    return 0;
}
```

## 文件操作

### 什么是文件？

内存中的数据在计算机关机后就会消失。要长久保存数据，就要使用硬盘、光盘、U 盘等设备。而为了便于数据的管理和检索，引入了“文件”的概念，即 **「文件是计算机系统中用于存储和组织数据的一种基本形式」**。

一个文档、一段视频、一个可执行程序，都可以被保存为一个文件，并赋予一个文件名。操作系统以**文件为单位**管理磁盘中的数据。

如果不对文件进行分类，当有成千上万个文件放在一起时，使用起来就会非常不便，因此又引入了**树形目录**（目录也叫文件夹）的机制，可以把文件放在不同的文件夹中，文件夹中还可以嵌套文件夹，这就便于用户对文件进行管理和使用，正如 `Windows` 的资源管理器呈现的那样。

通常，按文件的功能来分，文件可分为文本文件、视频文件、音频文件、图像文件、可执行文件等多种类别。但从数据存储的角度来说，所有文件本质上都是一样的，都是一个 0、1 比特串。不同的文件呈现出不同的形态（有的是文本，有的是视频等等）是因为文件的创建者和解释者（使用文件的软件）约定好了**文件格式**。

所谓“格式”，就是对文件中每一部分的内容代表什么含义的一种约定。例如，常见的纯文本文件（也叫文本文件，扩展名通常是“.txt”），指的是能够在 Windows 的“记事本”程序中打开，并且能看出是一段有意义的文字的文件。文本文件的格式可以用一句话来描述：文件中的每个字节都是一个可见字符的 ASCII 码。

除了纯文本文件外，图像、视频、可执行文件等一般被称作“二进制文件”。二进制文件如果用“记事本”程序打开，看到的是一片乱码。

所谓“文本文件”和“二进制文件”，只是约定俗成的、从用户角度出发进行的分类，并不是计算机科学的分类。因为从计算机科学的角度来看，所有的文件都是由二进制位组成的，都是二进制文件。文本文件和其他二进制文件只是格式不同而已。

___

实际上，只要规定好格式，而且不怕浪费空间，用文本文件一样可以表示图像、声音、视频甚至可执行程序。简单地说，如果约定用字符 &#39;1&#39;、&#39;2&#39;、...、&#39;7&#39; 表示七个音符，那么由这些字符组成的文本文件就可以被遵从该约定的音乐软件演奏成一首曲子。

以 256 色图像为例，可以用 0~255 这 256 个数代表 256 种颜色，那么每个像素就可以用一个数来表示。再约定文件开始的两个数代表图像的宽度和高度（以像素为单位），则以下文本文件就可以表示一幅宽度为 7 像素、高度为 3 像素的 256 色图像：

```bash
7 3
37 0 38 129 4 154 0
73 3 227 40 0 0 1
17 173 127 20 0 0 2
```

这个“文本图像”文件的格式可以描述为：第一行的两个数分别代表水平方向的像素数目和垂直方向的像素数目，此后每行代表图像的一行像素，一行中的每个数对应于一个像素，表示其颜色。理解这一格式的图像处理软件就可以把上述文本文件呈现为一幅图像。视频是由每秒 24 幅图像组成的，因此用文本文件也可以表示视频。

但用文本文件来表示图像是非常低效的方法，浪费了太多的空间。文件中大量的空格是一种浪费。另外，常常要用 2 个甚至 3 个字符来表示一个像素，也造成大量浪费，因为用一个字节就足以表示 `0~255` 这 256 个数。因此，可以约定一个更节省空间的格式来表示一个 256 色的图像，此种文件格式的描述如下：文件中的第 0 和 1 个字节是整数 n，代表图像的宽度（2 字节的 n 的取值范围是 0~65 535，说明图像最多只能是 65 535 个像素宽），第 2 和 3 个字节代表图像的高度。接下来，每 n 个字节表示图像的一行像素，其中每个字节对应于一个像素的颜色。

用这种格式存储 256 色图像，比用上面的文本格式存储图像能够大大节省空间。在“记事本”程序中打开它，看到的就会是乱码，这个图像文件也就是所谓的“二进制文件”。

真正的图像文件、音频文件、视频文件的格式都比较复杂，有的还经过了压缩，但只要文件的制作软件和解读软件（如图像查看软件，音频、视频播放软件）遵循相同的格式约定，用户就可以在文件解读软件中看到文件的内容。

### 文件流类

C&#43;&#43; 标准类库中有三个类可以用于文件操作，它们统称为文件流类。这三个类是：

* **`ifstream`**：用于从文件中读取数据。
* **`ofstream`**：用于向文件中写人数据。
* **`fstream`**：既可用于从文件中读取数据，又可用于 向文件中写人数据。

使用这三个类时，程序中需要包含 **`fstream`** 头文件。

### 打开文件和关闭文件

在对文件进行读写操作前需要先打开文件。**打开文件的目的有以下两个**：

1. 建立起指定文件和文件流对象的关联，以后要对文件进行操作时，就可以通过与之关联的流对象来进行。
2. 指明文件的使用方式。使用方式有只读、只写、既读又写、在文件末尾添加数据、以文本方式使用、以二进制方式使用等多种。

**打开文件可以通过以下两种方式进行**：

1. 调用流对象的 `open` 成员函数打开文件。
1. 定义文件流对象时，通过构造函数打开文件。

**注意：** 对文件操作结束后，一定要调用流对象的 **`close()`** 成员函数来关闭文件！！！且打开后应调用流对象的 **`is_open()`** 成员函数来判断是否成功打开文件。

无论是哪种打开方式，都需要使用两个参数：①以字符串的形式给出的要打开的文件路径。②文件打开模式

文件打开模式如下表所示：

| 模式标记            | 适用对象                  | 作用                                                                                                                     |
| :------------------ | :------------------------ | :----------------------------------------------------------------------------------------------------------------------- |
| ios::in             | ifstream fstream          | 打开文件用于读取数据。如果文件不存在，则打开出错。                                                                       |
| ios::out            | ofstream fstream          | 打开文件用于写入数据。如果文件不存在，则新建该文件；如果文件原来就存在，则打开时清除原来的内容。                         |
| ios::app            | ofstream fstream          | 打开文件，用于在其尾部添加数据。如果文件不存在，则新建该文件。                                                           |
| ios::ate            | ifstream                  | 打开一个已有的文件，并将文件读指针指向文件末尾。如果文件不存在，则打开出错。                                             |
| ios::trunc          | ofstream                  | 单独使用时与ios::out的效果相同。该模式主要应用于组合使用的情况，用于清晰表达程序员的意图，即明确表示打开文件时清空其内容 |
| ios::binary         | ifstream ofstream fstream | 以二进制方式打开文件。若不指定此模式，则以文本模式打开。所有文件都可以二进制的形式打开。                                 |
| ios::in \| ios::out | fstream                   | 打开已存在的文件，既可读取其内容，也可向其写入数据。文件刚打开时，原有内容保持不变。如果文件不存在，则打开出错。         |

打开模式的值是可以通过 **`|`** 符号来组合使用的，例如：`ios::in | ios::out | ios::trunc` 表示以读写方式打开指定文件，且打开文件时清空其内容。

```cpp
#include &lt;iostream&gt;
#include &lt;fstream&gt;

int main() 
{
    // 以构造函数的形式打开 output.txt 文件，省略了第二个参数，因为有默认值 ios::out
    std::ofstream fout(&#34;output.txt&#34;); 
    
    // 以调用成员函数 open() 的方式打开 example.txt文件 ，省略了第二个参数，因为有默认值 ios::in
    std::ifstream fin;
    fin.open(&#34;example.txt&#34;);
    
    // 判断是否成功打开了文件
    if ( !fout.is_open() ) 
    {
        std::cerr &lt;&lt; &#34;fout Error opening file!&#34; &lt;&lt; std::endl;
        return 1;
    }

    if ( !fin.is_open() ) 
    {
        std::cerr &lt;&lt; &#34;fin Error opening file!&#34; &lt;&lt; std::endl;
        return 1;
    }

    ...... // 对文件进行的一系列操作

    // 对文件操作完成后关闭文件。
    fout.close();
    fin.close();

    return 0;
}
```

### 文本打开方式和二进制打开方式的区别

在 `UNIX/Linux` 平台中，用文本方式或二进制方式打开文件没有任何区别。

在 `UNIX/Linux` 平台中，文本文件以`\n`（ASCII 码为 `0x0a`）作为换行符号；而在 `Windows` 平台中，文本文件以连在一起的`\r\n`（`\r`的 ASCII 码是 `0x0d`）作为换行符号。

在 Windows 平台中，如果以文本方式打开文件，当读取文件时，系统会将文件中所有的`\r\n`转换成一个字符`\n`，如果文件中有连续的两个字节是 `0x0d0a`，则系统会丢弃前面的 `0x0d` 这个字节，只读入 `0x0a`。当写入文件时，系统会将`\n`转换成`\r\n`写入。

也就是说，如果要写入的内容中有字节为 `0x0a`，则在写人该字节前，系统会自动先写入一个 `0x0d`。因此，如果用文本方式打开二进制文件进行读写，读写的内容就可能和文件的内容有出入。

因此，**用二进制方式打开文件总是最保险的**。

### 文本文件的读取和写入

使用文件流对象打开文件后，该文件流对象就成为了一个输入流或输出流。就可以像使用 `cin`、`cout` 那样来使用对象对文件进行读写操作。

```cpp
#include &lt;iostream&gt;
#include &lt;fstream&gt;

int main() 
{
    std::ifstream fin(&#34;in.txt&#34;);
    std::ofstream fout(&#34;output.txt&#34;); 
    string mstr;

    if (!fin.is_open()) 
    {
        std::cerr &lt;&lt; &#34;in Error opening file!&#34; &lt;&lt; std::endl;
        return 1;
    }

    if (!fout.is_open()) 
    {
        std::cerr &lt;&lt; &#34;out Error opening file!&#34; &lt;&lt; std::endl;
        return 1;
    }

    fin &gt;&gt; mstr; // 从 in.txt 读取一个字符串存储到mstr中
    fout &lt;&lt; mstr &lt;&lt; endl; // 将 mstr的内容和换行符输入到 output.txt 中 

    fin.close();
    fout.close();

    return 0;
}
```

### 二进制文件的读取和写入

二进制文件比文本文件更加容易检索、且存储相同大小的内容时二进制文件占用的空间更小。

对二进制文件的读写只能使用文件流类的 **`read()`** 和 **`write()`** 成员函数。

write() 的原型为：  
`ostream &amp; write(char* buffer, int count);`

该成员函数用于将`buffer` 中的前 `count` 个字节写入到文件中去，从文件写指针指向的位置开始写。返回值是调用该函数的对象的引用。

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt; 读指针和写指针 &lt;/summary&gt;
  
  文件中通常有读指针（read pointer）和写指针（write pointer），它们分别用于跟踪文件内容的读取和写入位置。

  **读指针：** 当文件以读取模式打开时，读指针指向文件的当前读取位置。随着数据的读取，读指针会相应地向前移动。如果到达文件末尾，读指针会保持在文件末尾的位置。
  
  **写指针：** 当文件以写入模式打开时，写指针指向文件的当前写入位置。写入数据时，写指针会根据写入的数据量向前移动。如果文件是新创建的或被清空的，写指针通常从文件的开始位置写入；如果是追加模式，写指针会移动到文件的末尾。
  
  在可以同时进行读写操作的文件流（如 fstream）中，读指针和写指针可以同时存在。它们可以独立操作，分别用于读取和写入数据。

&lt;/details&gt;

直接看示例：

```cpp
#include &lt;iostream&gt;
#include &lt;fstream&gt;
using namespace std;

class CStudent
{
public:
    char szName[20];
    int age;
};

int main()
{
    CStudent s;
    ofstream outFile(&#34;students.dat&#34;, ios::out | ios::binary); // 以二进制的形式打开文件

    while (cin &gt;&gt; s.szName &gt;&gt; s.age)
        outFile.write((char *)&amp;s, sizeof(s)); // 将内容输入到 students.dat 中

    outFile.close(); // 关闭文件
    return 0;
}
```

如果用记事本程序打开该文件，则会呈现乱码。

___

read()的原型如下：  
`istream &amp; read(char* buffer, int count);`

该成员函数的作用是从文件的读指针指向的位置开始读取 `count` 个字节的内容到 `buffer` 中。返回值是调用该函数的对象的引用。

如果想要知道最终读取的字节数，可以在调用`read()`函数后调用 `gcount()`。

将上面的`students.dat`文件中的内容读取出来并输出到控制台。

```cpp
#include &lt;iostream&gt;
#include &lt;fstream&gt;
using namespace std;

class CStudent
{
    public:
        char szName[20];
        int age;
};

int main()
{
    CStudent s;       
    ifstream inFile(&#34;students.dat&#34;,ios::in|ios::binary); //二进制读方式打开

    if(!inFile.is_open()) 
    {
        cout &lt;&lt; &#34;error&#34; &lt;&lt;endl;
        return 0;
    }

    while(inFile.read((char *)&amp;s, sizeof(s))) // 每次读取一个同学的信息 
    {
        int readedBytes = inFile.gcount(); // 获取刚才读取的字节数
        cout &lt;&lt; s.szName &lt;&lt; &#34; &#34; &lt;&lt; s.age &lt;&lt; endl;   // 输出到控制台
    }

    inFile.close(); // 关闭文件
    return 0;
}
```

___

如果每次只想读取或写入一个字节，那么可以使用`get()`和`put()`成员函数。

### 移动或获取文件读写指针

在读写文件时，有时希望直接跳到文件中的某处开始读写，这就需要先将文件的读写指针指向该处，然后再进行读写。

* ifstream 类和 fstream 类有 **`seekg`** 成员函数，可以设置文件读指针的位置；
* ofstream 类和 fstream 类有 **`seekp`** 成员函数，可以设置文件写指针的位置。

所谓“位置”，就是指 **「距离文件开头有多少个字节」**。文件开头的位置是 0。

这两个函数的原型如下：

```cpp
ostream &amp; seekp (int offset, int mode);
istream &amp; seekg (int offset, int mode);
```

mode 代表文件读写指针的设置模式，有以下三种选项：

* **`ios::beg`**：让文件读指针（或写指针）指向从文件开始向后的 offset 字节处。offset 等于 0 即代表文件开头。在此情况下，offset 只能是非负数。
* **`ios::cur`**：在此情况下，offset 为负数则表示将读指针（或写指针）从当前位置朝文件开头方向移动 offset 字节，为正数则表示将读指针（或写指针）从当前位置朝文件尾部移动 offset字节，为 0 则不移动。
* **`ios::end`**：让文件读指针（或写指针）指向从文件结尾往前的 |offset|（offset 的绝对值）字节处。在此情况下，offset 只能是 0 或者负数。

___

此外，我们还可以得到当前读写指针的具体位置：

* ifstream 类和 fstream 类还有 **`tellg`** 成员函数，能够返回文件读指针的位置
* ofstream 类和 fstream 类还有 **`tellp`** 成员函数，能够返回文件写指针的位置。

返回值为当前指针距离文件头的字节数，即偏移量。

## 现代C&#43;&#43;教程

一般学完上面的知识就够用了。如果你想学习更新的特性，比如C&#43;&#43;14后的新特性，可去[此网站](https://changkun.de/modern-cpp/zh-cn/00-preface/)进行学习。

## 参考资料

1. https://www.54benniao.com/view/6396.html
2. https://learn-cpp.guyutongxue.site/ch01/assignment_and_if.html
3. https://docs.oldtimes.me/c.biancheng.net/view/2264.html
4. https://www.rowlet.info/post/7#1.%20static_cast
5. Kimi ai
6. ChatGPT
7. C&#43;&#43; Primer(中文版，第5版)


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/3b2b064/  


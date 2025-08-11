# Python学习笔记


## 环境搭建

我是在WSL2下的Ubuntu进行Python开发的，所以本笔记的环境搭建仅适用于Liunx开发环境，Windows下搭建环境请自行Google。

### miniconda

首先，先安装 **`miniconda`**，直接按照[官方教程](https://docs.anaconda.com/miniconda/install/#quick-command-line-install)即可安装。  
&gt; 不同的项目可能需要不同版本的库或包，而直接在系统中安装多个版本可能会导致冲突和不可预见的问题。为了解决这个问题，conda虚拟环境应运而生。
&gt;
&gt; conda虚拟环境允许你在同一台机器上创建多个 **「独立」** 的环境，每个环境都有自己的Python解释器和依赖库，从而实现了项目之间的隔离。这样，你可以在一个环境中安装特定版本的库，而不影响其他环境。

**`miniconda`** 的常用命令有：

``` bash
# 创建一个名为 myenv 的环境并指定在该环境中安装 3.7版本的Python
conda create --name myenv python=3.7 

# 激活或切换到名称为 myenc 的环境
conda activate myenv

# 在当前环境下安装 numpy 包
conda install numpy

# 在当前环境下安装指定版本的 numpy 包
conda install numpy=1.21.0

# 在当前环境下删除 numpy 包
conda remove numpy

# 清理Conda环境，删除没有使用的包和环境以及包缓存
conda clean --all

# 删除名称为 myenv的环境及其所有内容
conda env remove --name myenv

# 列出所有环境
conda env list

# 列出当前环境中的所有包
conda list

# 导出当前环境
conda env export &gt; environment.yml

# 根据导出文件创建环境
conda env create -f environment.yml
```

### VSCode插件

作者是使用VSCode来进行开发的，你也可以选择PyCharm。这里就推荐一些好用的`Python`插件

1. Python
2. Python Docstring Generator
3. isort
4. Black Formatter

## Python基础

该笔记适用于已经掌握一门编程语言的人，所以对于一些基础知识不会进行过多介绍。

### 输入输出

`Python` 使用 **`print()`** 函数进行输出，该函数可以接收多个参数，每个参数用逗号 `,` 隔开（该逗号会被当做空格来输出），接受的参数类型可以是表达式、字符串、变量。

**NOTE：** 当表达式作为参数时，会输出该表达式的值。该函数会自动输出一个换行符。

```python
print(&#39;100 &#43; 200 =&#39;, 100 &#43; 200)

# 输出结果：100 &#43; 200 = 300
```

&lt;br&gt; `Python` 使用 **`input()`** 函数进行输入，无论用户输入什么内容，该函数都会将其作为字符串返回。如果需要将输入转换为整数、浮点数或其他数据类型，需要在获取输入后 **显式地** 进行转换。该函数可以接收一个字符串参数，该字符串会被打印出来提示用户输入信息。

``` python
# 会打印 please enter your name: 提示用户要输入的内容
name = input(&#39;please enter your name: &#39;)
print(&#39;hello,&#39;, name)

# 如果 input() 函数没有使用参数就只会一直闪烁光标等待用户输入
```

### 数据类型和变量

#### 数据类型

在Python中，能够直接处理的数据类型有以下几种：

1. **整数**  
   Python可以处理任意大小的整数，和其他语言一样也可以使用其他进制来表示，比如十六进制（0x前缀）。对于很大的整数，Python允许在数字之间用 **`_`** 分隔，因此，`10_000_000_000` 和`10000000000`是完全一样的。十六进制数也可以写成 `0xa1b2_c3d4`。  
   &lt;br&gt;Python中用 **`//`** 来表示整除
2. **浮点数**  
   和其他语言一样，没什么区别。对于很大或很小的浮点数，必须用科学计数法来表示。
3. **字符串**  
   在Python中，字符串是以单引号 `&#39;` 或双引号 `&#34;` 括起来的任意文本，比如 `&#39;abc&#39;，&#34;xyz&#34;`等等。如果一个字符串中包括单引号，则可以用双引号括起该字符串；如果一个字符串中既包含单引号又包含双引号，则可以用转义字符 `\` 来标识。如果想要字符串默认不转义，则可以在字符串前面加上`r`。  
   当一个字符串需要多次换行输出时，可以使用`&#39;&#39;&#39;`符号将字符串括起来，这样就不必多次使用`\n`符号了。

   ```python
   print(&#39;\\\t\\&#39;) # 输出结果：\       \
   print(r&#39;\\\t\\&#39;) # 输出结果：\\\t\\
   print(&#39;&#39;&#39;line1
    line2
    line3&#39;&#39;&#39;)
    # 输出结果：
    # line1
    # line2
    # line3
   ```

4. **布尔值**  
   和其他语言一样，只有 `True` 和 `False` 两种值（注意首字母大写）。在Python中 **与或非** 采用逻辑 **`and` `or` `not`** 来表示。
5. **空值**  
   空值是Python里一个特殊的值，用 **`None`** 表示。None不能理解为0，因为0是有意义的，而 `None` 是一个特殊的空值。  
   &lt;br&gt;此外，Python还提供了列表、字典等多种数据类型，还允许创建自定义数据类型，后面会讲到。

#### 变量

在Python中变量是没有固定类型的，你给其赋什么类型的值，它就是什么类型的变量。所以，在Python中定义变量时不需要指定其类型。

``` python
x = 10   # 这时变量x是整型
x = &#39;abc&#39; # 这时变量x是字符串
```

#### 常量

在Python中并没有像C&#43;&#43;中的`const`那样的关键字来定义常量，但是有一个约定俗成的惯例，即常量通常使用 **全大写字母** 表示。

### 字符串和编码

#### 字符编码

因为作者使用到字符编码的情况不多，所以这里就不多介绍了，请自行在[此网站](https://liaoxuefeng.com/books/python/basic/string-encoding/index.html)进行学习。

#### 格式化

Python输出格式化的字符串的方式有多种，这里只介绍最常用的，用 **`%`** 来实现，和C语言的语法相识。

```python
# 格式为 print(&#39;...&#39; % (参数1, 参数2) )

print(&#39;%2d-%02d&#39; % (3, 1))
print(&#39;%.2f&#39; % 3.1415926)
```

控制符的使用与C语言一样，如果想要输出 `%`符号则需要使用 `%%` 来表示。如果只有一个参数，则`%`后的小括号可以省略。

### list和tuple

#### list

`list`是一种有序的集合，是Python内置的一种数据类型，可以随时添加和删除其中的元素。使用`[ ]`符号来创建一个列表。可以把`list`看作`vector`，直接看示例：

```python
classmates = [&#39;Michael&#39;, &#39;Bob&#39;, &#39;Tracy&#39;]  # 创建一个列表，包含三个元素
print(len(classmates)) # 输出列表长度
print(classmates[0]) # 输出 Michael
print(classmates[1]) # 输出 Bob
print(classmates[2]) # 输出 Tracy
```

在上面的例子中，想要获取最后一个元素除了使用索引`2`以外，还可以使用索引`-1`，即`classmates[2]` 等价于 `classmates[-1]`。依此类推，`-2`也是倒数第二个元素的索引，`-3`是倒数第三个元素的索引。

&lt;span style=&#34;color: red;&#34;&gt;**Note:**&lt;/span&gt; 使用索引时要确保索引在正确的范围内。

&lt;br&gt;list是一个可变的有序表，所以，可以往list中添加元素：

```python
classmates.append(&#39;Adam&#39;) # 往列表尾部追加元素 Adam
classmates.insert(1, &#39;Ddam&#39;) # 往索引为1的位置插入元素 Ddam
```

&lt;br&gt;要删除list中的元素，用`pop()`方法，该方法会返回被删除的元素：

```python
classmates.pop() # 删除末尾元素
classmates.pop(1) # 删除索引为1的元素
```

要把某个元素替换成别的元素，可以直接赋值给对应的索引位置：
`classmates[1] = &#39;Sarah&#39;`

&lt;span style=&#34;color: red;&#34;&gt;**Note:**&lt;/span&gt; list中的元素类型可以不同，比如`[&#39;123&#39;, 333, [&#39;1&#39;, 2]]` 这个列表中的元素类型分别为字符串、整形、列表。

#### tuple

tuple表示元组，跟`list`非常类似，使用`()`符号来定义，但是tuple一旦初始化后就不能修改（元素的顺序也不能改变），使用它会让代码更安全。tuple没有像append(), insert()这样可以修改tuple的方法，可以正常地使用classmates[0]，classmates[-1]，但不能赋值成另外的元素。

tuple中的元素类型也可以不相同，下面为定义tuple的示例：

```python
t1 = ()        # 定义一个空的tuple
t2 = (1,)     # 定义只包含一个元素的tuple
t3 = (1, 2, &#39;abc&#39;)  # 定义包含三个元素的tuple
print(t3[1]) #输出 2
```

&lt;span style=&#34;color: red;&#34;&gt;**Note:**&lt;/span&gt; 定义只含一个元素的tuple时，必须使用`(1,)`的格式，即必须加上一个 **逗号** ，因为在python中括号既表示tuple，又表示数学公式中的小括号，这就产生了歧义，因此，Python规定，这种情况下，(1)会被认为是数学表达式，而不是tuple。Python在输出只含一个元素的tuple时，也会加一个逗号，以免你误解成数学计算意义上的括号。

&lt;br&gt;虽然tuple本身不可变，但如果tuple内包含可变对象（如list），则这些可变对象的内容是可以修改的，如：

```python
t = (1, 2, [3, 4])
t[2][0] = 5   # 修改了元组中列表的内容
print(t)      # 输出 (1, 2, [5, 4])
```

在上面的这个例子中，tuple的元素是没有改变的，变的只是list的元素！！！

&lt;br&gt;tuple只支持两个基本方法：

```python
t = (1, 2, 2, 3)
print(t.count(2))  # 统计元组t中元素2出现的次数，输出 2
print(t.index(2))  # 输出索引为2的元素
```

### 条件判断

Python中的`if`语法为：

```python
if &lt;条件判断1&gt;:
    &lt;执行1&gt;
elif &lt;条件判断2&gt;:
    &lt;执行2&gt;
elif &lt;条件判断3&gt;:
    &lt;执行3&gt;
else:
    &lt;执行4&gt;
```

&lt;span style=&#34;color: red;&#34;&gt;**Note:**&lt;/span&gt; Python中使用 **缩进** 来确定代码块，而不是花括号`{}`

### 模式匹配

Python中的模式匹配类似于C/C&#43;&#43;中的`switch`，但功能更加强大，除了可以匹配简单的单个值外，还可以匹配多个值、匹配一定范围，需要`3.10`以上的版本才能使用，具体使用参考[此教程](https://liaoxuefeng.com/books/python/basic/match/index.html)。

### 循环

Python的循环只有两种，一种是`for...in`循环，一种是`while`循环，这两个循环的语法为：

```python
for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ...]:
    &lt;执行语句&gt;

while &lt;条件&gt;:
    &lt;执行语句&gt;
```

Python也可以在循环中使用 **`break`** 和 **`continue`**。

如果要使用for循环来执行1000次循环，从1写到1000是很困难的，幸好Python提供一个 **`range()`** 函数，可以生成一个整数序列，即一个范围对象，可以通过`list()`函数将其转换为列表。

range()函数可以接受一到三个整数参数：

* range(stop)：生成从0到stop-1的整数序列。
* range(start, stop)：生成从start到stop-1的整数序列。
* range(start, stop, step)：生成从start开始，每次增加step，直到stop-1的整数序列。

例如：

```python
# 从0到4
range(5)

# 从1到4
range(1, 5)

# 从0到9，步长为2
range(0, 10, 2)

# 循环 1000 次
for x in range(1000):
    &lt;执行语句&gt;
```

### dict和set

#### dict

Python中的字典`dict`就是其他语言中的`map`，使用`key-value`存储。使用花括号`{ }`来初始化一个字典。

例如：`d = {&#39;Michael&#39;: 95, &#39;Bob&#39;: 75, &#39;Tracy&#39;: 85}`。

除了初始化可以放入元素外，还可以通过`key` 放入数据，`d[&#39;Adam&#39;] = 67`。

如果访问不存在的key，dict就会报错，要避免key不存在的错误，有两种方法：

```python
d = {&#39;Michael&#39;: 95, &#39;Bob&#39;: 75, &#39;Tracy&#39;: 85}
# 方法一：通过in判断

print(&#39;Thomas&#39; in d) # 输出 False

# 方法二：通过dict提供的get()方法判断
print(d.get(&#39;Thomas&#39;)) # 输出None
print(d.get(&#39;Thomas&#39;, -1)) # 输出-1
```

&lt;br&gt;要删除一个key，用`pop(key)`方法：**`d.pop(&#39;Bob&#39;)`**

&lt;span style=&#34;color: red;&#34;&gt;**Note:**&lt;/span&gt; key必须是 **不可变对象**，无论对不可变对象做什么操作，其内容都是不会变的。

#### set

Python中的set和其他语言的set一样，是一个无序的无重复元素的集合，也是使用花括号`{ }`来进行初始化，直接看示例：

```python
s = {1, 1, 2, 2, 3, 3}
print(s) # 显示{1, 2, 3}

s.add(4) # 添加元素4
print(s) # 显示{1, 2, 3, 4}

s.remove(4) # 删除元素4
print(s) # 显示{1, 2, 3}

# Python中的set可以做交集、并集操作
s1 = {1, 2, 3}
s2 = {2, 3, 4}
print(s1 &amp; s2) # 输出交集结果
print(s1 | s2)   # 输出并集结果
```

## 函数

### 定义函数

Python定义函数的语法为：

```python
def funName():
    &lt;执行语句&gt;
    return ...

# 当还没有想好该函数该怎么实现时可用 pass来替代函数体，这样就定义了一个空函数
def funName():
    pass
```

Python中的函数不需要写返回值类型和参数类型，因为前面已经说过了Python是动态语言，其变量的类型是不定的。

`pass`除了可以用在函数内，也能用于其他语句中，比如`if`

Python中的函数可以直接返回 **&#34;多个值&#34;**：

```python
import math

def move(x, y, step, angle):
    nx = x &#43; step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny
  
x, y = move(100, 100, 60, math.pi / 6)
print(x, y) # 输出：151.96152422706632 70.0
```

但这只是一种假象，Python函数返回的仍然是单一值，函数将多个要返回的参数定义成一个 **元组`tuple`** 进行返回!!!

```python
r = move(100, 100, 60, math.pi / 6)
print(r) # 输出：(151.96152422706632, 70.0)
```

### 函数的参数

#### 默认参数

与其他语言一样，Python中的函数也能使用默认参数，当你为某个参数提供默认值时，该参数 **右边的参数** 也必须有默认参数，下面这种函数定义就是错误的：  
`def process(data, mode=&#34;read&#34;, option)`

如果想要给`mode`提供默认参数，那么就必须也给`option`提供默认参数。

调用带默认参数的函数方式与其他语言一样：

```python
def enroll(name, gender, age=6, city=&#39;Beijing&#39;):
    print(&#39;name:&#39;, name)
    print(&#39;gender:&#39;, gender)
    print(&#39;age:&#39;, age)
    print(&#39;city:&#39;, city)

enroll(&#39;A&#39;, &#39;F&#39;) # 其他参数使用默认值
enroll(&#39;B&#39;, &#39;F&#39;, 7) # city参数使用默认值
enroll(&#39;C&#39;, &#39;M&#39;, city = &#39;SiChuan&#39;) # age参数使用默认值
```

需要注意的是，函数定义中的默认参数只会在 **「函数定义时被创建一次」**。所以，当你给参数提供一个可变对象（比如列表）为默认值时一定要小心，因为多次调用该函数时，使用的都是 **同一个可变对象**。

直接看例子：

```Python
def add_end(L=[]):
    L.append(&#39;END&#39;)
    return L

add_end()
# 输出：&#39;END&#39;]

add_end()
# 输出：[&#39;END&#39;, &#39;END&#39;]

add_end()
# 输出：[&#39;END&#39;, &#39;END&#39;, &#39;END&#39;]
```

所以，定义默认参数要牢记一点：**默认参数必须指向不变对象！**

修改上面的例子，使其调用多次都不会有问题：

```python
def add_end(L=None):
    if L is None:
        L = []
    L.append(&#39;END&#39;)
    return L
```

#### 可变参数

跟其他语言一样，Python也可以定义可变参数，在参数前面加上一个 **`*`** 号即可。

```python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum &#43; n * n
    return sum

print(calc(1, 2)) # 输出：5
print(calc()) # 输出：0
```

如果已经有一个list或者tuple，要调用一个可变参数怎么办？可以这样做：

```python
nums = [1, 2, 3]
calc(nums[0], nums[1], nums[2]) # 方法一
calc(*nums)  # 方法二，推荐做法
```

#### 关键字参数

可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个`tuple`。而关键字参数允许你传入0个或任意个 **含参数名的参数**，这些关键字参数在函数内部自动组装为一个 **`dict`**。请看示例：

```python
# 该函数除了必选参数name和age外，还接受关键字参数kw。
def person(name, age, **kw):
    print(&#39;name:&#39;, name, &#39;age:&#39;, age, &#39;other:&#39;, kw)

person(&#39;Michael&#39;, 30)
# 输出：name: Michael age: 30 other: {}

person(&#39;Adam&#39;, 45, gender=&#39;M&#39;, job=&#39;Engineer&#39;)
# 输出：name: Adam age: 45 other: {&#39;gender&#39;: &#39;M&#39;, &#39;job&#39;: &#39;Engineer&#39;}
```

关键字参数有什么用？它可以 **扩展函数的功能**。比如，在person函数里，我们保证能接收到name和age这两个参数，但是，如果调用者愿意提供更多的参数，我们也能收到。试想你正在做一个用户注册的功能，除了用户名和年龄是必填项外，其他都是可选项，利用关键字参数来定义这个函数就能满足注册的需求。

和可变参数类似，也可以先组装出一个dict，然后，把该dict转换为关键字参数传进去：

```python
extra = {&#39;city&#39;: &#39;Beijing&#39;, &#39;job&#39;: &#39;Engineer&#39;}
person(&#39;Jack&#39;, 24, city=extra[&#39;city&#39;], job=extra[&#39;job&#39;])
# 输出：name: Jack age: 24 other: {&#39;city&#39;: &#39;Beijing&#39;, &#39;job&#39;: &#39;Engineer&#39;}
```

当然，也可使用简化的写法：
**`person(&#39;Jack&#39;, 24, **extra)`**

#### 命名关键字参数

对于关键字参数，函数的调用者可以传入任意不受限制的关键字参数。如果想要限制关键字参数的名字，就可以使用命名关键字参数。

定义方式如下：

```python
# 方式一：没有可变参数，命名关键字参数前需要一个 * 分隔符，即 * 后面的参数都是命名关键字参数
def person(name, age, *, city, job):
    print(name, age, city, job)

# 方式二：有可变参数，可变参数后面的参数都是命名关键字参数
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
```

&lt;span style=&#34;color: red;&#34;&gt;**Note:**&lt;/span&gt; 命名关键字参数必须传入参数名！！！命名关键字参数也能有默认值。

#### 参数组合

在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意，参数定义的顺序 **必须是**：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

#### 修改实参值

在 Python 中，当实参是 **「可变对象」** 时（例如列表、字典、集合等），在函数内对该对象的修改会直接影响到实参的值。这是因为可变对象是通过引用传递的。

如果实参是不可变对象（如整数、浮点数、字符串、元组等），在函数内部对它们的任何修改实际上是创建了新的对象，不会影响到原始的实参。

## 高级特性

### 切片

取一个list或tuple的部分元素是非常常见的操作。比如，一个list如下：
`L = [&#39;Michael&#39;, &#39;Sarah&#39;, &#39;Tracy&#39;, &#39;Bob&#39;, &#39;Jack&#39;]`

如果要取前3个元素，该怎么做？直接采用循环来做吗？

当然可以用循环来做，但这十分繁琐，一点都不优雅，所以，Python提供了切片操作符，能直接优雅的进行这种操作，直接看例子：

```python
L = [&#39;Michael&#39;, &#39;Sarah&#39;, &#39;Tracy&#39;, &#39;Bob&#39;, &#39;Jack&#39;]
print(L[0:3]) # 采用切片操作直接取出前三个元素
```

切片的详细语法为：**`test[start_idx : end_idx : step]`**

`test`为任意序列类型，比如列表、字符串、元组等；`test[start_idx : end_idx ]` 表示取出test中索引为 `[start_idx : end_idx)` 的元素，如果 start_idx 为 0 或 end_idx 为最后一个元素的索引时均可不写，例如 test[ : 3], test[1 : ], test[ : ]；`test[start_idx : end_idx : step]` 表示在 `[start_idx : end_idx)`中依次取出索引为 start_idx、start_idx &#43; step、start_idx &#43; step * 2、start_idx &#43; step * 3···的元素，当步长为1时，可省略不写。

### 迭代

在Python中，迭代是通过 **`for ... in`** 来实现的，它可以迭代任意 **可迭代对象**。需要注意的是，`dict` 默认迭代的是key。如果要迭代 value，可以用`for value in d.values()`，如果要同时迭代 key 和 value，可以用`for k, v in d.items()`。

那么，如何判断一个对象是可迭代对象呢？方法是通过`collections.abc`模块的`Iterable`类型判断：

```python
# 引入头文件
from collections.abc import Iterable

print(isinstance(&#39;abc&#39;, Iterable)) # str是否可迭代，输出 True
print(isinstance([1,2,3], Iterable)) # list是否可迭代，输出 True
print(isinstance(123, Iterable)) # 整数是否可迭代，输出 False
```

如果想要实现像其他语言一样的下标循环，可以使用Python内置的`enumerate`函数。

### 列表生成式

列表生成式，顾名思义，就是用来生成列表的。根据前面的知识我们可以使用`list(rang(1, 11))`的方式来生成list，但如果要生成`[1x1, 2x2, 3x3, ..., 10x10]`怎么做？

直接用循环是十分繁琐的，这时就可以使用列表生成式，列表生成式的学习可直接看[此教程](https://liaoxuefeng.com/books/python/advanced/list-comprehension/index.html)。

### 生成器

通过列表生成式，我们可以直接生成一系列的元素，直接创建一个列表。但是，受到内存限制，列表容量肯定是有限的。那有没有什么方法能逐个生成值，在需要的生成的时候才生成值呢？当然有！这时就可以使用Python中的生成器：**`generator`**。

要创建一个`generator`，有很多种方法。第一种方法很简单，只要把一个列表生成式的`[]`改成`()`，就创建了一个`generator`：

```python
g = (x * x for x in range(10)) # g就是一个生成器
print(g) # 输出：&lt;generator object &lt;genexpr&gt; at 0x7f12d2c69ba0&gt;

print(next(g)) # 输出: 0
print(next(g)) # 输出: 1
print(next(g)) # 输出: 2
```

通过next()函数可以获取生成器的下一个返回值，直到没有更多的元素时，没有更多的元素时，抛出`StopIteration`的错误。

当然，上面这种不断调用`next(g)`的做法是十分不优雅的，正确的方法是使用for循环，因为`generator`也是可迭代对象：

```python
g = (x * x for x in range(10))
for n in g:
    print(n)
```

&lt;br&gt;`generator`非常强大。如果推算的算法比较复杂，用类似列表生成式的for循环无法实现的时候，还可以用函数来实现。在函数中使用 **`yield`** 关键字，就可将函数转换为generator函数，调用generator函数将返回一个生成器。

generator函数的执行顺序和不同函数是不同的，generator函数在每次调用`next()`函数（或每次循环）时执行，遇到`yield`语句就返回，再次执行时从上次`yield`语句的 **下一条语句** 开始执行。

```python
def odd():
    print(&#34;step 1&#34;)
    yield 1
    print(&#34;step 2&#34;)
    yield (3)
    print(&#34;step 3&#34;)
    yield (5)


o1 = odd()

print(next(o1)) 
# 输出: 
# step 1
# 1

print(next(o1)) 
# 输出: 
# step 2
# 3

print(next(o1)) 
# 输出: 
# step 3
# 5

o2 = odd()

for n in o2:
    print(n)
# 依次输出跟前面一样的结果
```

&lt;span style=&#34;color: red;&#34;&gt;**Note:**&lt;/span&gt; 调用generator函数会创建一个generator对象，多次调用generator函数会创建 **多个相互独立** 的generator。即反复执行语句 `next(odd())`，它只会反复输出 `step 1`

&lt;br&gt;generator函数中当然也可以使用`return`语句，但你会发现直接是拿不到该函数的返回值的，因为当没有元素可以生成时它会抛出一个`StopIteration`错误，想要获得返回值就必须捕获 **`StopIteration`** 错误。

### 迭代器

在Python中，可以直接作用于for循环的对象统称为可迭代对象：**`Iterable`**，可以被`next()`函数调用并不断返回下一个值的对象称为迭代器：**`Iterator`**，迭代对象可通过`iter()`函数变为迭代器。

当我们使用for循环来变量一个可迭代对象时，Python会首先将调用 `iter()` 函数将该对象变为迭代器，再依次调用`next()`函数来获取每一个值。

更多详细信息可看[此教程](https://liaoxuefeng.com/books/python/advanced/iterator/index.html)。

## 函数式编程

### 高阶函数

跟其他语言一样，在Python中函数名也是变量，也可以赋值给其他变量，也可以将其指向其他对象（但不推荐这么做）。当一个函数接收另一个函数作为参数时，该函数就被称为高阶函数。

```python
# 这里的 add 函数就是高阶函数
def add(x, y, f):
    return f(x) &#43; f(y)
```

#### map/reduce

`map()` 函数会将指定的函数应用到可迭代对象的每个元素上，并返回一个新的 **迭代器对象**。它可以轻松地将同一操作应用到序列的每个元素上。

语法为：`map(function, iterable1, iterable2, ...)`  

* function：要应用的函数，必须是一个接收一个或多个参数的函数。
* iterable：一个或多个可迭代对象（如列表、元组、集合等），可以有多个可迭代对象。
* 返回值：map 对象（一个迭代器）。

示例：

```python
def f(x):
    return x * x


# 将列表中的每个数平方
nums = [1, 2, 3, 4]
squared = map(f, nums)
print(list(squared))  # 输出: [1, 4, 9, 16]
```

&lt;br&gt;如果有多个可迭代对象，`map()`会将它们的每个元素分别传递给函数参数，但 **长度必须一致**，否则会按最短的可迭代对象进行映射。

```python
def f(x, y):
    return x &#43; y


# 将两个列表对应位置的数相加
nums1 = [1, 2, 3, 4]
nums2 = [1, 2, 3, 4]
sum = map(f, nums1, nums2)
print(list(sum))  # 输出: [2, 4, 6, 8]
```

&lt;br&gt;`reduce`把一个函数作用在一个序列[x1, x2, x3, ...]上，这个函数 **必须接收两个参数**，`reduce`把结果继续和序列的下一个元素做累积计算，最后返回一个 **值**，其效果就是：  
**`reduce(f, [x1, x2, x3, x4]) 等价于 f(f(f(x1, x2), x3), x4)`**

示例：

```python
# 将一个整数序列变成一个整数
from functools import reduce


def fn(x, y):
    return x * 10 &#43; y


print(reduce(fn, [1, 2, 3, 4, 5, 6, 7]))

```

在 Python 3 中，`reduce()` 函数位于 `functools` 模块中，需要导入后使用。

#### filter

顾名思义，该函数的作用就是过滤序列，和map()类似，`filter()`也接收一个函数和一个序列，返回一个迭代器。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素。

例如，在一个list中，删掉偶数，只保留奇数，可以这么写：

```python
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 结果: [1, 5, 9, 15]
```

#### sorted

该函数用于排序，详细信息参考[此教程](https://liaoxuefeng.com/books/python/functional/higher-order-function/sorted/index.html)。

### 返回函数

在 Python 中，高阶函数可以返回一个函数作为结果。以下示例展示了如何定义一个求和函数并将其返回，而不是立即计算结果：

```python
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax &#43;= n
        return ax

    return sum

f = lazy_sum(1, 3, 5, 7, 9)
print(f())  # 输出 25

```

调用 `lazy_sum` 时，会返回一个函数（未计算的和函数），每次调用都会返回一个 **新的函数**，即使传入相同的参数。调用返回的函数时才会计算结果。

&lt;br&gt; 当一个函数内部定义了另一个函数，且内部函数引用了外部函数的局部变量，那么这种结构称为 **「闭包」**。闭包可以保存外部函数的状态，在外部函数返回后仍然可以访问这些状态。

在内部函数中使用的外部函数变量会被当成 **静态变量**（我是这样来理解的），所以，当你返回多个不同的内部函数时，它们的运行结果都是一样的。直接看例子：

```python
def count():
    fs = []
    for i in range(1, 4):

        def f():
            return i * i

        fs.append(f)
    return fs


f1, f2, f3 = count()

print(f1())
print(f2())
print(f3())

# 你以为的输出结果：
1
4
9

# 实际的输出结果：
9
9
9
```

当内容函数使用了外部函数的变量`i`时，该变量就变成了静态变量，而循环结束时，i的值为3。所以，返回的三个函数输出值都是9。

&lt;br&gt;如果想要对外部函数的变量进行赋值，就必须在内部函数中使用关键字`nonlocal`来声明该变量是外部函数的变量，否则会报错，直接看例子：

```python
# 下面的代码会报错，因为x作为局部变量是没有初始化的
def inc():
    x = 0
    def fn():
        # nonlocal x
        x = x &#43; 1
        return x
    return fn

f = inc()
print(f()) # 1
print(f()) # 2

#下面这样就是正确的，因为x作为外部变量已经初始化了
def inc():
    x = 0
    def fn():
        nonlocal x
        x = x &#43; 1
        return x
    return fn
```

### 匿名函数

同样的Python也支持`lambda`匿名函数，语法为：**`lambda arguments: expression`**

lambda函数能够接收零个或多个参数，一个表达式，不用写`return`，返回值就是该表达式的结果。

Python对匿名函数的支持有限，只有一些简单的情况下可以使用匿名函数。

### 装饰器

在 Python 中，装饰器是一种用于增强函数功能的**特殊函数**。装饰器通常用于添加日志、权限校验、性能测试、缓存等功能，而不改变原始函数的代码。它的实现基于 **闭包原理**，并且通常以 `@decorator_name` 的形式来使用。

装饰器接受一个函数作为输入，并返回一个新的函数（或原函数的修改版）。其语法如下：

```python
@decorator_name
def function_to_decorate():
    pass
```

这等价于：

`function_to_decorate = decorator_name(function_to_decorate)`

直接来看一个简单的示例：

```python
def simple_decorator(func):
    def wrapper():
        print(&#34;Function is being called.&#34;)
        func()
        print(&#34;Function has been called.&#34;)
    return wrapper

@simple_decorator
def say_hello():
    print(&#34;Hello!&#34;)

say_hello()
```

**输出：**

```bash
Function is being called.
Hello!
Function has been called.
```

在上面的代码中，`@simple_decorator` 装饰了 `say_hello` 函数，使得 `say_hello()` 在执行时自动执行 `wrapper` 中的其他逻辑。

&lt;br&gt;装饰器可以应用于带参数的函数。在这种情况下，装饰器内部的函数需要接收并转发参数：

```python
def decorator_with_args(func):
    def wrapper(*args, **kwargs):
        print(&#34;Arguments were:&#34;, args, kwargs)
        return func(*args, **kwargs)
    return wrapper

@decorator_with_args
def greet(name):
    print(f&#34;Hello, {name}!&#34;)

greet(&#34;Alice&#34;)
```

**输出：**

```bash
Arguments were: (&#39;Alice&#39;,) {}
Hello, Alice!
```

&lt;br&gt;装饰器也可以操作和返回被装饰函数的返回值：

```python
def square_decorator(func):
    def wrapper(x):
        result = func(x)
        return result ** 2
    return wrapper

@square_decorator
def double(x):
    return x * 2

print(double(3))  # 输出 36，因为 (3*2)^2 = 36
```

&lt;br&gt;有时我们希望装饰器本身可以接受参数，这可以通过嵌套函数来实现，即使装饰器变为三层嵌套：

```python
def repeat(num_times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(num_times):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(3)
def greet():
    print(&#34;Hello!&#34;)

greet()
```

**输出：**

```bash
Hello!
Hello!
Hello!
```

&lt;br&gt;Python 提供了 `functools.wraps` 来保留被装饰函数的元数据（如函数名、文档字符串等）。若不使用 wraps，装饰后的函数 `__name__` 和 `__doc__` 属性会变为 wrapper 函数的属性。

```python
from functools import wraps

def my_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(&#34;Calling decorated function&#34;)
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def example():
    &#34;&#34;&#34;This is an example function.&#34;&#34;&#34;
    print(&#34;Example function&#34;)

print(example.__name__)  # 输出: example
print(example.__doc__)   # 输出: This is an example function.
```

### 偏函数

Python的`functools`模块提供了很多有用的功能，其中一个就是偏函数（Partial function）。要注意，这里的偏函数和数学意义上的偏函数不一样。

在介绍函数参数的时候，我们讲到，通过设定参数的默认值，可以降低函数调用的难度。而偏函数也可以做到这一点。举例如下：

`int()`函数可以把字符串转换为整数，当仅传入字符串时，int()函数默认按十进制转换：  

```python
print(int(&#39;12345&#39;)) # 输出：12345
```

但int()函数还提供额外的base参数，默认值为10。如果传入base参数，就可以做N进制的转换：

```python
print(int(&#39;12345&#39;, base = 8)) # 输出：5349
print(int(&#39;12345&#39;, base = 16)) # 输出：74565
```

假设要转换大量的二进制字符串，每次都传入int(x, base=2)非常麻烦，于是，我们想到，可以定义一个int2()的函数，默认把`base=2`传进去：

```python
def int2(x, base=2):
    return int(x, base)

print(int2(&#39;10000&#39;)) # 直接将该字符串转换成二进制的数
```

`functools.partial`就是帮助我们创建一个偏函数的，不需要我们自己定义`int2()`，可以直接使用下面的代码创建一个新的函数`int2`：

```python
import functools

int2 = functools.partial(int, base=2)
print(int2(&#39;1000000&#39;))
```

所以，简单总结`functools.partial`的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。当然，你也可以给该新函数中有默认值的参数传值。

## 模块

前面已经介绍过，如何在conda中安装包，那么安装包后如何在程序中进行导入呢？其语法如下：

```python
import module_name
import math

print(match.sqrt(16))
```

该方法会导入整个模块，会允许访问该模块中所有公开属性和方法。使用该模块中的函数或属性时，需要加上模块名前缀。

&lt;br&gt;如果只想导入该模块中的部分函数或变量，可以使用语法：**`from module_name import ...`**

这种方式只会导入该模板的指定成员，可以直接使用它们而无需模块前缀名。

&lt;br&gt; 当我们想要模块中的某些操作在执行该模块时可以执行，而该模块被导入时不执行，可以使用 `if __name__==&#39;__main__&#39;:` 来进行操作。

Python 模块在 **首次导入** 时会被解释器执行（例如初始化变量、加载资源等）。如果该模块是我们通过在命令直接执行的，那么该模块的`__name__`会被赋值为 `__main__`，如果该模块是被导入时执行的，那么 `__name__`会被赋值为 `__该模块的文件名__`。

当某个函数或变量只想被该模块所访问，就可以以`_xxx`或`__xxx`的形式命令。这样就将其定义为非公开的，不应该被直接引用。

## 面向对象编程

### 类和实例

在Python中，类的定义语法如下所示：

```python
# 当没有合适的基类时，就写object，因为这是所有类的的基类
class ClassName(BaseName):
    pass
```

定义好一个类后，就可以创建该类的实例了，由于Python中没有指针，所以，只能通过 `类名()` 的方式来创建实例，然后通过 **`.`** 运算访问类的成员。

类的构造函数的定义语法如下所示：

```python
class Example(object):
    def __init__(self, arg1, arg2, ...):
        pass
```

**`init`** 前后有两个下划线，和普通函数相比，类成员函数的第一个参数必须是 **`self`**，表示实例本身（相当于其他语言的`this`）。在Python中，类成员变量是不需要提前声明的，直接使用 `self.变量名` 来创建成员变量。

### 访问限制

如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线 **`__`**，在Python中，实例的变量名如果以`__`开头，就变成了一个 **私有变量**（private），只有内部可以访问，外部不能访问。

&lt;span style=&#34;color: red;&#34;&gt;**Note:**&lt;/span&gt; 在Python中，变量名类似`__xxx__`的，也就是以双下划线开头，并且以双下划线结尾的，是 **特殊变量** ，不是`private`变量，特殊变量是可以直接访问的。

&lt;br&gt;有些时候，你会看到以一个下划线开头的实例变量名，比如`_name`，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

双下划线开头的实例变量是不是一定不能从外部访问呢？其实也不是。不能直接访问`__example`是因为Python解释器对外把`__example`变量改成了`_ClassName__exmaple`，所以，仍然可以通过`_ClassName__exmaple`来访问`__example`变量：

```python
class Student(object):
    def __init__(self, name):
        self.__name = name

    def get_name(self):
        return self.__name

    def set_name(self, name):
        self.__name = name


# 以下方式仍然可以直接访问类的私有成员
stu = Student(&#34;Ami&#34;)
print(stu._Student__name)
```

但是强烈建议不要这么干，因为不同版本的Python解释器可能会把`__name`改成不同的变量名。

总的来说就是，Python本身没有任何机制阻止你干坏事，一切全靠自觉。

&lt;br&gt;最后注意下面的这种 **错误写法**：

```python
class Student(object):
    def __init__(self, name):
        self.__name = name

    def get_name(self):
        return self.__name

    def set_name(self, name):
        self.__name = name


stu = Student(&#34;Ami&#34;)
stu.__name = &#34;New Name&#34;

print(stu.get_name())  # 输出：Ami
print(stu.__name)  # 输出：New Name
```

表面上看，外部代码“成功”地设置了`__name`变量，但实际上这个`__name`变量和`class`内部的`__name`变量不是一个变量！内部的`__name`变量已经被Python解释器自动改成了`_Student__name`，而外部代码给stu新增了一个`__name`变量。

&lt;br&gt;最后再次声明一下：在Python中，类实例的成员变量（属性）是动态的，这意味着你可以随时为实例添加新的属性，或者修改、删除已有的属性（通过实例也可以修改）。这是Python灵活性的体现，它允许你在运行时动态地改变对象的状态。所以，在Python中同一个类的实例是可以有不同的类成员的！！！一定要注意代码的组织和封装。

### 继承和多态

这没什么好说，跟其他语言一样理解就行。（虽然我感觉在Python中没什么作用，也就是让子类继承了父类的成员而已。因为你Python本来变量就是动态类型的，根本就不存在对于每个派生类都需要定义一个变量来调用的情况）

### 获取对象信息

一般使用 **`isinstance()`** 函数来判断一个对象是否是某种类型。

如果要获得一个对象的所有属性和方法，可以使用`dir()`函数，它返回一个包含字符串的`list`，比如，获得一个`str`对象的所有属性和方法：

```python
print(dir(&#39;ABC&#39;))
# 输出：[&#39;__add__&#39;, &#39;__class__&#39;,..., &#39;__subclasshook__&#39;, &#39;capitalize&#39;, &#39;casefold&#39;,..., &#39;zfill&#39;]
```

仅仅把属性和方法列出来是不够的，配合getattr()、setattr()以及hasattr()，我们可以直接操作一个对象的状态：

```python
 class MyObject(object):
    def __init__(self):
        self.x = 9

    def power(self):
        return self.x * self.x


obj = MyObject()

print(hasattr(obj, &#34;x&#34;))  # 有属性&#39;x&#39;吗？
print(getattr(obj, &#34;x&#34;))  # 获取属性&#39;x&#39;

print(hasattr(obj, &#34;power&#34;))  # 有属性&#39;power&#39;吗？
print(getattr(obj, &#34;power&#34;))  # 获取属性&#39;power&#39;

print(hasattr(obj, &#34;y&#34;))  # 有属性&#39;y&#39;吗？
setattr(obj, &#34;y&#34;, 23)  # 设置一个属性 &#39;y&#39;, 其值为 23
print(hasattr(obj, &#34;y&#34;))  # 有属性&#39;y&#39;吗？
print(getattr(obj, &#34;y&#34;))  # 获取属性&#39;y&#39;
```

**输出：**

```bash
True
9
True
&lt;bound method MyObject.power of &lt;__main__.MyObject object at 0x7fea3e503fd0&gt;&gt;
False
True
23
```

&lt;br&gt;如果试图获取不存在的属性，会抛出`AttributeError`的错误。`getattr()`函数可以传入一个default参数，如果属性不存在，就返回默认值：

```python
getattr(obj, &#39;z&#39;, 101) # 获取属性&#39;z&#39;，如果不存在，返回默认值101
```

### 实例属性和类属性

前面已经说过了，由于Python是动态语言，根据类创建的实例可以任意绑定属性。

给实例绑定属性的方法是通过 **实例变量**，或者通过 **`self`变量**：

```python
class Student(object):
    def __init__(self, name):
        self.name = name

s = Student(&#39;Bob&#39;)
s.score = 90 # 绑定了一个 score 成员
```

但是，如果`Student`类本身需要绑定一个属性呢？可以直接在`class`中定义属性，这种属性是类属性，归`Student`类所有：

```python
class Student(object):
    name = &#39;Student&#39;
```

当我们定义了一个类属性后，这个属性归类所有，类的所有实例都可以访问到（只要这个类没有定义相同名称的类属性）。来测试一下：

```python
class Student(object):
    name = &#34;Student&#34;


s = Student()  # 创建实例s
print(s.name)  # 打印name属性，因为实例并没有name属性，所以会继续查找class的name属性
print(Student.name)  # 打印类的name属性

s.name = &#34;Michael&#34;  # 给实例绑定name属性
print(s.name)  # 由于实例属性优先级比类属性高，因此，它会屏蔽掉类的name属性

print(Student.name)  # 但是类属性并未消失，用Student.name仍然可以访问

del s.name  # 如果删除实例的name属性
print(s.name)  # 再次调用s.name，由于实例的name属性没有找到，类的name属性就显示出来了

```

**输出：**

```python
Student
Student
Michael
Student
Student
```

当某个实例要访问某个属性时，会先去该实例中查找是否有相关属性，如果没有才会取类中查找是否有相关属性。即 **相同名称的实例属性会屏蔽掉类属性**。

## 面向对象高级编程

### 使用__slots__

在前面我们说了，Python是一个十分灵活的语言，每个类的实例都可以自己添加自己想要的属性（变量属性、函数属性都可以）。但这会占用很多内存，且可能会发生属性误用的情况。

&gt; 在 Python 中，每个对象的属性默认存储在一个叫 `__dict__` 的字典中。字典会占用额外的内存，特别是当类的实例数量较大、属性较少时，内存开销会显得较大。使用 `__slots__` 可以让 Python 不再为每个实例创建 `__dict__`，从而节省内存。

为了解决上述问题，我们可以使用`__slots__`来限制可以添加的实例属性。

```python
class MyClass:
    __slots__ = [&#39;name&#39;, &#39;age&#39;]  # 限定实例属性

    class_attr = &#34;I am a class attribute&#34; # 定义一个类属性

    def __init__(self, name, age):
        self.name = name
        self.age = age

obj = MyClass(&#34;Alice&#34;, 30)

# 访问实例属性
print(obj.name)  # 输出: Alice
print(obj.age)   # 输出: 30

# 尝试添加未在 __slots__ 中定义的实例属性
try:
    obj.gender = &#34;Female&#34;  # 会引发 AttributeError
except AttributeError as e:
    print(e)  # 输出: &#39;MyClass&#39; object has no attribute &#39;gender&#39;

# 访问类属性
print(MyClass.class_attr)  # 输出: I am a class attribute

# 修改类属性
MyClass.class_attr = &#34;New class attribute value&#34;
print(MyClass.class_attr)  # 输出: New class attribute value

# 动态添加新的类属性
MyClass.new_class_attr = &#34;Another class attribute&#34;
print(MyClass.new_class_attr)  # 输出: Another class attribute

```

&lt;span style=&#34;color: red;&#34;&gt;**Note:**&lt;/span&gt; `__slots__`只会限制可以添加的实例属性，不会限制类属性的修改。

### 使用@property

前面说过了，对于私有成员，我们要设置单独的函数来对其进行访问和修改，有没有什么方法能用类似属性这样简单的方式来访问类的变量呢？

当然有！Python内置的 **`@property`** 装饰器就是负责把一个方法变成属性调用的：

```python
class Student(object):
    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError(&#39;score must be an integer!&#39;)
        if value &lt; 0 or value &gt; 100:
            raise ValueError(&#39;score must between 0 ~ 100!&#39;)
        self._score = value
```

`@property`的实现比较复杂，我们先考察如何使用。把一个getter方法变成属性，只需要加上`@property`就可以了，此时，`@property`本身又自动创建了另一个装饰器`@score.setter`(你不使用这个新的自动创建的装饰器，那么该变量就是只读的)，负责把一个setter方法变成属性赋值，于是，我们就拥有一个可控的属性操作：

```python
s = Student()
s.score = 90 # OK，实际转化为s.set_score(90)
print(s.score) # OK，实际转化为s.get_score()

s.score = 9999
```

**输出：**

```bash
90
Traceback (most recent call last):
  ...
ValueError: score must between 0 ~ 100!
```

还可以定义只读属性，只定义getter方法，不定义setter方法就是一个只读属性：

```python
class Student(object):
    @property
    def birth(self):
        return self._birth

    @birth.setter
    def birth(self, value):
        self._birth = value

    @property
    def age(self):
        return 2015 - self._birth
```

上面的birth是可读写属性，而age就是一个 **只读属性**，因为age可以根据birth和当前时间计算出来。

&lt;span style=&#34;color: red;&#34;&gt;**Note:**&lt;/span&gt; 属性的方法名不要和实例变量重名。因为当属性方法名和实例变量重名，你在属性方法中调用同名的变量，会认为在调用属性方法，造成递归调用，导致栈溢出报错！

### 多重继承

和 **`C&#43;&#43;`** 一样，允许一个子类继承多个父类。

### 定制类

Python的`class`允许定义许多定制方法，可以让我们非常方便地生成特定的类。比如，`__str__`函数可以让print(类)是打印更简洁直接的内容等。更多信息可参考[此教程](https://liaoxuefeng.com/books/python/oop-adv/special-method/index.html)。

### 使用枚举类

Python中的枚举（Enumeration）类是一种特殊的类，用于定义一组命名的 **常量**。枚举类在Python 3.4中通过`enum`模块引入，提供了一种类型安全的方式来表示固定的一组值。使用枚举类可以提高代码的可读性和可维护性，尤其是在处理固定集合的值时，如一周的天数、月份、状态码等。

使用枚举类时，首先要从enum模块导入Enum类：`from enum import Enum`

定义一个枚举类与定义一个常规类类似，但是枚举类的成员会被自动赋值为枚举值（你不显示的赋值，它们将自动从1开始递增）：

```python
class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3

favorite_color = Color.RED
print(favorite_color)  # 输出: Color.RED

print(favorite_color.value)  # 输出: 1
print(favorite_color.name)  # 输出: &#39;RED&#39;
```

&lt;br&gt;枚举成员是可比较的，你可以使用`==`和`!=`操作符：

```python
if favorite_color == Color.RED:
    print(&#34;Favorite color is red.&#34;)
```

&lt;br&gt;你可以使用Enum类的`__members__`属性或者`enum.auto()`函数来获取枚举类的所有成员：

```python
# 使用 __members__ 属性
for name, member in Color.__members__.items():
    print(name, member)

# 使用 enum.auto()
for member in enum.auto():
    print(member)
```

## 错误、调式和测试

### 错误处理

在Python中，错误处理的语法为：

```python
try:
    ...
except class1Error as e:
    ...
except class1Error as e:
    ...
...
else:
    ...
finally:
    ...
```

其中 `else`和`finally`都是可选的，`else`是没有发生错误时要执行的内容，`finally`是最后执行的内容（无论是否发生异常情况）。

### 调式

直接使用IED里面的调式功能。

### 单元测试

编写Python中的单元测试，需要引用Python自带的`unittest`模块。（这里只是记录一下，需要用时才去学习，该模块就类似于C&#43;&#43;中的gtest）

### 文档测试

在 Python 中，`doctest` 模块是一种用于在文档字符串中直接嵌入测试代码的工具。通过 `doctest`，可以编写和运行与代码示例一致的测试，确保代码示例保持准确。`doctest` 常用于简单的单元测试或示例代码的验证。

`doctest` 会扫描文档字符串（docstring）中形如 Python 交互式解释器中的代码，并执行这些代码。若执行结果与示例中给出的结果不符，`doctest` 会报告错误。在函数的 `docstring` 中书写测试示例。示例代码以 `&gt;&gt;&gt;` 开头，结果紧跟在示例代码下方。

```python
def add(a, b):
    &#34;&#34;&#34;
    返回两个数的和。

    示例：
    &gt;&gt;&gt; add(2, 3)
    5
    &gt;&gt;&gt; add(-1, 1)
    0
    &#34;&#34;&#34;
    return a &#43; b

```

&lt;br&gt;`doctest` 的测试可以通过以下几种方式运行：

**方式一：命令行运行**
直接使用命令行执行该 Python 文件，添加 -m doctest 选项：

```bash
python -m doctest filename.py
```

此方式会扫描文件中的所有 `doctest`，并报告测试结果。默认情况下，如果测试通过则没有输出，仅当测试失败时才会显示错误信息。

**方式二：直接在代码中运行**
可以在代码中调用 `doctest.testmod()` 函数来运行测试：

```python
if __name__ == &#34;__main__&#34;:
    import doctest
    doctest.testmod()
```

此方式适用于在脚本中嵌入测试，运行脚本时自动测试。

&lt;br&gt;`doctest` 更适合简单的输入输出测试，复杂的逻辑测试通常使用 `unittest`模块。

## IO编程

先记录在这，要使用时再学习，[学习地址](https://liaoxuefeng.com/books/python/io/file/index.html)。

## 进程和线程

先记录在这，要使用时再学习，[学习地址](https://liaoxuefeng.com/books/python/process-thread/index.html)。

## 参考资料

1. https://liaoxuefeng.com/books/python/introduction/index.html


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/92c957a/  


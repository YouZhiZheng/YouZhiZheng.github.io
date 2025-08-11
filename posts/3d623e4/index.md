# Linux学习笔记

## 软件安装与更新

在Linux下，一般用包管理器来安装软件，它能自动化地完成软件的安装、更新、配置和移除功能，优雅而快速。

软件包管理器的一个重要组成部分是**软件仓库**。软件仓库是收藏了互联网上可用软件包（应用程序）的图书馆，里面往往包含了数万个可供下载
和安装的可用软件包。

有了软件仓库，我们不需要手动下载大量的软件包再通过包管理器安装。只需要知道软件在软件仓库中的名称，即可让包管理器从网络中抓取到相应的软件包到本地，自动进行安装。

但是与应用商店相比，使用包管理器安装需要预先知道所需软件在软件仓库中的对应包名，和应用商店相比无法进行模糊搜索（不过你也可以在包管理器官网上进行查找包名，再通过包管理器安装）。

作者使用的是`Ubuntu`系统，`Ubuntu`下默认的包管理器为`apt`，所有后面的讲解以`apt`为例。

### 使用包管理器系统安装

#### 搜索

安装前，先在浏览器中搜索所需的软件，查找包名，再使用 **`apt search package_name`** 命令搜索软件仓库，查看对应的包名是否在软件仓库中(这步可跳过，只要知道要安装的软件包名即可)。

#### 安装

找到要安装的软件的包名后，使用命令 **`apt install package_name`** 进行安装。

**PS：** 如果提示当前用户无安装软件权限，则在命令前加上 `sudo`，即 **`sudo apt install package_name`**，输入此命令后会要求用户输入密码(**输入的密码不会在终端显示**)。

#### 官方软件源镜像

通过 apt 安装的软件都来源于相对应的软件源，每个 Linux 发行版一般都带有官方的软件源，在官方的软件源中已经包含了丰富的软件，apt 的软件源列表在 `/etc/apt/sources.list` 下。

Ubuntu 官方源位于国外，往往会有速度与延迟上的限制，可以通过修改官方源为其镜像实现更快的下载速度。镜像缓存了官方源中的软件列表，与官方源基本一致。

&gt;**修改官方源为镜像步骤**
&gt;
&gt;本例以修改官方源为 USTC Mirror 为例。**注意：在操作前请做好备份**。
&gt;
&gt;一般情况下，/etc/apt/sources.list 下的官方源地址为 `http://archive.ubuntu.com/` ，我们只需要将其替换为 `http://mirrors.ustc.edu.cn` 即可。
&gt;
&gt;如果你使用 Ubuntu 图形安装器安装，默认的源地址通常不是 `http://archive.ubuntu.com/` ， 而是 `http://&lt;country-code&gt;.archive.ubuntu.com/ubuntu/` ，如 `http://cn.archive.ubuntu.com/ubuntu/`，同样也将其替换为 `http://mirrors.ustc.edu.cn` 即可。
&gt;
&gt;可以使用如下命令：
&gt;
&gt;
&gt;`$ sudo sed -i &#39;s|//.*archive.ubuntu.com|//mirrors.ustc.edu.cn|g&#39; /etc/apt/sources.list`
&gt;
&gt;当然也可以直接使用 vim、nano 等文本编辑器进行修改。

#### 第三方软件源

有时候，由于种种原因，官方软件源中并没有我们需要的软件，但是第三方软件提供商可以提供自己的软件源。在将第三方软件源添加到 /etc/apt/sources.list 中之后，就可以获取到第三方提供的软件列表，再通过 **`apt install package_name`** 安装我们需要的第三方软件。一般可以在需要的第三方软件官网找到对应的配置说明。

### 使用包管理器手动安装

在一些情况下，软件仓库中并没有加入我们所需要的软件。除了添加第三方软件源，从源安装外，有时候还可以直接下载安装软件供应商打包好的 `deb`、`rpm` 等二进制包。

安装软件包需要相应的软件包管理器，deb 格式的软件包对应的是 `dpkg`。

相对于 `apt` 而言，`dpkg` 会更加底层，`apt` 是一个包管理器的前端，并不直接执行软件包的安装工作，是交由 `dpkg` 完成。`dpkg` 反馈的依赖信息则会告知 `apt` 还需要安装的其他软件包，并从软件仓库中获取到相应的软件包进行安装，从而完成依赖管理问题。

直接通过 `dpkg` 安装 deb 并不会安装需要的依赖，只会报告出相应的依赖缺失了。

所以，推荐使用`apt`来安装`dev`文件，命令如下：
**`sudo apt install devFileName.dev`**

如果使用`dpkg -i` 安装软件，导致出现依赖包问题，可以使用命令：
**`sudo apt -f install`** 来帮助修复依赖管理

### 更新软件列表与更新软件

在计算机本地，系统会维护一个包列表，在这个列表里面，包含了软件信息以及软件包的依赖关系，在执行 `apt install` 命令时，会从这个列表中读取出想要安装的软件信息，包括下载地址、软件版本、依赖的包，同时 apt 会对依赖的包递归执行如上操作，直到不再有新的依赖包。如上得到的所有包，将会是在 `apt install some-package` 时安装的。

使用命令：**`sudo apt update`**  获取新的软件版本、软件依赖关系。在更新好包列表后，再使用命令 **`sudo apt upgrade`** 将安装的软件进行更新升级。

## 文件和目录操作

在 Linux 在进行操作文件与目录是使用 Linux 最基础的一个技能。不像在 Windows 和 macOS 下有图形化界面，拖拽文件即可完成文件的移动，很容易管理文件与目录，Linux 的命令行操作虽然繁琐一些，但一旦上手，就可以通过命令与参数的组合完成通过图形化界面难以实现或者无法实现的功能。

### 查看文件内容

#### `cat`命令

使用该命令会一次性将整个文件内容打印到终端，适用于看内容不多的文件，**`cat`** 命令用法如下：

1. 查看 file.txt 的全部内容  
   **`cat file.txt`**
2. 查看 file1.txt 与 file2.txt 连接后的全部内容  
   **`cat file1.txt file2.txt`**

#### `less`命令

该命令与`cat`命令的区别在于，此命令一次只会显示一页，且支持向前/后滚动、搜索等功能，适用于查看大型文件。用法如下：  
**`less FILE`**

在查看文件时，可以使用以下快捷键：
| 按键                             | 效果                              |
| :------------------------------- | :-------------------------------- |
| `d`/`u`                          | 向下/上滚动半页                   |
| `f`/`b` or `Page Down`/`Page Up` | 向下/上滚动一页                   |
| `g`/`G`                          | 跳转到文件开头/结尾               |
| `j`/`Down`                       | 向下移动一行                      |
| `k`/`Up`                         | 向上移动一行                      |
| `/PATTERN`                       | 在文件中搜索内容PATTERN           |
| `n`/`N`                          | 跳转到下一个/上一个找到的搜索内容 |
| `q`                              | 退出                              |

### 编辑文件内容

Nano 是在很多机器上自带的命令行文本编辑器，相比于 vim 和 emacs 来说，对新手更加友好，不需要提前记忆复杂的键位。

命令：`nano file.txt`   使用 nano 编辑 file.txt 文件，如果没有则创建。  
Nano 启动后，用户可以**直接开始输入需要的内容**，使用**方向键**移动光标。在终端最下方是 nano 的快捷键，^ 代表需要按下 Ctrl 键（例如，`^X` 就是需要同时按下 `Ctrl &#43; X`）。在编辑完成后，按下 `Ctrl &#43; X`，确认是否保存后即可。
___
当然也可以使用 Vi/Vim 来编辑文件内容。  
&gt;Vim 被誉为「编辑器之神」，但是其陡峭的学习曲线也让人望而却步。因为不一定所有的机器上都有 nano，但是可以肯定几乎所有的机器上都会安装 vi（vim 的前身），所以了解如何使用 vim，恐怕是一件难以避免的事情。所幸，vim 的基础操作并不算难。图形界面下也可以安装 `gvim` 获得图形界面。
&gt;
&gt;在打开 vim 后，默认进入的是普通模式，按下 `i` 就进入编辑模式，进入编辑模式后就可以随意编辑文件了。在这两个模式中，都可以使用键盘方向键移动光标。在编辑完成后，按下 `Esc` 回到普通模式，然后输入 `:wq` 就可以保存文件并退出；如果不想保存文件直接退出，则输入 `:q!` 即可。
&gt;
&gt;以上的简单教学已经可以帮助你正常操作 vim 了，vim 也自带 `vimtutor` 教学程序(非常好！！！墙裂推荐，直接在命令行输入`vimtutor`即可启动程序)，可以帮助你快速掌握 vim 的基本操作。熟练使用 vim 有助于提高编辑文本时的工作效率。

### 复制文件和目录

命令格式为：**`cp [opt1] [opt2] ... [optn] source dest`**

* `source` 表示要复制的文件或目录的路径
* `dest` 表示目标文件或目录的路径
* `[opt]` 表示可选参数  
  常用参数如下：  
  | 参数                      | 含义                             |
  | :------------------------ | :------------------------------- |
  | `-r`, `-R`, `--recursive` | 递归复制，常用于复制目录         |
  | `-f`, `--force`           | 覆盖目标地址同名文件             |
  | `-u`, `--update`          | 仅当源文件比目标文件新才进行复制 |
  | `-l`, `--link`            | 创建硬链接                       |
  | `-s`, `--symbolic-link`   | 创建软链接                       |
  &lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;硬链接和软链接&lt;/summary&gt;
  简单而言，一个文件的硬链接和软链接都指向文件自身，但是在底层有不同的实现。
  
  需要先了解一个概念：inode。在许多“类 Unix 文件系统”中，inode 用来描述文件系统的对象，如文件和目录。inode 记录了文件系统对象的属性和磁盘块的位置。可以被视为保存在磁盘中的文件的索引（英文：index node）。
  
  关于 inode 的进一步讲解可以参考[这篇文章](https://www.ruanyifeng.com/blog/2011/12/inode.html)。
  ![图1](/PostsImgs/Linux_Learning_Note_imgs/picture1.png)  
  硬链接与源文件有着相同的 inode，都指向磁盘中的同一个位置。删除其中一个，并不影响另一个。
  
  软链接与源文件的 inode 不同。软链接保存了源文件的路径，在访问软链接的时候，访问的路径被替换为源文件的路径，因此访问软链接也等同于访问源文件。但是如果删除了源文件，软链接所保存的路径也就无效了，软链接因此也是无效的。

  `ln`命令也可以用来硬链接和软链接  

  ```bash
   ln -s file symlink  # 创建指向文件 file 的软链接 symlink
   ln file hardlink  # 创建指向文件 file 的硬链接 hardlink
  ```

&lt;/details&gt;

下面是一些`cp`命令的使用示例：

1. 将 `file.txt` 文件复制到当前目录下的 `dir/` 下  
   **`cp file1.txt dir/`**
2. 将目录 `dir1/` 及其所有内容递归地复制到当前目录下的 `dir2/` 中  
   **`cp -r dir1/ dir2/`**
3. 将文件 `file1.txt` 复制为 `file2.txt`，并且在复制之前询问是否覆盖已存在的 `file2.txt`  
   **`cp -i file1.txt file2.txt`**
4. 将文件 `file1.txt` 复制为 `file2.txt`，并且强制覆盖已存在的 `file2.txt`，而不提示  
   **`cp -f file1.txt file2.txt`**
5. 递归地复制当前目录下的 `dir1/` 及其所有内容到当前目录下的 `dir2/`，并且保留源文件的权限、所有权和时间戳信息  
   **`cp -rp dir1/ dir2/`**
6. 将多个文件一同复制到当前目录下的`dir2/`中  
   **`cp file1.txt file2.txt file3.txt dir2/`**

### 移动文件和目录

命令格式为：**`mv [opt1] [opt2]... source dest`**
`mv`与`cp` 的使用方式类似(source文件也可以有多个)，效果就是 Windows 下的**剪切**，即`mv`命令默认是递归的。

常用参数：
| 参数 | 含义                             |
| :--- | :------------------------------- |
| -f   | 覆盖目标地址同名文件             |
| -u   | 仅当源文件比目标文件新才进行移动 |

### 删除文件和目录

命令格式：**`rm [opt1] [opt2] ... FILE ...`**

常用参数：
| 参数 | 含义                                                       |
| :--- | :--------------------------------------------------------- |
| -f   | 不需要提示，直接删除文件，即使文件属性为只读               |
| -i   | 删除前逐一询问是否删除                                     |
| -r   | 递归删除目录下的文件。删除**除空目录外的目录**必须加此参数 |

```bash
rm file1.txt # 删除当前目录下的 file1.txt 文件
rm -r test/ # 删除当前目录下的 test 目录
rm -rf test1/ test2/ file1.txt #删除当前目录下的 test1、test2目录 和 file1.txt 文件
```

### 创建目录

命令格式：**`mkdir [opt1] ... DirName ...`**

常用参数：
| 参数 | 含义                                                           |
| :--- | :------------------------------------------------------------- |
| -p   | 如果中间目录不存在，则创建；如果要创建的目录已经存在，则不报错 |

```bash
mkdir test1 #在当前目录下创建目录test1
mkdir test1 test2 #在当前目录下创建目录test1和test2
mkdir -p test1/test2/test3/ #在当前目录下创建路径 test1/test2/test3
```

### 创建文件

命令格式：**`touch file1 ...`**

```bash
touch file1.txt test.md #在当前目录下创建 file1.txt 和 test.md 文件
```

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;为什么名字是touch而不是create呢？&lt;/summary&gt;
  touch 工具实际上的功能是修改文件的访问时间（access time, atime）和修改时间（modification time, mtime），可以当作是摸（touch）了一下文件，使得它的访问与修改时间发生了变化。当文件不存在时，touch 会创建新文件，所以创建文件也就成为了 touch 最常见的用途。
&lt;/details&gt;

使用 **`stat filename`** 命令可以查看文件的属性信息。

### 搜索文件和目录

命令格式：**`find PATH [opt1] [opt2] ...`**

* PATH 用于指定查找路径。如果不指定，则默认在当前目录下进行查找，即 `find .` 等价于 `find`

常用参数：
| 参数            | 含义                                                           |
| :-------------- | :------------------------------------------------------------- |
| `-name &#39;*.ext&#39;` | 查找后缀为ext的文件，*是任意匹配符                             |
| `-type d`       | 指定查找的文件类型为目录                                       |
| `-size &#43;1M`     | 查找文件大小大于1M的文件，&#43;表示大于这个大小，-表示小于这个大小 |
| `-or`           | 或运算符，表示它前后两个条件满足一个即可                       |

```bash
find -name &#39;report.pdf&#39; #在当前目录下查找 report.pdf 文件
find / -size &#43;1G #在全盘查找大于1G的文件
find ~/ -name &#39;node_modules&#39; -type d #在用于目录下查找名为node_modules的目录
```

### 模式匹配

许多现代的 shell 都支持一定程度的模式匹配。举个例子，bash 的匹配模式被称为 [glob](https://mywiki.wooledge.org/glob)，支持的操作如下：  
| 模式     | 匹配的字串                                                  |
| :------- | :---------------------------------------------------------- |
| `*`      | 任意字串                                                    |
| `foo*`   | 以foo开头的字串                                             |
| `*x*`    | 含有x的字串                                                 |
| `?`      | 一个任意字符                                                |
| `a?b`    | `acb`、`a0b`等，但不能是`accb`这种，`?`只能表示一个任意字符 |
| `*.[ch]` | 以 `.c` 或 `.h` 结尾的字串                                  |

和上面提到的命令结合，可以显著提高操作效率。例如：

```bash
rm *.tar.gz #删除当前目录下所有以 .tar.gz 结尾的压缩文件
mv *.[ch] /path #将当前目录及子目录下所有以 .c 和 .h 结尾的文件移动到 /path 下
```

**PS：** &lt;span style=&#34;color: red;&#34;&gt;使用通配符前请再三确认输入无误，否则可能出现严重的后果（建议结合ChatGPT来使用）。&lt;/span&gt;

### 使用tar操作文档、压缩文件

经常，我们希望将许多文件打包然后发送给其他人，这时候就会用到 tar 命令，作为一个存档工具，它可以将许多文件打包为一个存档文件。

通常，可以使用其自带的 gzip 或 bzip2 算法进行压缩，生成压缩文件：**`tar [opt1] [opt2] ... FILE ...`**

常用选项：

| 选项                       | 含义                                         |
| -------------------------- | -------------------------------------------- |
| `-A`                       | 将一个存档文件中的内容追加到另一个存档文件中 |
| `-r`, `--append`           | 将一些文件追加到一个存档文件中               |
| `-c`, `--create`           | 从一些文件创建存档文件                       |
| `-t`, `--list`             | 列出一个存档文件的内容                       |
| `-x`, `--extract`, `--get` | 从存档文件中提取出文件                       |
| `-f`, `--file=ARCHIVE`     | 使用指定的存档文件                           |
| `-C`, `--directory=DIR`    | 指定输出的目录                               |

添加压缩选项可以使用压缩算法进行创建压缩文件或者解压压缩文件：

| 选项                                   | 含义                        |
| -------------------------------------- | --------------------------- |
| `-z`, `--gzip`, `--gunzip`, `--ungzip` | 使用 gzip 算法处理存档文件  |
| `-j`, `--bzip2`                        | 使用 bzip2 算法处理存档文件 |
| `-J`, `--xz`                           | 使用 xz 算法处理存档文件    |

下面是一些`tar` 的使用示例：

```bash
tar -c -f target.tar file1 file2 file3   # 将 file1、file2、file3 打包为 target.tar
tar -x -f target.tar -C test/            # 将 target.tar 中的文件提取到 test 目录中
tar -cz -f target.tar.gz file1 file2 file3   # 将 file1、file2、file3 打包，并使用 gzip 算法压缩，得到压缩文件 target.tar.gz
tar -xz -f target.tar.gz -C test/  # 将压缩文件 target.tar.gz 解压到 test 目录中
tar -Af archive.tar archive1.tar archive2.tar archive3.tar # 将 archive1.tar、archive2.tar、archive3.tar 三个存档文件中的文件追到 archive.tar 中
tar -t -f target.tar   # 列出 target.tar 存档文件中的内容
tar -tv -f target.tar   # 列出 target.tar 存档文件中的文件的详细信息
```

与大部分 Linux 命令相同，tar 命令允许将多个单字母（使用单个 `-` 符号的）选项组合为一个参数，便于用户输入。例如，以下命令是等价的：

```bash
tar -c -z -v -f target.tar test/
tar -czvf target.tar test/
tar -f target.tar -czv test/
```

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;存档文件的后缀名&lt;/summary&gt;
   后缀名并不能决定文件类型，但后缀名通常用于帮助人们辨认这个文件的可能文件类型，从而选择合适的打开方法。
  
   在第一个例子中，创建得到的文件名为 `target.tar`，后缀名为 `tar`，表示这是一个没有进行压缩的存档文件。

   在第二个例子中，创建得到的文件名为 `target.tar.gz`。将 `tar.gz` 整体视为后缀名，可以判断出，为经过 gzip 算法压缩（`gz`）的存档文件（`tar`）。可知在提取文件时，需要添加 `-z` 选项使其经过 gzip 算法处理后再进行正常 tar 文件的提取。

   同样地，通过不同压缩算法得到的文件应该有不同的后缀名，以便于选择正确的参数。如经过 `xz` 算法处理得到的存档文件，其后缀名最好选择 `tar.xz`，这样可以知道为了提取其中的文件，应该添加 `--xz` 选项。
&lt;/details&gt;

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;为什么使用 tar 创建压缩包需要两次处理？&lt;/summary&gt;
  tar 名字来源于英文 tape archive，原先被用来向只能顺序写入的磁带写入数据。tar 格式本身所做的事情非常简单：把所有文件（包括它们的“元数据”，包含了文件权限、时间戳等信息）放在一起，打包成一个文件。&lt;span style=&#34;color: red;&#34;&gt;注意，这中间没有压缩的过程&lt;/span&gt;。
  
  为了得到更小的打包文件，方便存储和网络传输，就需要使用一些压缩算法，缩小 tar 文件的大小。这就是 tar 处理它自己的打包文件的逻辑。在 Windows 下的一部分压缩软件中，为了获取压缩后的 tar 打包文件的内容，用户需要手动先把被压缩的 tar 包解压出来，然后再提取 tar 包中的文件。
&lt;/details&gt;

___
在 Linux 上的 tar 一般只支持 gzip、bzip、xz 和 lzip 几种压缩算法。如果需要解压 Windows 上更为常见的 7z、zip 和 rar 等，则需要寻求替代软件，这里推荐使用 [**unar**](#chapter-1) 软件。

## 进程

简单而不太严谨地来说，进程就是正在运行的程序：当我们启动一个程序的时候，操作系统会从硬盘中读取程序文件，将程序内容加载入内存中，之后 CPU 就会执行这个程序。

进程是现代操作系统中必不可少的概念。在 Windows 中，我们可以使用「任务管理器」查看系统运行中的进程；Linux 中同样也有进程的概念。下面简单介绍 Linux 中的进程。

### 查看当前运行的进程

#### 使用[htop](#chapter-2)软件

`htop` 可以简单方便查看当前运行的所有进程，以及系统 CPU、内存占用情况与系统负载等信息，直接在命令行输入 `htop` 即可启动。

使用鼠标与键盘都可以操作 `htop`。`Htop` 界面的最下方是一些选项，使用鼠标点击或按键盘的 `F1` 至 `F10` 功能键可以选择这些功能，常用的功能例如搜索进程（`F3`, Search）、过滤进程（`F4`, Filter，使得界面中只有满足条件的进程）、切换树形结构/列表显示（`F5`, Tree/List）等等。

#### 使用 `ps` 命令

`ps`（process status）是常用的输出进程状态的工具。直接在命令行输入 `ps` 命令即可启动。

 `ps` 命令**仅会显示本终端**中运行的相关进程，如果需要**显示所有进程**，对应的命令为 `ps aux`。

### 进程标识符

首先，有区分才有管理。**进程标识符**（PID，Process Identifier）是一个数字，是**进程的唯一标识**。在 htop 中，最左侧一列即为 `PID`。当用户想挂起、继续或终止进程时可以使用 `PID` 作为索引。

在 htop 中，直接单击绿色条内的 `PID` 栏，可以将进程顺序按照 `PID` 升序排列，再次点击为降序排列，同理可应用于其他列。  
![图2](/PostsImgs/Linux_Learning_Note_imgs/picture2.png)

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;Linux进程启动顺序&lt;/summary&gt;
  按照 PID 排序时，我们可以观察系统启动的过程。Linux 系统内核从引导程序接手控制权后，开始内核初始化，随后变为 init_task，初始化自己的 PID 为 0。随后创建出 1 号进程（init 程序，目前一般为 systemd）衍生出用户空间的所有进程，创建 2 号进程 kthreadd 衍生出所有内核线程。随后 0 号进程成为 idle 进程，1 号、2 号并非特意预留，而是产生进程的自然顺序使然。
  
  由于 kthreadd 运行于内核空间，故需按大写 K（`Shift` &#43; `k`）键显示内核进程后才能看到。然而无论如何也不可能在 htop 中看到 0 号进程本体，只能发现 1 号和 2 号进程的 PPID 是 0。
&lt;/details&gt;

### 进程优先级与状态

我们平时使用操作系统的时候，可能同时会开启浏览器、聊天软件、音乐播放器、文本编辑器……前面提到它们都是进程，但是单个 CPU 核心一次只能执行一个进程。为了让这些软件看起来 **「同时」** 在执行，操作系统需要用非常快的速度将计算资源在这些进程之间切换，这也就引入了进程优先级和进程状态的概念。

#### 优先级与 nice 值

有了进程，谁先运行？谁给一点时间就够了，谁要占用大部分 CPU 时间？这又是如何决定的？这些问题之中体现着 **「优先权」** 的概念。如果说上面所介绍的那些进程属性描述了进程的控制信息，那么 **「优先权」** 则反映操作系统调度进程的手段。

在 **htop** 的显示中有两个与优先级有关的值： **Priority（PRI）** 和 **nice（NI）**。以下主要介绍**用户层使用的 nice 值**。

Nice 值越高代表一个进程对其它进程越 &#34;nice&#34;（友好），对应的优先级也就更低。Nice 值最高为 19，最低为 -20。通常，我们运行的程序的 nice 值为 0。我们可以打开 htop 观察与调整每个进程的 nice 值。

用户可以使用 `nice` 命令在运行程序时指定优先级，而 `renice` 命令则可以重新指定优先级。当然，若想调低 nice 值，还需要 `sudo`（毕竟不能随便就把自己的优先级设置得更高，不然对其他的用户不公平）。

```bash
nice -n 10 vim # 以 10 为 nice 值运行 vim
renice -n 10 -p 12345 # 设置 PID 为 12345 的进程的 nice 值为 10
```

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;PRI值&lt;/summary&gt;
  如果你在 htop 中测试调整进程的 nice 值，可能会发现一个公式：「PRI = nice &#43; 20」。这对于普通进程是成立的——普通进程的 PRI 会被映射到一个「非负」整数。
  
  但在正常运行的 Linux 系统中，我们可能会发现有些进程的 PRI 值是 RT，或者是负数。这表明对应的进程有更高的实时性要求（例如内核进程、音频相关进程等），采用了与普通进程不同的调度策略，优先级也相应更高。
&lt;/details&gt;

#### 进程状态

配合上面的进程调度策略，我们可以粗略地将进程分为三类：

1. 一类是正在运行的程序，即处于**运行态**（running）
2. 一类是可以运行但正在排队等待的程序，即处于**就绪态**（ready）
3. 最后一类是正在等待其他资源（例如网络或者磁盘等）而无法立刻开始运行的程序，即处于**阻塞态**（blocked）。

调度时操作系统轮流选择可以运行的程序运行，构成就绪态与运行态循环。运行中的程序如果需要其他资源，则进入阻塞态；阻塞态中的程序如果得到了相应的资源，则进入就绪态。

在实际的 Linux 系统中，进程的状态分类要稍微复杂一些。在 htop 中，按下 H 键到帮助页，可以看到对进程状态的如下描述：

**`Status: R: running; S: sleeping; T: traced/stopped; Z: zombie; D: disk sleep`**

其中 R 状态对应上文的运行和就绪态（即表明该程序可以运行），S 和 D 状态对应上文阻塞态。

需要注意的是，S 对应的 sleeping 又称 interruptible sleep，字面意思是「可以被中断」；而 D 对应的 disk sleep 又称 uninterruptible sleep，不可被中断，一般是因为阻塞在磁盘读写操作上。

Zombie 是僵尸进程，该状态下进程已经结束，只是仍然占用一个 PID，保存一个返回值。而 traced/stopped 状态正是下文使用 Ctrl &#43; Z 导致的挂起状态（大写 T），或者是在使用 gdb 等调试（Debug）工具进行跟踪时的状态（小写 t）。

进程状态表：
| 状态             | 缩写表示 | 说明                  |
| :--------------- | :------- | :-------------------- |
| Running          | R        | 正在运行/可以立刻运行 |
| Sleeping         | S        | 可以被中断的睡眠      |
| Disk Sleep       | D        | 不可以被中断的睡眠    |
| Traced / Stopped | T        | 被跟踪/被挂起的进程   |
| Zombie           | Z        | 僵尸进程              |

## 用户进程控制

要想控制进程，首先要与进程对话，那么必然需要了解进程间通信机制。由于 **「进程之间不共享自己的内存空间」**，也就无法直接发送信息，必须要操作系统帮忙，于是信号机制就产生了。

### 信号

信号是 Unix 系列系统中进程之间相互通信的一种机制。发送信号的 Linux 命令叫作 **`kill`**。被称作 &#34;kill&#34; 的原因是：早期信号的作用就是关闭（杀死）进程。

信号列表可以使用命令 **`man 7 signal`** 查看。  
![图3](/PostsImgs/Linux_Learning_Note_imgs/picture3.png)

### 前后台切换

上面的图片中，出现了 **`fg`，`bg`** 和 **`Ctrl &#43; Z`**，涉及到的正是 shell 中前后台的概念。

**一个程序运行在前台指的是：**

* 当前终端窗口会不能接受其他指令，直到当前程序暂停或运行结束
* 关闭当前终端窗口时，该程序也会被关闭

**一个程序运行在后台指的是：**

* 程序运行在后台，当前终端窗口可以接受其他指令
* 关闭当前终端窗口时，该程序也会被关闭

**PS：** 无论程序运行在前台还是后台，如果有输出到终端的程序指令，如cout，cerr等，都会在运行的终端上输出

默认情况下，在 shell 中运行的命令都在前台运行，如果需要在后台运行程序，需要在最后加上 **`&amp;`** 符号：

```bash
./matmul &amp;  # 例子：将耗时的计算放在后台运行同时进行其他操作
ps
    PID TTY          TIME CMD
   1720 pts/0    00:00:00 bash
   1861 pts/0    00:00:06 matmul
   1862 pts/0    00:00:00 ps
# 使用 ps 命令，可以发现 matmul 程序在后台运行，同时我们仍然可以操作 shell
```

而如果需要将**前台程序切换到后台**，则需使用命令 **「`Ctrl &#43; Z`」** 发送 SIGTSTP 信号使进程挂起（即将程序暂停运行并放入后台），控制权还给 shell。再使用 **「`jobs`」** 命令，查看当前 shell 上所有相关进程。

此时屏幕输出如下所示，刚才挂起的进程 **代号**为 2（**不是pid**），状态为 stopped，命令为 **`ping localhost`**。  
![图4](/PostsImgs/Linux_Learning_Note_imgs/picture4.gif)

任务前的代号在 `fg`，`bg`，乃至 `kill` 命令中发挥作用。使用时需要在前面加 `%`。

```bash
bg %2 #表示让代号为2的任务在后台继续运行
fg %2  #表示让代号为2的任务在前台继续运行
```

也许你注意到了编号后面跟着 `&#43;` 和 `-` 号：

```bash
[1]  - running    ./signal handle
[2]  &#43; suspended  ping localhost
```

这里的加号标记了 `fg` 和 `bg` 命令默认作用到的任务为 2，所以这里 `bg %2` 也可以直接简化为 `bg`。减号表示如果加号标记的进程退出了，它就会成为加号标记进程。我们也可以用 `%&#43;` 和 `%-` 指代这两个任务。

### 终止进程

正如上所述，许多信号都会引发进程的终结，然而标准的终止进程信号是 SIGTERM，意味着一个进程的自然死亡。

#### 使用 htop 发送信号

启动 htop 后，使用 **「空格」** 标记要发送信号的进程(被标记的进程会变色)，再按下 **K** 键，在左侧提示栏中选择要发送的信号，按下回车发送信号。

#### 使用 kill 命令发送信号

命令格式：`kill -signal PID`

* -signal 参数为信号的名称或编号。如果不使用此参数，将默认使用 **15（SIGTERM）** 作为信号参数

使用 `kill -l` 可查看所有信号

```bash
$ kill -l # 显示所有信号名称
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN&#43;1  36) SIGRTMIN&#43;2  37) SIGRTMIN&#43;3
38) SIGRTMIN&#43;4  39) SIGRTMIN&#43;5  40) SIGRTMIN&#43;6  41) SIGRTMIN&#43;7  42) SIGRTMIN&#43;8
43) SIGRTMIN&#43;9  44) SIGRTMIN&#43;10 45) SIGRTMIN&#43;11 46) SIGRTMIN&#43;12 47) SIGRTMIN&#43;13
48) SIGRTMIN&#43;14 49) SIGRTMIN&#43;15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
```

### 脱离终端

前面说过，无论是前台还是后台运行的程序，只要关闭 shell 窗口(即终端)，这些程序都会停止运行。这是因为终端一旦被关闭会向其中每个进程发送 **SIGHUP**（Signal hangup）信号，而 SIGHUP 的默认动作即退出程序运行。

经常使用 SSH 连接到远程服务器执行任务的人都知道，shell 中执行的程序在 SSH 断开之后会被关闭（SSH 断开视为关闭终端）。如果想要程序在关闭shell窗口或SSH断开后仍然能继续运行，则可以在命令前加上 **`nohup`** 命令。该命令字面含义就是「不要被 SIGHUP 影响」。

例如：**`nohup ping 101.ustclug.org &amp;`**

### 命令行多终端方式

一个终端（硬件概念）只有一套鼠标键盘，只能有一个 shell 主持一个 session，那如果我在 SSH 连接的时候有几个程序需要同时交互的话，只有一个前台进程很不方便。而且上面说过如果 SSH 网络断开，视为终端被关闭，也就意味着前后台一起收到 SIGHUP 一起退出，好不容易设置好的临时变量什么的还得重设。

开启多个 SSH 连接似乎可以解决这个问题。但是如果程序既需要交互，又想保证不因意外断线而停止程序，就是 nohup 也帮不了。

这时 [**tmux**](#chapter-3) 的出现，解决了会话保持与窗口复用的问题。关于 `tmux` 更详细的介绍，可以参见[这篇博客](http://louiszhai.github.io/2017/09/30/tmux/)。

## 服务与例行性任务

说到 **「服务」**，我们可能会想到服务器，上面运行着各式各样的网络服务。但是这里的「服务」不止于此，系统的正常运行也需要关键服务的支撑。在 Windows 中，我们可以从任务管理器一窥 Windows 中的「服务」；Linux 也有服务的概念，下面将作介绍。

### 守护进程

服务的特征，意味着服务进程必须独立于用户的登录，不能随用户的退出而被终止。根据前面的讲解，只有启动时脱离会话才能避免因为终端的关闭而消失。而这类一直默默工作于后台的进程被称为**守护进程** (daemon)。

### 管理服务

目前绝大多数 Linux 发行版的 init 方案都是 systemd，其管理系统服务的命令是 **`systemctl`**。

要管理服务，首先我们要清楚系统内有哪些服务。可以通过 **`systemctl status`** 命令一览系统服务运行情况。

```bash
  ~ systemctl status
● zyz-virtual-machine
    State: running
     Jobs: 0 queued
   Failed: 0 units
    Since: Thu 2024-04-18 11:00:49 CST; 3h 22min ago
   CGroup: /
           ├─user.slice 
           │ └─user-1000.slice 
           │   ├─user@1000.service 
           │   │ ├─gsd-xsettings.service 
           │   │ │ └─1869 /usr/libexec/gsd-xsettings
           │   │ ├─gvfs-goa-volume-monitor.service 
           │   │ │ └─1731 /usr/libexec/gvfs-goa-volume-monitor
           │   │ ├─gsd-power.service 
           │   │ │ └─1815 /usr/libexec/gsd-power
           │   │ ├─xdg-permission-store.service 
           │   │ │ └─1697 /usr/libexec/xdg-permission-store
           │   │ ├─xdg-document-portal.service 
           │   │ │ └─1982 /usr/libexec/xdg-document-portal
           │   │ ├─xdg-desktop-portal.service 
           │   │ │ └─2138 /usr/libexec/xdg-desktop-portal
           │   │ ├─gsd-sound.service 
           │   │ │ └─1856 /usr/libexec/gsd-sound
(以下省略)
```

上面命令所列出的是系统中正在运行的服务，若想了解全部服务内容，可以运行 **`systemctl list-units`** 来查看。该命令将显示所有 systemd 管理的单元，同时右面还会附上一句注释来表明该服务的任务。
&gt;Unit并不是Service，以上的叙述仅仅是为了方便。Systemd 中的 unit 可以是服务（Service）、设备（Device）、挂载点（Mount）、定时器（Timer）……有关 systemd unit 的详细介绍，可见 [systemd.unit(5)](https://www.freedesktop.org/software/systemd/man/systemd.unit.html)

至于服务的启动，终止，重载配置等命令可交付 tldr 介绍。

```bash
$ tldr systemctl
systemctl
Control the systemd system and service manager.

    - List failed units:  # 列出运行失败的服务
    systemctl --failed

    - Start/Stop/Restart/Reload a service:  # 开启/关闭/重启/重载服务。Reload 代表重载配置文件而不重启进程。
    systemctl start/stop/restart/reload {{unit}}

    - Show the status of a unit:  # 显示服务状态
    systemctl status {{unit}}

    - Enable/Disable a unit to be started on bootup:  # 设置（Enable）/取消（Disable）服务开机自启
    systemctl enable/disable {{unit}}

    - Mask/Unmask a unit, prevent it to be started on bootup:  # 阻止/取消阻止服务被 enable
    systemctl mask/unmask {{unit}}

    - Reload systemd, scanning for new or changed units:  # 重载 systemd，需要在创建或修改服务文件后执行
    systemctl daemon-reload
```

### 自定义服务

如果我想将一个基于 Web 的应用（如基于 Web 的 Python 交互应用）作为局域网内 Web 服务，以便于在其他设备上访问。那么如何将其注册为 systemd 服务呢？

其实只需要编写一个简单的 .service 文件即可。

&gt;编写 .service 文件并运行（以 Jupyter Notebook 为例）
&gt;
&gt;Jupyter Notebook 是基于浏览器的交互式编程平台，在数据科学领域非常常用。
&gt;
&gt;首先使用文本编辑器在 `/etc/systemd/system` 目录下创建一个名为 `jupyter.service` 的文件。并填入以下内容后保存：
&gt;
&gt;```bash
&gt;[Unit]
&gt;Description=Jupyter Notebook    # 该服务的简要描述
&gt;
&gt;[Service]
&gt;PIDFile=/run/jupyter.pid        # 用来存放 PID 的文件
&gt;ExecStart=/usr/local/bin/jupyter-notebook --allow-root
&gt;                                # 使用绝对路径标明的命令及命令行参数
&gt;WorkingDirectory=/root          # 服务启动时的工作目录
&gt;Restart=always                  # 重启模式，这里是无论因何退出都重启
&gt;RestartSec=10                   # 退出后多少秒重启
&gt;
&gt;[Install]
&gt;WantedBy=multi-user.target      # 依赖目标，这里指进入多用户模式后再启动该服务
&gt;```
&gt;
&gt;然后运行 `systemctl daemon-reload`，就可以使用 `systemctl` 命令来管理这个服务了，例如：
&gt;
&gt;```bash
&gt;systemctl start jupyter
&gt;systemctl stop jupyter
&gt;systemctl enable jupyter  # enable 表示标记服务的开机自动启动
&gt;systemctl disable jupyter # 取消自启
&gt;```

可以参考系统中其他 service 文件，以及 [systemd.service(5)](https://www.freedesktop.org/software/systemd/man/systemd.service.html) 手册页编写配置文件

### 例行性服务

所谓例行性任务，是指**基于时间的一次或多次周期性定时任务**。在 Linux 中，实现定时任务工作的程序主要有 `at` 和 `crontab`，它们无一例外都作为系统服务存在。

#### at命令

at 命令负责**单次**计划任务，当前许多发行版中，并没有预装该命令，需要使用命令 **`sudo apt install at`** 进行安装。

随后使用 tldr 查看该命令使用方法：

```bash
$ tldr at
at
Execute commands once at a later time.
Service atd (or atrun) should be running for the actual executions.

 - Execute commands from standard input in 5 minutes (press
   Ctrl &#43; D when done):
   at now &#43; 5 minutes

 - Execute a command from standard input at 10:00 AM today:
   echo &#34;{{./make_db_backup.sh}}&#34; | at 1000

 - Execute commands from a given file next Tuesday:
   at -f {{path/to/file}} 9:30 PM Tue
```

所以该命令的基本用法示例如下：

```bash
$ at now &#43; 1min
&gt; echo &#34;hello&#34;
&gt; &lt;EOT&gt; （按下 Ctrl &#43; D）
job 3 at Sat Apr 18 16:16:00 2020   # 任务编号与任务开始时间
```

一分钟后……为什么没有打印出字符串呢？其实是因为 `at` 默认将标准输出（stdout）和标准错误（stderr）的内容以**邮件**形式发送给用户。使用编辑器查看 **`/var/mail/$USER`** 就可以看到输出了（需要本地安装 mail 相关的服务）。

设置完任务之后，我们需要管理任务，我们可以用 **`at -l`** 列出任务，**`at -r &lt;编号&gt;`** 删除任务。它们分别是 `atq` 和 `atrm` 命令的别名。

#### crontab命令

`cron` 命令负责**周期性**的任务设置，与 `at` 略有不同的是，`cron` 的配置大多通过配置文件实现。

大多数系统应该已经预装了 crontab，首先查看 crontab 的用法：

```bash
$ crontab --help
crontab: invalid option -- &#39;-&#39;  # 出现这两行字很正常，许多命令（如 ssh）没有专用的 help
crontab: usage error: unrecognized option  # 输入「错误」的选项时，便会出现简单的使用说明。
usage:  crontab [-u user] file
        crontab [ -u user ] [ -i ] { -e | -l | -r }
                (default operation is replace, per 1003.2)
        -e      (edit user&#39;s crontab)
        -l      (list user&#39;s crontab)
        -r      (delete user&#39;s crontab)
        -i      (prompt before deleting user&#39;s crontab)
```

可以看到基本命令即对指定用户的例行任务进行显示、编辑、删除。如果任何参数都不添加运行 crontab，将从标准输入（stdin）读入设置内容，并覆盖之前的配置。所以如果想以后在现有配置基础上添加，应当在家目录中创建专用文件存储，或者使用 **`crontab -e`** 来对本用户任务进行编辑。

crontab 的配置格式很简单，对于配置文件的每一行，前半段为时间，后半段为 shell 执行命令。其中前半段的时间配置格式为：

```bash
# 分   时   日   月   星期  | 命令
# 下面是几个示例
*  *  *  *  *  echo &#34;hello&#34; &gt;&gt; ~/count # 每分钟输出 hello 到家目录下 count 文件
0,15,30,45 0-6 * JAN SUN  command # 每年一月份的每个星期日半夜 0 点到早晨 6 点每 15 分钟随便做点什么
5  3  *  *  * curl &#39;http://ip.42.pl/raw&#39; | mail -s &#34;ip today&#34; xxx@xxx.com # 每天凌晨 3 点 05 分将查询到的公网 ip 发送到自己的邮箱 （假设半夜 3 点重新拨号）
```

如果这里解释得尚不清楚，可以访问 [**此网站**](https://crontab.guru/)，该网站可以将配置文件中的时间字段翻译为日常所能理解的时间表示。  
![图7](/PostsImgs/Linux_Learning_Note_imgs/picture7.png)

另外，systemd 的 timer 单元也可以实现定时任务，配置文件的格式与上面的 service 文件一致，具体可以参考 [Systemd 定时器教程](https://www.ruanyifeng.com/blog/2018/03/systemd-timer.html)进行配置。

## 用户与用户组、文件权限、文件系统层次结构

### 用户与用户组

#### 为何需要用户

早期的操作系统没有用户的概念（如 MS-DOS），或者有「用户」的概念，但是几乎不区分用户的权限（如 Windows 9x）。而现在，这不管对于服务器，还是个人用户来说，都是无法接受的。

在服务器环境中，「用户」的概念是明确的：服务器的管理员可以为不同的使用者创建用户，分配不同的权限，保障系统的正常运行；也可以为网络服务创建用户（此时，用户就不再是一个有血有肉的人），通过权限限制，以减小服务被攻击时对系统安全的破坏。

而对于个人用户来说，他们的设备不会有第二个人在使用。此时，现代操作系统一般区分使用者的用户与「系统用户」，并且划分权限，以尽可能保证系统的完整性不会因为用户的误操作或恶意程序而遭到破坏。

#### Linux下的用户简介

你可以查看 `/etc/passwd` 文件，来得到系统中用户的配置信息。

以下是一个例子：

```bash
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
（中间内容省略）
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
ustc:x:1000:1000:ustc:/home/ustc:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
mysql:x:111:116:MySQL Server,,,:/nonexistent:/bin/false
```

在此文件中，每一行都代表一个用户，每行中用户信息由冒号 `:` 隔开，存储着包括用户名、用户编号 (UID, User ID)、家目录位置等信息。更多介绍，可以通过 `man 5 passwd` 查阅。

可以关注到，除了你自己以外，还有一个特殊的用户：`root`，和一大堆你素未相识的名字。下面将会进行介绍。
&gt;在 Unix 最初的时候，passwd 文件存储了用户密码的哈希。但是，这个文件是所有用户都可以读取的。为了不让用户的密码哈希被任意获取，进而导致用户的密码被暴力破解，现在一般把密码存在别的地方。对于 Linux 来说，密码哈希信息存储在 /etc/shadow 里面，只有根用户可以访问与修改。
___
&lt;span style=&#34;color: #FF0000;&#34;&gt;**根用户**&lt;/span&gt;

根用户在 Linux 操作系统中拥有**最高**的权限，可以对系统做任何操作（包括删除所有系统文件这一类**极端危险的操作**）。`root` 用户的用户数据存储在 `/root` 下。

在使用 `sudo` 的时候，输入自己的密码并验证正确之后，`sudo` 就会以 `root` 用户的身份，执行后面我们希望执行的命令。而使用 `apt` 安装的软件存储在了系统的目录下，所以必须要以 `root` 用户的身份安装，这就是我们平时需要 `sudo` 来安装软件的原因。

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:red;&#34;&gt;谨慎使用 sudo 来执行命令&lt;/summary&gt;

  我们知道，`root` 用户可以对系统做极其危险的操作。当使用 `root` 权限执行命令时（如使用 `sudo`），一定要**小心、谨慎，理解命令的含义之后再按下回车**。请不要复制网络上所谓的「Linux 优化命令」等，以 `root` 权限执行，否则**可能会带来灾难性的后果**。
  
  以下是一些会对系统带来毁灭性破坏的例子。 **再重复一遍，不要执行下面的命令！**
  
* `rm -rf /`（删除系统中的所有可以删除的文件，**包括被挂载的其他分区**。**即使不以 `root` 权限执行，也可以删掉自己的所有文件。**）
* `mkfs.ext4 /dev/sda`（将系统的第一块硬盘直接格式化为 ext4 文件系统。这会破坏其上所有的文件。）
* `dd if=/dev/urandom of=/dev/sda`（对系统的第一块硬盘直接写入伪随机数。这会破坏其上所有的文件，并且找回文件的可能性降低。）
* `:(){ :|: &amp; };:`（被称为「Fork 炸弹」，会消耗系统所有的资源。在未对进程资源作限制的情况下，只能通过重启系统解决，所有未保存的数据会丢失。）

&lt;/details&gt;

___

&lt;span style=&#34;color: #32CD32;&#34;&gt;**系统用户**&lt;/span&gt;

除了你、`root` 和其他在用你的电脑/服务器的人（如果有）以外，剩下还有很多用户，如 `nobody`, `www-data` 等。它们由系统或相关程序创建，用于执行服务等系统任务。不要随意删除这些用户，以免系统运行出现问题。

一般地，在 Linux 中，系统用户的 UID 有一个**指定范围**，而这段范围在各个发行版中可能不同。如 Debian 使用了 `100-999`, `60000-64999` 等区间分配给系统用户。

此外，由于系统用户的特殊性，它们一般默认禁止使用密码登录。
___

&lt;span style=&#34;color: #20B2AA;&#34;&gt;**普通用户**&lt;/span&gt;

普通用户可以登录系统，并**只能对自己的家目录下的文件进行操作**。所有普通用户的家目录都在 `/home/` 下，位于 `/home/username/` 的位置，其中 `username` 是用户名。

普通用户无法直接修改系统配置，也无法为系统环境安装或卸载软件。
___

#### 切换用户

1. **`sudo` 命令**

   该命令可以让你以「另一个用户的身份」来执行指定的命令。命令格式如下：  
   `sudo -u 用户名 命令`

   **不加参数**，则表示以 `root` 用户身份执行指定命令。
2. **`su` 命令**

   该命令用于直接切换用户，命令格式如下：  
   `su 用户名`

   不加用户名参数，则表示切换到 `root` 用户。

   在读完上面这句话之后，你可能会尝试切换到 `root`，但是却失败了。这是因为，如 Ubuntu 等 Linux 发行版默认禁止了 `root` 用户的密码登录，只允许通过 `sudo` 提高权限。但是，我们可以用 `sudo` 运行 `su`，来得到一个为 `root` 用户权限的 shell。

    ```bash
    $ sudo su
    Password:
    （没错，是我自己的密码）
    # id
    uid=0(root) gid=0(root) groups=0(root)
    # exit
    $
    ```

  &lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt; 「sudo su」 和 「sudo su -」 的区别&lt;/summary&gt;
  
  1. **sudo su**：
     * 该命令切换当前用户到 root 用户，并启动一个新的 shell。它不会改变环境变量，当前用户的环境变量将被保留。
     * 例如，如果当前用户是 **`user1`**，则执行 **`sudo su`** 后，用户会切换到 root 用户，但当前工作目录和环境变量等都保持为 **`user1`** 的设置。
  2. **sudo su -**：
     * 该命令也用于切换当前用户到 root 用户，并启动一个新的 shell。但是，它会模拟登录 root 用户，因此会加载 root 用户的环境变量和配置文件（例如 **`.bashrc`**、**`.profile`** 等），并且当前工作目录会切换到 root 用户的家目录。
     * 与 **`sudo su`** 不同，执行 **`sudo su -`** 后，用户切换到 root 用户后会拥有 root 用户的完整环境，包括 PATH、umask 等设置。

  因此，如果需要以 root 用户身份登录到系统，并且希望获取完整的 root 用户环境和配置文件，可以使用 **`sudo su -`** 命令。如果您只是需要执行一些 root 权限下的命令，并且不需要修改环境变量，可以使用 **`sudo su`** 命令。
  &lt;/details&gt;

#### 用户组简介

用户组是用户的集合。通过用户组机制，可以为一批用户设置权限。可以使用 **`groups`** 命令，查看自己所属的用户组。

```bash
➜  桌面 groups
zyz adm cdrom sudo dip plugdev lpadmin lxd sambashare
```

可以看到，用户 `zyz` 从属于多个用户组，包括一个与其名字相同的用户组。一般在用户创建的时候，都会创建与它名字相同的用户组。

对于普通用户来说，用户组机制会在配置部分软件时使用到。如在使用 **Docker** 时，可以把自己加入 `docker` 用户组，从而不需要使用 `root` 权限，也可以访问它的接口。

将某个用户加入某个组的命令为：`sudo usermod -a -G groupname username`

同样，用户组和用户一样，也有编号：GID (Group ID)。

#### 命令行的用户配置操作

1. **`passwd`命令**  
   使用命令 `passwd UserName` 修改 `UserName` 用户的密码。如果没有输入用户名，则修改自己的密码。
2. **`adduser`命令**  
   adduser 是 Debian 及其衍生发行版的专属命令，它可以用来向系统添加用户、添加组，以及将用户加入组。
   * `sudo adduser 用户名` ，即可添加新用户
   * `sudo adduser --group 组名` ，即可添加新用户组
   * `sudo adduser 用户名 组名` ，即可将指定用户添加到指定用户组

### 文件权限

在 Linux 中，每个文件和目录都有自己的权限。可以使用 **`ls -l`** 查看当前目录中文件的详细信息。

```bash
$ ls -l
total 8
-rwxrw-r-- 1 ustc ustc   40 Feb  3 22:37 a_file
drwxrwxr-x 2 ustc ustc 4096 Feb  3 22:38 a_folder
······
```

第一列的字符串从**左到右**意义分别是：

* 文件类型（一位）
* 文件所属用户的权限（三位）
* 文件所属用户组的权限（三位）
* 其他人的权限（三位）

对于每个权限，第一位 `r` 代表读取 (**R**ead)，第二位 `w` 代表写入 (**W**rite)，第三位 `x` 代表执行 (E**x**ecute)，`-` 代表没有对应的权限。

第三、四列为文件所属用户和用户组。

例如，上面的文件 **`a_file`** 为普通文件 (`-`)，所属用户权限为 `rwx`，所属用户组权限为 `rw-`，其他人的权限为 `r--`，文件所属用户和用户组均为 `ustc`。

**文件类型对应的字符有：**

1. **普通文件**： **`-`**，表示常规文件，存储文本、二进制数据或其他内容的常规文件类型。
2. **目录**： **`d`**，表示目录，用于组织文件和其他目录的容器。
3. **符号链接**： **`l`**，表示符号链接（软链接），是指向另一个文件或目录的引用。
4. **设备文件**：
    * 字符设备文件： **`c`**，表示字符设备文件，用于表示字符设备（如键盘、鼠标等）。
    * 块设备文件： **`b`**，表示块设备文件，用于表示块设备（如硬盘、闪存驱动器等）。
5. **管道**： **`p`**，表示管道（命名管道或 FIFO），用于在进程之间进行通信的特殊文件类型。
6. **套接字**： **`s`**，表示套接字，用于在网络上进行进程间通信的特殊文件类型。

&lt;details&gt;
  &lt;summary style=&#34;font-weight:bold; color:#40E0D0;&#34;&gt;执行权限的意义&lt;/summary&gt;
  读取和写入权限是很容易理解的。但是执行权限是什么意思？对于一个文件来说，拥有执行权限，它就可以被操作系统作为程序代码执行。如果某个程序文件没有执行权限，你仍然可以查看这个程序文件本身，修改它的内容，但是无法执行它。
  
  而对于目录来说，拥有执行权限，你就可以访问这个目录下的文件的内容。以下是一个例子：
  
  ```bash
  $ ls -l
  total 8
  -rwxrw-r-- 1 ustc ustc   40 Feb  3 22:37 a_file
  drw-rw-r-- 2 ustc ustc 4096 Feb  3 22:38 a_folder
  $ （与上面不同，我们去掉了 a_folder 的执行权限）

  $ cd a_folder
  -bash: cd: a_folder/: Permission denied
  $ （失败了，这说明，如果没有执行权限，我们无法进入这个目录）

  $ ls a_folder
  ls: cannot access &#39;a_folder/test&#39;: Permission denied
  test
  $ （列出了这个目录中的文件列表，但是因为没有执行权限，我们没有办法访问到里面的文件 test）

  $ cat a_folder/test
  cat: a_folder/test: Permission denied

  $ cp a_folder/test test
  cp: cannot stat &#39;a_folder/test&#39;: Permission denied

  $ mv a_folder/test a_folder/test2
  mv: failed to access &#39;a_folder/test2&#39;: Permission denied

  $ touch a_folder/test2
  touch: cannot touch &#39;a_folder/test2&#39;: Permission denied

  $ rm a_folder/test
  rm: cannot remove &#39;a_folder/test&#39;: Permission denied
  $ （可以看到，即使我们有写入权限，在此目录中进行添加、删除、重命名的操作仍然是不行的）
  ```

为了更好地理解目录权限的含义，可以把目录视为一个「文件」来看待，这个文件包含了目录中下一层的文件列表—— **「读取」** 对应读取文件列表的权限，**「写入」** 对应修改文件列表（添加、删除、重命名文件）的权限，**「执行」** 对应实际去访问列表中文件、以及使用 cd 切换当前目录到此目录的权限。当没有执行权无法访问文件时，就无法修改文件。
&lt;/details&gt;

___

使用 **`chmod`** 命令可修改文件/目录权限。命令格式如下：  
**`chmod [who] [operator] [permissions] FileName`**  

* **`who`**：指定权限变更对象。可以是以下之一：
  * **`u`**：文件拥有者（user）
  * **`g`**：文件所属组（group）
  * **`o`**：其他用户（others）
  * **`a`**：所有用户（all，也是**默认参数**）
* **`operator`**：指定权限变更操作。可以是以下之一：
  * **`&#43;`**：添加权限
  * **`-`**：移除权限
  * **`=`**：设置权限
* **`permissions`**：指定要添加、移除或设置的权限。可以是以下之一：
  * **`r`**：读权限
  * **`w`**：写权限
  * **`x`**：执行权限
* **`fileName`**：指定要更改权限的文件或目录。

当我们执行某个文件时，出现提示 **`Permission denied`** ，大多数情况下，说明这个文件没有执行权，可以使用 chmod 加上。

### 文件系统层次结构

Linux 下文件系统的结构和 Windows 的很不一样。在 Windows 中，分区以盘符的形式来标识（如「C 盘」、「D 盘」），各个分区的分界线是很明确的。在系统所在的分区（一般为 C 盘）中，存储着程序文件 (`Program Files`)，系统运行需要的文件 (`Windows`)，用户文件 (`Users`) 等。这种组织形式源于 DOS 和早期的 Windows，并一直传承下来。

而 UNIX 系列采用了一种不一样的思路组织文件：整个系统的文件都从 `/`（根目录）开始，像一棵树一样，类似于下图。  
![图8](/PostsImgs/Linux_Learning_Note_imgs/picture8.png)

其他的分区以挂载 (mount) 的形式「挂」在了这棵树上，如图中的 `/mnt/windows_disk/`。

那么在根目录下的这些目录各自有什么含义呢？这就由文件系统层次结构标准 (FHS, Filesystem Hierarchy Standard) 来定义了。这个标准定义了 Linux 发行版的标准目录结构。大部分的 Linux 发行版遵循此标准，或由此标准做了细小的调整。以下进行一个简要的介绍，也可以在[**官网**](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html)查看标准的具体内容。

当然，实际情况不一定会和以下介绍的内容完全一致。可以使用 `man hier` 和 `man file-hierarchy` 查看你的系统中关于文件系统层次结构的文档。  
文档。

| 目录           | 功能                                                                                                              |
| :------------- | :---------------------------------------------------------------------------------------------------------------- |
| /bin           | 存储必须的程序文件，对所有用户都可用                                                                              |
| /boot          | 存储在启动系统时需要的文件                                                                                        |
| /dev           | 存储设备文件（设备文件就是计算机设备抽象成文件的形式）                                                            |
| /etc           | 存储系统和程序的配置文件                                                                                          |
| /home          | 用户的家目录。存储用户自己的信息。                                                                                |
| /lib           | 存放系统运行必须的程序库文件                                                                                      |
| /media 和 /mnt | 这两个目录都用于挂载其他的文件系统。/media 用于可移除的文件系统（如光盘），而 /mnt 用于临时使用                   |
| /opt           | 存放额外的程序包。一般将一些大型的、商业的应用程序放置在这个目录                                                  |
| /root          | root 用户的家目录                                                                                                 |
| /run           | 系统运行时的数据。在每次启动时，里面的数据都会被删除                                                              |
| /sbin          | 存储用于系统管理，以及仅允许 root 用户使用的程序。如 fsck（文件系统修复程序）、reboot（重启系统）等               |
| /srv           | 存储网络服务的数据                                                                                                |
| /tmp           | 临时目录，所有用户都可使用                                                                                        |
| /usr           | 大多数软件都会安装在此处。其下有一些目录与 / 下的结构相似，如：/usr/bin 、/usr/lib 等                             |
| /usr/include   | 存储系统通用的 C 头文件。当然，里面会有你非常熟悉的头文件，如 stdio.h                                             |
| /usr/local     | 存储系统管理员自己安装的程序，这些文件不受系统的软件管理机制（如 apt）控制。/usr/local 里面的层次结构和 /usr 相似 |
| /usr/share     | 存储程序的数据文件（如 man 文档、GUI 程序使用的图片等）                                                           |
| /var           | 存储会发生变化的程序相关文件                                                                                      |

## 网络、文本处理工具

### I/O重定向与管道

#### 重定向

一般情况下命令从**标准输入（stdin）**读取输入，并输出到**标准输出（stdout）**，默认情况下两者都是你的终端。使用重定向可以让命令从文件读取输入/输出到文件。

重定向输出使用 `&gt;` 或 `&gt;&gt;` 符号，下面是以 `echo` 为例的重定向输出：

```bash
$ echo &#34;Hello Linux!&#34; &gt; output_file # 将输出写入到文件（覆盖原有内容）
$ cat output_file
Hello Linux!

$ echo &#34;rewrite it&#34; &gt; output_file
$ cat output_file # 可以看到原来的 Hello Linux! 被覆盖了。
rewrite it

$ echo &#34;append it&#34; &gt;&gt; output_file # 将输出追加到文件（不会覆盖原有内容）
$ cat output_file
rewrite it
append it
```

无论是 `&gt;` 还是 `&gt;&gt;`，当输出文件不存在时都会**创建**该文件。

重定向输入使用符号 `&lt;`：

```bash
command &lt; inputfile
command &lt; inputfile &gt; outputfile
```

&lt;br&gt;

&gt;除了 `stdin` 和 `stdout` 还有标准错误（`stderr`），他们的编号分别是 0、1、2（这些数字都是**文件描述符**）。stderr 可以用 `2&gt;` 重定向（注意数字 2 和 &gt; 之间没有空格）。
&gt;
&gt;使用 `2&gt;&amp;1` 可以将 stderr 合并到 stdout。
&gt;
&gt;**`&amp;`** 符号用于表示后面跟的是一个文件描述符，如果不使用 `&amp;` 符号则表示 1 是一个文件名

#### 管道

管道（pipe），使用操作符 `|`，作用为将符号左边的命令的 `stdout` 作为符号右边的命令的 `stdin`。管道不会处理 `stderr`。  
![图9](/PostsImgs/Linux_Learning_Note_imgs/picture9.png)

管道是类 UNIX 操作系统中非常强大的工具。通过管道，我们可以将实现各类小功能的程序拼接起来干大事。

```bash
$ ls / | grep bin  # 筛选 ls / 输出中所有包含 bin 字符串的行
bin
sbin
```

### 网络下载

在 Windows 下，很多人下载文件时会使用「迅雷」、「IDM」之类的软件来实现下载。那么在 Linux 环境下呢？在终端下，没有可视化软件提供点击下载。即使有桌面环境，有 Firefox 可以很方便地下载文件，硬件资源也会被很多不必要的服务浪费。通过以下内容讲述的 wget和 curl 工具，我们可以 Linux 上进行轻量的下载活动。

#### 使用 `wget`

wget 是强力方便的下载工具，可以通过 HTTP 和 FTP 协议从因特网中检索并获取文件。

**命令为：`wget [opt1]... [url1]...`**

常用的选项：
| 选项                       | 含义                                   |
| :------------------------- | :------------------------------------- |
| -i, --input-file=文件      | 下载本地或外部文件中的 URL             |
| -O, --output-document=文件 | 将输出写入文件                         |
| -b, --background           | 在后台运行 wget                        |
| -d, --debug                | 调试模式，打印出 wget 运行时的调试信息 |

例如批量下载 filelist.txt 中的链接：**`wget -i filelist.txt`**

#### 使用 `curl`

`curl` 是一个利用 URL 语法在命令行下工作的文件传输工具，其中 c 意为 client。虽然 cURL 和 Wget 基础功能有诸多重叠，如下载。但 cURL 由于可自定义各种请求参数，所以在**模拟 web 请求**方面更擅长；**wget** 由于支持 FTP 协议和递归遍历，所以在**下载文件**方面更擅长。

**`curl`** 的使用与 **`wget`** 相似，使用 **`curl -h`** 可详细了解其用法

常用的选项：
| 选项 | 含义                                                      |
| :--- | :-------------------------------------------------------- |
| -o   | 把远程下载的数据保存到文件中，需要指定文件名              |
| -O   | 把远程下载的数据保存到文件中，直接使用 URL 中默认的文件名 |
| -I   | 只展示响应头内容                                          |

### 文本处理

在进行文本处理时，我们有一些常见的需求：

* 获取文本的行数、字数
* 比较两段文本的不同之处
* 查看文本的开头几行和最后几行
* 在文本中查找字符串
* 在文本中替换字符串

下面介绍如何在 shell 中做到这些事情。

#### 文本统计

**`wc`** 是文本统计的常用工具，它可以输出文本的行数、单词数与字符（字节）数。

```bash
$ wc file
     427    2768   20131 file
```

**PS：** `wc` 无法准确统计中文文本

#### 文本比较

**`diff`** 工具用于比较两个文件的不同，并列出差异。

```bash
diff file1.txt file2.txt #比较两个文件
diff -r dir1 dir2 #比较两个目录
diff -w file1.txt file2.txt #忽略空白字符（空格、制表符）引起的差异
diff -u file1.txt file2.txt #显示差异处的三个上下文行，在中间显示差异行
diff -i file1.txt file2.txt #忽略大小写
```

#### 文本开头与结尾

**`head`** 和 **`tail`** 分别用来显示开头和结尾指定数量的文字。

以 `head` 为例，这里给出共同的用法：

* 不加参数的时候默认显示前 10 行
* `-n &lt;NUM&gt;` 指定行数，可简化为 `&lt;NUM&gt;`
* `-c &lt;NUM&gt;` 指定字节数

```bash
head file  # 显示 file 前 10 行
head -n 25 file  # 显示 file 前 25 行
head -25 file  # 显示 file 前 25 行
head -c 20 file  # 显示 file 前 20 个字符
tail -10 file  # 显示 file 最后 10 行
```

除此以外，`tail` 还有一个非常实用的参数 **`-f`**：当文件末尾内容增长时，持续输出末尾增加的内容。这个参数常用于「动态显示」 log 文件的更新（试一试`tail -f /var/log/syslog`）。

#### 文本查找

**`grep`** 命令可以查找文本中的字符串：

```bash
grep &#39;hello&#39; file  # 查找文件 file 中包含 hello 的行
ls | grep &#39;file&#39;  # 查找当前目录下文件名包含 file 的文件
grep -i &#39;Systemd&#39; file  # 查找文件 file 中包含 Systemd 的行（忽略大小写）
grep -R &#39;hello&#39; .  # 递归查找当前目录下内容包含 hello 的文件
```

**注：** `grep`事实上是非常强大的查找工具，将在介绍正则表达式语法之后进一步介绍 `grep`。

#### 文本替换

**`sed`** 命令可以替换文本中的字符串：**`sed [opt]... &#39;command&#39; file`**

常用参数：

* **`-e &lt;command&gt;`**：指定多个编辑命令。
* **`-n`**：禁止默认输出，只输出经过编辑的行。
* **`-i`**：直接编辑文件，而不是将结果输出到标准输出。
* **`-r`**：使用扩展正则表达式（需要 **`E`** 选项）。
* **`-E`**：使用扩展正则表达式。

常用命令：

* **`s/regexp/replacement/`**：替换匹配到的文本。
* **`d`**：删除匹配到的行。
* **`p`**：打印匹配到的行。
* **`i\`**：在指定行之前插入文本。
* **`a\`**：在指定行之后追加文本。

示例：

1. **替换文件中的文本：**  
    **`sed &#39;s/old_text/new_text/&#39; file.txt`**
2. **删除文件中匹配到的行：**  
   **`sed &#39;/pattern/d&#39; file.txt`**
3. **插入文本到指定行之前：**  
   **`sed &#39;/pattern/i\inserted_text&#39; file.txt`**
4. **使用多个编辑命令：**  
   **`sed -e &#39;s/old_text/new_text/&#39; -e &#39;/pattern/d&#39; file.txt`**
5. **使用扩展正则表达式进行匹配和替换：**  
   **`sed -E &#39;s/regexp/replacement/&#39; file.txt`**

**注：`sed`** 事实上也是非常强大的查找工具，将在后面进一步介绍

## Shell高级文本处理与正则表达式

### 其他shell文本处理工具

#### sort

sort 用于文本的行排序。默认排序方式是升序，按每行的字典序排序。

一些基本用法：

* `r` 降序（从大到小）排序
* `u` 去除重复行
* `o [file]` 指定输出文件
* `n` 用于数值排序，否则“15”会排在“2”前

```bash
$ echo -e &#34;snake\nfox\nfish\ncat\nfish\ndog&#34; &gt; animals
$ sort animals
cat
dog
fish
fish
fox
snake
$ sort -r animals
snake
fox
fish
fish
dog
cat
$ sort -u animals
cat
dog
fish
fox
snake
$ sort -u animals -o animals
$ cat animals
cat
dog
fish
fox
snake
$ echo -e &#34;1\n2\n15\n3\n4&#34; &gt; numbers  #-e参数是允许解释转义字符
$ sort numbers
1
15
2
3
4
$ sort -n numbers
1
2
3
4
15
```

#### uniq

uniq 也可以用来排除重复的行，但是仅对连续的重复行生效。

通常会和 sort 一起使用：

```bash
sort animals | uniq
```

只是去重排序明明可以用 `sort -u` ，uniq 工具是否多余了呢？实际上 uniq 还有其他用途。

`uniq -d` 可以用于仅输出重复行：

```bash
sort animals | uniq -d
```

`uniq -c` 可以用于统计各行重复次数：

```bash
sort animals | uniq -c
```

### 正则表达式

正则表达式（regular expression）描述了一种字符串匹配的模式，可以用来检查一个串是否含有某种子串、将匹配的子串做替换或者从某个串中取出符合某个条件的子串等。

#### 特殊字符

| 特殊字符 | 描述                                                                                                 |
| :------- | :--------------------------------------------------------------------------------------------------- |
| `[]`     | 方括号表达式，表示匹配的字符集合，例如 [0-9]、[abcde]                                                |
| `()`     | 标记子表达式起止位置                                                                                 |
| `*`      | 匹配前面的子表达式零或多次                                                                           |
| `&#43;`      | 匹配前面的子表达式一或多次                                                                           |
| `?`      | 匹配前面的子表达式零或一次                                                                           |
| `\`      | 转义字符，除了常用转义外，还有：`\b` 匹配单词边界；`\B` 匹配非单词边界等                                 |
| `.`      | 匹配除 \n（换行）外的任意单个字符                                                                    |
| `{}`     | 标记限定符表达式的起止。例如 {n} 表示匹配前一子表达式 n 次；{n,} 匹配至少 n 次；{n,m} 匹配 n 至 m 次 |
| `\|`     | 表明前后两项二选一                                                                                   |
| `$`      | 匹配字符串的结尾                                                                                     |
| `^`      | 匹配字符串的开头，在方括号表达式中表示不接受该方括号表达式中的字符集合                               |

以上特殊字符，若是想要匹配特殊字符本身，需要在之前加上转义字符 **`\`**。

#### 简单示例

匹配正整数：  
**`[1-9][0-9]*`**

匹配仅由 26 个英文字母组成的字符串：  
**`^[A-Za-z]&#43;$`**

匹配 Chapter 1-99 或 Section 1-99：  
**`^(Chapter|Section) [1-9][0-9]{0,1}$`**

匹配“ter”结尾的单词：  
**`ter\b`**

### 常用 Shell 文本处理工具（正则）

#### grep

grep 全称 Global Regular Expression Print，是一个强大的文本搜索工具，可以在一个或多个文件中搜索指定 pattern 并显示相关行。

grep 默认使用 BRE，要使用 ERE 可以使用 `grep -E` 或 egrep。

命令格式：`grep [option] pattern file`

一些用法：

* `n`：显示匹配到内容的行号
* `v`：显示不被匹配到的行
* `i`：忽略字符大小写

```bash
ls /bin | grep -n &#34;^man$&#34;  # 搜索内容仅含 man 的行，并且显示行号
ls /bin | grep -v &#34;[a-z]\|[0-9]&#34;  # 搜索不含小写字母和数字的行
ls /bin | grep -iv &#34;[A-Z]\|[0-9]&#34;  # 搜索不含字母和数字的行
```

#### sed

sed 全称 Stream EDitor，即流编辑器，可以方便地对文件的内容进行逐行处理。

sed 默认使用 BRE，要使用 ERE 可以 sed -E。

命令格式：

```bash
sed [OPTIONS] &#39;command&#39; file(s)
sed [OPTIONS] -f scriptfile file(s)
```

此处的 command 和 scriptfile 中的命令均指的是 sed 命令。

常见 sed 命令：

* s 替换
* d 删除
* c 选定行改成新文本
* a 当前行下插入文本
* i 当前行上插入文本

```bash
$ echo -e &#34;seD\nIS\ngOod&#34; &gt; sed_demo
$ cat sed_demo
seD
IS
gOod
$ sed &#34;2d&#34; sed_demo  # 不显示第二行
seD
gOod
$ sed &#34;s/[a-z]/~/g&#34; sed_demo  # 替换所有小写字母为 ~
~~D
IS
~O~~
$ sed &#34;3cpErfeCt&#34; sed_demo  # 选定第三行，改成 pErfeCt
seD
IS
pErfeCt
```

#### awk

awk 是一种用于处理文本的编程语言工具，名字来源于三个作者的首字母。相比 sed，awk 可以在逐行处理的基础上，针对列进行处理。默认的列分隔符号是空格，其他分隔符可以自行指定。

awk 使用 ERE。

命令格式：`awk [options] &#39;pattern {action}&#39; [file]`

awk 逐行处理文本，对符合的 patthern 执行 action。需要注意的是，awk 使用**单引号**时可以直接用 `$`，使用**双引号**则要用 `\$`。

一些示例：

```bash
$ cat awk_demo
Beth    4.00    0
Dan     3.75    0
kathy   4.00    10
Mark    5.00    20
Mary    5.50    22
Susie   4.25    18
$ # 选择第三列值大于 0 的行，对每一行输出第一列的值和第二第三列的乘积
$ awk &#39;$3 &gt;0 { print $1, $2 * $3 }&#39; awk_demo
kathy 40
Mark 100
Mary 121
Susie 76.5
```

示例中 `$1`，`$2`，`$3` 分别指代本行的第 1、2、3 列。特别地，$0 指代本行。

awk 语言是「图灵完全」的，这意味着理论上它可以做到和其他语言一样的事情。这里我们不仅可以对每行进行操作，还可以定义变量，将前面处理的状态保存下来，以下是一个求和的例子：

```bash
$ awk &#39;BEGIN { sum = 0 } { sum &#43;= $2 * $3 } END { print sum }&#39; awk_demo
337.5
```

关于 awk，有一本知名的书籍《The AWK Programming Language》（[**中文翻译**](https://github.com/wuzhouhui/awk)）

## Ubuntu下推荐的软件

### Zsh

使用 Linux 系统时，不可避免接触终端命令行操作，但是默认的终端黑底白字。有什么办法可以既美化终端，又提高工作效率呢？下面介绍一个美化终端的方法，更换 Shell。

在此之前，使用命令：`echo $SHELL` 来检查目前我们正在使用的是什么 Shell，Ubuntu默认使用 `Bash`。在这里推荐一个更加强大的 Shell 工具——Z shell（Zsh）。

`Zsh`使用步骤如下：

1. 使用命令 `sudo apt install zsh` 来安装 zsh
2. 使用命令 `chsh -s /bin/zsh` 将zsh设置为默认 shell
3. **重启**后打开终端就会发现 shell 变为了 zsh

Zsh 虽然好用，但直接用起来还是比较麻烦，不过幸运的是，已经有大神给我们配置好了一个很棒的框架：[oh-my-zsh](https://link.zhihu.com/?target=https%3A//github.com/robbyrussell/oh-my-zsh)，专门为 Zsh 打造，使用以下命令即可安装：

```bash
sh -c &#34;$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)&#34;
```

安装好oh-my-zsh就可以配置插件一些好用的插件。在 ~/.zshrc 文件中，中间靠下有`plugins`这句话，这里可以看到我们目前启用的插件，**在里面输入已安装且想启用的插件名**，插件之间用**空格间隔**。  
![图6](/PostsImgs/Linux_Learning_Note_imgs/picture6.png)

1. git  
   无需配置，默认已开启，使我们可以方便的使用git命令的缩写
2. zsh-syntax-highlighting  
   高亮语法，输入正确语法会显示绿色，错误的会显示红色，使得我们无需运行该命令即可知道此命令语法是否正确。使用以下命令安装:

   ```bash
   git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
   ```

3. zsh-autosuggestions  
   自动补全，只需输入部分命令即可根据之前输入过的命令提示，按右键即可补全，使用以下命令安装：

   ```bash
   git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions   
   ```

#### zsh的一些使用技巧

* 在zsh中**按一次`tab`键**就会列出所有的选项和帮助说明，**连按两次`tab`键**就可以开始在补全的列表直接选择参数（按tab键或方向键移动），回车选中参数。
* 在当前目录下输入 `..` 或 `...` ，或直接输入当前目录名都可以跳转，甚至不再需要输入 `cd` 命令了。
* 在你知道路径的情况下，比如 `/usr/local/bin` 你可以输入`cd /u/l/b` 然后按tab补全，实现快速输入
* 输入 d，即可列出你在这个会话里访问的目录列表，输入列表前的序号，即可直接跳转。
* 在 `.zshrc` 中添加 `setopt HIST_IGNORE_DUPS` 可以消除重复记录，也可以利用`sort -t &#34;;&#34; -k 2 -u ~/.zsh_history | sort -o ~/.zsh_history`手动清除

### unar &lt;a id=&#34;chapter-1&#34;&gt;&lt;/a&gt;

在 Linux 上的 tar 一般只支持 gzip、bzip、xz 和 lzip 几种压缩算法，如果需要解压 Windows 上更为常见的 7z、zip 和 rar 等，则需要寻求替代软件，推荐使用 [**unar**](https://theunarchiver.com/command-line) 软件。

Ubuntu 上直接使用 apt 安装即可：**`sudo apt install unar`**

安装完成后会得到两个命令：**`unar`** 和 **`lsar`**，分别用来解压存档文件以及浏览存档文件内容。  
**`lsar`** 命令详解：

```bash
lsar archive.zip # 浏览存档文件内容
lsar -l archive.zip # 查看详细信息
lsar -L archive.zip # 查看特别详细的信息
```

**`unar`** 命令详解：

```bash
# 直接将文件解压到当前目录下
unar test.zip

# -o 参数指定解压结果保存的位置
unar test.zip -o /home/dir/

# -e 参数指定编码
unar -e GBK test.zip

# -p 参数指定解压密码
unar -p 123456 test.zip

# 解决压缩包乱码问题
# 1.指定查看时的编码方式，如果能够正常显示，则直接使用该编码方式解压
lsar -e GB18030 test.zip
# 2. 解压
unar -e GB18030 test.zip
```

### htop &lt;a id=&#34;chapter-2&#34;&gt;&lt;/a&gt;

htop 是 Linux 系统中的一个互动的进程查看器，与 Linux 传统的 top 相比, htop 更加人性化。 它可让用户交互式操作，支持颜色主题, 可横向或纵向滚动浏览进程列表，并支持鼠标操作。

使用命令 **`sudo apt install htop`** 安装，直接在命令行输入 `htop` 即可启动软件，[htop使用教程](https://cloud.tencent.com/developer/article/1115041)。

**PS：** F10按键被窗口占用时，可在 窗口设置→配置文件首选项→取消启用菜单快捷键，即可将F10从终端窗口解绑。

### silversearcher-ag

`silversearcher-ag`（通常称为 ag）是一个代码搜索工具，它旨在比传统的 grep 更快、更高效。`ag` 擅长在大型代码库中快速进行搜索，它的速度优势主要得益于并行搜索以及自动忽略.git和其他版本控制系统文件夹和文件。

安装命令为：**`sudo apt install silversearcher-ag`**

**`ag`** 的基本语法非常简单：**`ag [options] &lt;search-pattern&gt; [path]`**  
path可选，默认为当前目录

#### 常用选项

* `-i`：忽略大小写。
* `-v`：只打印不匹配的行。
* `-w`：仅匹配整个单词。
* `-A NUM`：打印匹配行之后 NUM 行。
* `-B NUM`：打印匹配行之前 NUM 行。
* `-C NUM`：打印匹配行前后各 NUM 行。
* `-l`：只打印包含匹配项的文件名。
* `-L`：只打印不包含匹配项的文件名。
* `-g`：只打印匹配 PATTERN 的文件名。
* `-G`：只从满足正则表达式的文件中搜索。
* `-u`：搜索所有文件，忽略 .gitignore 等忽略文件。
* `-z`：搜索压缩文件的内容。

### tmux &lt;a id=&#34;chapter-3&#34;&gt;&lt;/a&gt;

tmux是一款优秀的终端复用软件，拥有丝滑分屏、保护现场、会话共享等主要功能。一个 tmux 会话可以包括多个窗口，一个窗口又可以包括多个面板，窗口下的面板，都处于同一界面下，这些面板适合运行相关性高的任务，以便同时观察到它们的运行情况。

tmux 由会话（session），窗口（window），面板（pane）组织起每个 shell 的输入框。会话用于区分不同的工作；窗口是会话中以显示屏为单位的不同的页；而面板则是一个窗口上被白线分割的不同区域。熟练掌握会话，窗口，面板之间的切换，可以极大提高使用效率。

安装命令为：**`sudo apt install tmux`**

#### 新建一个 tmux 会话

```bash
tmux  # 新建一个无名称的会话
tmux new -s demo # 新建一个名称为demo的会话
```

**PS：** 为了便于管理，建议指定会话名称

#### 关闭会话

会话的使命完成后，一定要关闭，命令如下：

```bash
tmux kill-session -t demo #关闭demo会话
tmux kill-server #关闭全部会话
```

#### 查看所有会话

```bash
tmux ls
```

#### 断开当前会话

会话中操作了一段时间，我希望断开会话同时下次还能接着用，怎么做？此时可以使用detach命令。

```bash
tmux detach
```

#### 进入之前的会话

断开会话后，想要接着上次留下的现场继续工作，就要使用到tmux的attach命令了，语法为tmux attach-session -t session-name，可简写为tmux a -t session-name 或 tmux a。通常我们使用如下两种方式之一即可：

```bash
tmux a #默认进入第一个会话
tmux a -t demo #进入名称为demo的会话
```

#### 常用快捷键

tmux的所有指令，都包含同一个前缀，默认为`Ctrl&#43;b`，输入完前缀过后，控制台激活，命令按键才能生效。即按快捷键之前需要先按完 `Ctrl&#43;b` 键激活控制台。
| 快捷键（需先按 Ctrl&#43;B） | 功能                                                         |
| :---------------------- | :----------------------------------------------------------- |
| %                       | 左右分屏                                                     |
| &#34;                       | 上下分屏                                                     |
| ↑ ↓ ← →                 | 焦点切换为上、下、左、右侧pane，正在交互的pane被绿色框选中。 |
| d (detach)              | 从tmux中脱离，回到命令行界面                                 |
| z (zoom)                | 将pane暂时全屏，再按一次恢复原状                             |
| c                       | 新建窗口                                                     |
| ,                       | 为窗口命名                                                   |
| s                       | 列出所有 session                                             |

![图5](/PostsImgs/Linux_Learning_Note_imgs/picture5.gif)

#### 定制 tmux

说实在的，tmux 默认的快捷键的确有些苦手，比如 `Ctrl &#43; B` 这个对手指相当不友好的长距快捷键就应当改进。而且你可能会想，横竖分屏居然需要 `%` 和 `&#34;`，为什么不使用更为直观的 `-` 和 `|` 呢？如果要对这些特性进行修改，可以在家目录下创建配置文件 .tmux.conf 达到所需目的。我的配置文件内容如下：

```bash
set -g prefix C-a                                 # 设置前缀按键 Ctrl &#43; A。
unbind C-b                                        # 取消 Ctrl &#43; B 快捷键。
bind C-a send-prefix                              # 第二次按下 Ctrl &#43; A 为向 shell 发送 Ctrl &#43; A。
                                                  # （Shell 中 Ctrl &#43; A 表示光标移动到最前端）。
set -g mouse on                                   # 启动鼠标操作模式，随后可以鼠标拖动边界进行面板大小调整。
unbind -n MouseDrag1Pane
unbind -Tcopy-mode MouseDrag1Pane

unbind &#39;&#34;&#39;                                        # 使用 - 代表横向分割。
bind - splitw -v -c &#39;#{pane_current_path}&#39;        # -v 代表新建的面板使用全部的宽度，效果即为横向分割（或者说，切割得到的新的面板在竖直方向 (vertical) 排列）。

unbind %                                          # 使用 \ 代表纵向分割（因为我不想按 Shift）。
bind \\ splitw -h -c &#39;#{pane_current_path}&#39;       # -h 则代表新建的面板使用全部的高度，效果即为纵向分割（切割得到的新的面板在水平方向 (horizontal) 排列）。
setw -g mode-keys vi                              # 设置 copy-mode 快捷键模式为 vi。
```

以 `.` 开头的文件为隐藏文件，需要使用 **`ls -a`** 命令查看。所以保存之后你可能不会直接在图形界面看到，不用担心。

保存后，使用 **`tmux source ~/.tmux.conf`** 重新载入配置，或者 **`tmux kill-server`** 后重启 `tmux`。

关于 `tmux` 更详细的介绍，可以参见[这篇博客](http://louiszhai.github.io/2017/09/30/tmux/)。

### gparted

`gparted` 是一款功能强大的开源磁盘分区工具，广泛用于 Linux 发行版。它支持多种文件系统和存储设备，提供直观的图形界面来帮助用户创建、移动、调整大小、格式化和删除磁盘分区。

安装命令为：**`sudo apt install gparted`**，输入命令 **`sudo gparted`**  即可进入图形化页面。

#### 使用 gparted 调整分区大小

1. 打开 gparted。在图形界面中，你会看到当前磁盘的分区布局。
2. 选择需要调整大小的分区，右键点击并选择 &#34;Resize/Move&#34;。
3. 拖动分区边界来调整大小，或者在对话框中输入新的分区大小。
4. 调整后，点击 &#34;Resize/Move&#34; 应用更改。
5. 最后，点击工具栏上的 &#34;Apply&#34; 按钮执行所有挂起的操作 。

#### 创建新分区

1. 在 gparted 中选择未分配的空间。
2. 右键点击并选择 &#34;New&#34;。
3. 在弹出的对话框中设置分区大小、文件系统类型、分区名称和标签。
4. 点击 &#34;Add&#34; 创建新分区。
5. 同样，不要忘记点击 &#34;Apply&#34; 来应用更改 。

#### 删除分区

1. 选择要删除的分区。
2. 右键点击并选择 &#34;Delete&#34;。
3. 确认删除操作。
4. 点击 &#34;Apply&#34; 执行删除。

#### 注意事项

* 在对磁盘分区进行操作之前，建议备份重要数据，以防数据丢失。
* 在使用 gparted 进行分区操作时，确保待操作的分区没有挂载，否则可能无法进行操作。
* 如果使用的是虚拟机，扩展虚拟硬盘的大小需要先在虚拟机设置中扩大硬盘大小。

## 参考资料

1. https://101.lug.ustc.edu.cn/


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/3d623e4/  


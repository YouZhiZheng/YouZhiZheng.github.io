# Git学习笔记

## Git概述
&gt; **Git** 是一款**分布式的代码版本控制工具**，Linux 之父 Linus 嫌弃当时主流的中心式的版本控制工具太难用还要花钱，就自己花两周时间开发出了 Git 的主体程序，一个月后就开始用Git来维护 Linux 的版本（给大佬跪了）。

常见的版本控制工具有：**分布式版本控制工具**和**集中式版本控制工具**  
1. **分布式版本控制工具：**
    - **工作原理：** 分布式版本控制工具不依赖于单一的中央服务器，每个开发者都拥有完整的代码库的副本。开发者在本地工作副本上进行修改，并在本地进行提交、分支、合并等操作。开发者可以选择与其他开发者直接交换修改的内容，也可以将修改推送到共享的远程仓库中。
    - **特点：**
      - 每个开发者都拥有完整的代码库的副本，可以在本地进行版本控制
      - 具有更好的分支和合并功能，能够轻松地处理复杂的开发工作流程
      - 典型的分布式版本控制工具有Git和Mercurial等

2. **集中式版本控制工具：**
    - **工作原理：** 集中式版本管理工具使用单一的中央服务器来存储所有版本的代码库。开发者在本地工作副本上进行修改，然后将修改后的内容提交到中央服务器。其他开发者通过从中央服务器检出代码库来获取最新的代码，并将他们的修改提交到同一个中央服务器上。
    - **特点：**
      - 中央服务器是唯一的源头，所有的代码修改都需要提交到中央服务器
      - 开发者在没有网络连接的情况下无法进行版本控制操作
      - 典型的集中式版本管理工具包括Subversion（SVN）和Perforce等

Git的**工作机制**如下图所示:  

![图1](/PostsImgs/GitLearningNote_imgs/picture1.png)  
- **工作区**：开发人员在本地存放项目文件（代码）的地方
- **暂存区**：是一个缓冲区域，用于临时存放即将提交到本地库的修改，开发者通过 **`git add`** 命令将工作区中的修改**添加到**暂存区，**只有添加到暂存区的文件该会被提交到本地库中去**
- **本地库**：存放项目完整历史记录和版本信息的地方，开发者通过 **`git commit`** 命令将暂存区的内容**提交到**本地库，**使用该命令后暂存区就会清空**
- **远程库**：用于存放项目的中央代码库（如GitHub），位于云端或其他服务器上。团队成员可以通过 **`git clone`** 操作从远程仓库克隆一个完整的 Git 仓库到本地。克隆操作会复制远程仓库的所有历史记录、分支、标签等信息，并在本地创建一个相同的仓库副本。通过 **`git push`** 用于将本地库推送到远程库， 通过 **`git pull`** 从远程库拉取最新的代码到本地库

## Git安装
### Windows下安装
先确定自己的电脑是64位操作系统还是32位的(按Win键，设置→系统→关于)，然后去[Git官网](https://git-scm.com/)下载对应的安装包，根据提示进行安装(详细安装过程请自行Google)。

**注意：** 安装路径中不要包含中文！
### Ubuntu下安装
由于本人是Ubuntu下使用Git的，所以这里的步骤会写的详细点。  
1. 输入命令 `sudo apt-get install git`进行安装，输入 `git version` 检查是否安装成功
2. 输入命令 `ssh -T git@github.com` 检查是否可以连接到GitHub，如果看到  
![图2](/PostsImgs/GitLearningNote_imgs/picture2.png)  
则说明能够连接。
3. 安装SSH keys（一定要在 **`~/.ssh`** 目录下操作）  
```bash
第一步：检查是否已经具有ssh keys，如果具有，则直接进行第三步
==========================================================
cd ~/.ssh
ls
==========================================================
如果存在文件 id_rsa 和 id_rsa.pub 则具有ssh keys

第二步：备份并移除已经存在的ssh keys
==========================================================
mkdir key_backup
cp id_rsa* key_backup
rm id_rsa*
==========================================================

第三步：执行以下命令
==========================================================
ssh-keygen -t rsa -C &#34;你的github邮箱&#34;
==========================================================
运行时会要求输入文件名，就输入id_rsa即可
接着会要求你输入两次密码，该密码用于push时的密码，不想设置密码可直接回车，建议设置

安装完毕后，再次输入ls查看是否存在文件id_rsa 和 id_rsa.pub
```
4. 输入命令 `cat id_rsa.pub` 查看 id_rsa.pub 中的内容，将其内容复制到GitHub账户的SSH keys中（头像→Settings→SSH and GPG keys→ New SSH key）
5. 再次输入命令`ssh -T git@github.com` （在~/.ssh目录下），如果显示`Hi 你的用户名! You’ve successfully authenticated, but GitHub does not provide shell access.` 则添加成功(如果在安装SSH keys时设置了密码，则该步骤会要求输出此密码)&lt;br&gt;
**注意：** 若报错`sign_and_send_pubkey: signing failed: agent refused operation `，则输入命令`eval &#34;$(ssh-agent -s)&#34; `和 `ssh-add `
___
安装好Git后，需要配置Git全局环境，输入以下命令：  
**`git config --global user.name &#34;你的GitHub用户名&#34;`**&lt;br&gt;
**`git config --global user.email &#34;你的GitHub邮箱地址&#34;`**&lt;br&gt;
输入命令 **`git config --global --list`** 查看是否设置成功&lt;br&gt;
这里设置的环境的作用是区分不同操作者身份。用户的签名信息可以在每一个版本的提交信息中看到，以此确认本次提交是谁做的。&lt;br&gt;
**注：** 此用户签名与远程库的账号没有任何关系

## Git常用命令
### 获取Git仓库
通常有两种获取 Git 项目仓库的方式（**两种方式都需要先进入到项目目录**）：
1. **将尚未进行版本控制的本地目录转换为 Git 仓库**  
输入命令 **`git init`** ，完成Git仓库初始化操作，会得到一个隐藏目录 **.git**
1. **从其它服务器克隆一个已存在的 Git 仓库**  
输入命令 **`git clone url`** (url为仓库地址，点击仓库页面的code按钮即可看到)，这样就能得到一个与远程仓库一样的本地仓库(仓库名也一样)。  
如果想要自定义本地库的名字，则可以修改命令为 **`git clone url 你要设定的本地仓库名`**  
**注：** 当你克隆一个远程仓库时，本地仓库通常**只会创建一个默认的主分支**（一般为 **`master`** 分支，也有可能是 **`main`** 分支），其他分支会以远程分支的形式存在于本地仓库中。

### 记录每次更新到仓库
Git仓库下的每一个文件只有两种状态：**已跟踪** 或 **未跟踪**。已跟踪指的是已经被纳入版本控制的文件，即是Git已经知道的文件。

可用 `git status` 命令查看哪些文件处于什么状态，如果在克隆仓库后立即使用此命令，会看到类似的输出:
```bash
On branch master
Your branch is up-to-date with &#39;origin/master&#39;.
nothing to commit, working directory clean
```
这说明当前所在分支为 master，所有已跟踪的文件在上次提交后都未被修改，且当前目录下没有出现未跟踪的文件。如果此时在当前项目下创建一个新的 **README** 文件，再次使用 `git status` 命令，会看到以下输出:
```bash
On branch master
Your branch is up-to-date with &#39;origin/master&#39;.
Untracked files:
  (use &#34;git add &lt;file&gt;...&#34; to include in what will be committed)
  README
nothing added to commit but untracked files present (use &#34;git add&#34; to
track)
```
在输出中可以看到存在未跟踪的文件 README，想要跟踪此文件则使用命令 `git add README` ，再次输入`git status` 命令，会发现 README 文件已被跟踪，并处于暂存状态:
```bash
On branch master
Your branch is up-to-date with &#39;origin/master&#39;.
Changes to be committed:
  (use &#34;git restore --staged &lt;file&gt;...&#34; to unstage)
  new file: README
```
也可使用命令 `git add .` 将**当前目录下的所有文件以及子目录下的所有文件**添加到暂存区，即命令`git add` 的作用是将文件存添加到暂存区（已跟踪的文件发生变化也需要使用此命令将其最新版本添加到暂存区），暂存区存放的文件是你执行`git add` 命令时的文件版本。

**注意:** 如果你不想某些文件(如日志文件，编译过程中的临时文件)被追踪且也不希望被Git提醒，则可以创建一个名为`.gitignore`的txt文件，列出要忽略的文件的模式。例如：
```bash
# 忽略所有的 .a 文件
*.a

# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a

# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

# 忽略任何目录下名为 build 的文件夹
build/

# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```
GitHub有一个十分详细的针对数十种项目及语言的 `.gitignore` 文件列表，点击[此处](https://github.com/github/gitignore)即可查看。
***

当要被追踪的文件和修改后的文件都已经添加到暂存区时，就可使用命令 **`git commit -m &#34;CommitInfo&#34;`** 或 **`git commit`** 将暂存区的文件提交到本地库。  
两者的区别是前者是在命令行书写简单的提交信息，后者会启动文本编辑器来书写复杂的提交信息，写好**提交说明信息**是非常重要的，可以帮助团队成员更好地理解和维护代码。我个人觉得这篇如何写好 [Commit Message](https://cbea.ms/git-commit/) 的博客非常值得一读([中文版](https://linux.cn/article-6401-1.html))。对于简单提交信息的书写则可以参照以下格式：  
格式：**`&lt;type&gt;(&lt;scope&gt;): &lt;subject&gt;`**  
type表示提交类型，scope表示涉及的文件(可用 **`*`** 来表示多个文件)，subject描述此次涉及的修改，常用type类型如下：
| Type | 说明 | 备注 |
| :----: | ------- | :--------: |
| feat | 提交新功能 | 常用 |
| fix  | 修复Bug | 常用 |
| docs | 修改文档 | |
| style | 修改格式，例如格式化代码，空格，拼写错误等 | |
| refactor | 重构代码，没有添加新功能也没有修复bug | |
| test | 添加或修改测试用例 | |
| perf | 代码性能调优 | |
| chore| 修改构建工具、构建流程、更新依赖库、文档生成逻辑| |

示例：**`fix(ngRepeat): fix trackBy function being invoked with incorrect scope`**


### 撤销修改
1. 当修改了工作区某个文件的内容且保持了，想要丢弃该修改。  
   命令：**`git checkout -- FileName`**
1. 当修改了工作区某个文件，且添加到暂存区，想要丢弃该修改。  
   依次执行命令：a. **`git reset HEAD FileName`**  b.**`git checkout -- FileName`**  
   **PS:** 只执行命令a 则是从暂存区丢弃该修改(即从暂存区删除此文件)
1. 当修改了工作区某个文件，不仅添加到了暂存区，还提交到了本地库，想要丢弃该修改，则需要进行版本回退。  
   命令：**`git reset --hard VersionNum`**  
   **PS:** 版本号可通过 **`git reflog`** 命令查看



### 比较差异
使用命令 **`git diff`** 来比较文件之间的差异，以下是此命令的常见用法：
1. **`git diff`**  
   这会显示工作区中未暂存的更改与暂存区中的内容之间的差异
1. **`git diff --cached`**  
   这会显示暂存区中的更改与最新提交（HEAD）之间的差异 
1. **`git diff HEAD`**  
   这会显示工作区中的未暂存更改与最新提交（HEAD）之间的差异  
   &lt;span style=&#34;color: red;&#34;&gt;**PS:**  &lt;/span&gt;若不想比较全部文件的差异，以上三种命令均可在末尾**指定文件名**
1. **`git diff branch1 branch2 -- FileName`**  
   比较不同分支中的同一文件的不同之处

### 文件重命名
想要在Git中对文件改名，则使用命令 **`git mv file_from file_to`**

### 查看提交历史
查看提交历史的常用命令有两个：**`git log`** 和 **`git reflog`**，后一个命令只显示简略的版本信息(常用于版本的穿梭)。  
**`git log`** 命令常用参数如下：  
![图4](/PostsImgs/GitLearningNote_imgs/picture4.png)

常用的命令为：**`git log --graph --oneline`**  
版本穿梭命令为：**`git reset --hard VersionNum`**


### 删除文件
1. 想要删除某个文件  
   命令：**`git rm FileName`**  
   &lt;span style=&#34;color: red;&#34;&gt;**PS:**  &lt;/span&gt;此命令等价于依次执行 **`rm FileName`** 和 **`git add FileName`** 命令
1. 想要删除被修改，但未添加到暂存区的文件，或已经在暂存区的文件。  
   命令：**`git rm -f FileName`** 
1. 想要某文件不再被Git跟踪(工作区中仍然存在该文件，只是不再被纳入版本管理)  
   命令：**`git rm --cached FileName`** 
1. 想要恢复删除文件  
   命令：**`git checkout -- FileName`**  
   此命令其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以&#34;一键还原&#34;

## Git分支
分支就是科幻电影里面的平行宇宙，当你正在电脑前努力学习Git的时候，另一个你正在另一个平行宇宙里努力学习SVN。

如果两个平行宇宙互不干扰，那对现在的你也没啥影响。不过，在某个时间点，两个平行宇宙合并了，结果，你既学会了Git又学会了SVN！  
![图5](/PostsImgs/GitLearningNote_imgs/picture5.png)  
分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。


### 创建、合并、删除分支
一开始的时候，主分支(`master` 或 `main`)是一条线，Git用`master` 或 `main`(创建仓库时确定主线指针名)指针指向最新的提交，再用`HEAD`指向master，就能确定当前分支，以及当前分支的提交点：  
![图6](/PostsImgs/GitLearningNote_imgs/picture6.png)

每次提交新的内容到本地库，`master`分支都会向前移动一步，这样，随着你不断提交，`master`分支的线也越来越长。

当我们创建新的分支，例如`dev`时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上：  
![图7](/PostsImgs/GitLearningNote_imgs/picture7.png)  
本质上每创建一个分支就是创建一个与分支同名的指针，让其指向当前分支的最新提交；HEAD则是指向当前所在分支的指针，例如当前在dev分支，则HEAD指向dev指针，从现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变：  
![图8](/PostsImgs/GitLearningNote_imgs/picture8.png)

假如我们在dev上的工作完成了，就可以把dev合并到master上。最简单的方法，就是直接把master指向dev的当前提交（这种合并方式被称为 **快进模式** 或 **快速合并**），就完成了合并：  
![图9](/PostsImgs/GitLearningNote_imgs/picture9.png)  
合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支：  
![图10](/PostsImgs/GitLearningNote_imgs/picture10.png)

&lt;span style=&#34;color: red;&#34;&gt;**合并的本质就是将目标分支的改动添加到当前分支**&lt;/span&gt;

#### 分支常用命令 &lt;a id=&#34;chapter-1&#34;&gt;&lt;/a&gt;
查看本地所有分支：**`git branch`** ，当前分支前会有一个 **`*`** 号

查看远程所有分支：**`git branch -r`**，使用 **`git branch -a`** 可查看本地和远程的所有分支

查看本地分支和远程分支的映射关系：**`git branch -vv`**

设置当前分支的上游分支(即设置该分支映射到远程库的哪个分支)：**`git branch -u REMOTE_BRANCH_NAME`**

创建分支：**`git branch branch_name`** 

切换分支：**`git switch branch_name`** 

创建并切换分支：**`git switch -c branch_name`** 或 **`git checkout -b branch_name`**

合并某分支到当前分支(采用快速合并方式，删除分支后会丢失分支信息)：**`git merge branch_name`**

合并某分支到当前分支(禁用快速合并方式，不会丢失分支信息)：**`git merge --no-ff -m &#34;Title&#34; branchName`**

删除已经合并的分支：**`git branch -d branch_name`** 

删除未合并的分支：**`git branch -D branch_name`**
___
通常，合并分支时，如果可能，Git会用 **`Fast forward`** 模式，但这种模式在删除分支后会丢掉分支信息。

如果不想在删除分支后丢失分支信息，可以强制禁用`Fast forward`模式，即 **`git merge --no-ff -m &#34;Title&#34; branchName`** ，Git就会在merge时生成一个**新的commit**，这样，从分支历史上就可以看出分支信息。

当使用`Fast forward`模式合并时，merge后将像这样：  
![图14](/PostsImgs/GitLearningNote_imgs/picture14.png)

禁用`Fast forward`模式合并时，merge后将像这样：  
![图15](/PostsImgs/GitLearningNote_imgs/picture15.png)


### 解决冲突
人生不如意之事十之八九，合并分支往往也不是一帆风顺的。

准备新的 **`feature1`** 分支，继续新分支开发：  
```bash
$ git switch -c feature1
Switched to anew branch &#39;feature1&#39;
```
修改 **`readme.txt`** 最后一行，改为：
```bash
Creating anew branch is quick AND simple.
```
在 **`feature1`** 分支上提交修改到本地库：
```bash
$ git add readme.txt

$ git commit -m &#34;AND simple&#34;
[feature1 14096d0]AND simple
 1 file changed, 1 insertion(&#43;), 1 deletion(-)
```
切换到 **`master`** 分支：
```bash
$ git switch master
Switched to branch &#39;master&#39;
Your branch is ahead of &#39;origin/master&#39; by 1commit.
  (use &#34;git push&#34;to publish yourlocal commits)
```
在 **`master`** 分支上把 **`readme.txt`** 文件的最后一行改为：
```bash
Creating anew branch is quick &amp; simple.
```
提交：
```bash
$ git add readme.txt
$ git commit -m &#34;&amp; simple&#34;
[master 5dc6824] &amp; simple
 1 file changed, 1 insertion(&#43;), 1 deletion(-)
```
现在，**`master`** 分支和 **`feature1`** 分支各自都分别有新的提交，变成了这样：  
![图11](/PostsImgs/GitLearningNote_imgs/picture11.png)

这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突。


在上面这种情况时，使用 **`git merge feature1`** 命令，Git就会告诉我们readme.txt文件存在冲突，必须手动解决冲突后再提交。使用命令 **`git diff branch1 branch2 -- FileName`** 来比较不同分支中的同一文件的不同之处。将当前分支中的readme.txt文件的内容修改为 `Creating a new branch is quick and simple.` 再提交。

现在，`master`分支和`feature1`分支变成了下图所示：  
![图12](/PostsImgs/GitLearningNote_imgs/picture12.png)

&lt;span style=&#34;color: red;&#34;&gt;**当你解决完冲突后，将修改后的文件添加到暂存区时Git就会知道冲突已解决，会自动执行合并操作，无需再次输入合并命令**&lt;/span&gt;  
使用命令 **`git log --graph --oneline`** 也可以看到分支的合并情况


### Bug分支
软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。
___
假设有如下情景：你当前正在`dev`分支进行工作，且进行的工作因未完成还未提交，这时出现了一个代号为 333 的bug任务需要修复。  
**修复此bug的流程如下：**  
1. 使用命令 **`git stash`** 把当前工作现场储藏起来(会保存当前工作目录中的所有更改，并将工作目录恢复到最后一次提交的状态)，等处理完bug后恢复现场继续工作。
2. 确定要在哪个分支上修复bug。假定需要在 **`master`** 分支上修复，就从 **`master`** 分支创建临时分支 **`bug-333`**。
3. 在 **`bug-333`** 分支上修复bug。
4. 切换回 **`master`** 分支进行合并操作，并删除 **`bug-333`** 分支。
5. 切换回 **`dev`** 分支，使用命令 **`git stash list`** 来查看存储列表，再使用 **`git stash pop StashName`** 来恢复对应的工作现场，并在列表中删除此工作现场。

由于dev分支是早期从master分支分出来的，所以，这个bug其实在当前dev分支上也存在。那怎么在dev分支上修复同样的bug？重复操作一次，提交不就行了？有木有更简单的方法？有！


同样的bug，要在dev上修复，我们只需要把 **`4c805e2 fix bug 333`** 这个提交所做的修改 “**复制**” 到dev分支。  
**注意：** 我们只想复制 **`4c805e2 fix bug 101`** 这个提交所做的修改，并不是把整个master分支merge过来。

&lt;span style=&#34;color: red;&#34;&gt;Git专门提供了一个`cherry-pick`命令，让我们能复制一个特定的提交到当前分支：&lt;/span&gt; **`git cherry-pick commit-hash`**  

在上面的例子中，完整命令为 **`git cherry-pick 4c805e2`** ，执行该命名前，要确定所在分支为dev

既然可以在master分支上修复bug后，在dev分支上可以“重放”这个修复过程，那么直接在dev分支上修复bug，然后在master分支上“重放”行不行？当然可以，不过仍然需要 **`git stash`** 命令保存现场，才能从dev分支切换到master分支。

### Rebase
Rebase被称为 [**变基**](https://blog.csdn.net/michaelshare/article/details/79108233) 操作，其作用为使日志看起来更加简洁明了，缺点是会打乱时间线。

merge操作是将两个分支的最新commit合并后进行提交，形成新的commit；而 **`git rebase`** 提取操作有点像 **`git cherry-pick`** 一样，执行rebase后依次将当前（执行rebase时所在分支）的提交cherry-pick到目标分支（待rebase的分支）上，然后将在原始分支（执行rebase时所在分支）上的已提交的commit删除。


### 分支管理策略
在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`main`(或`master`)分支应该是非常稳定的，也就是仅用来发布新版本。`develop`和`hotfix`均从`main`分支分出，分别用于集成测试和修复出现的bug。`feature`分支从`develop`分支分出，用于开发新功能，开发完成后合并到`develop`分支。有条件的可以细化不同的测试环境，例如，用`text`分支来进行功能测试，通过测试后由`text`分支合并到`release`分支进行用户验收测试，测试通过后再合并到`main`分支。

所以，团队合作的分支看起来就像这样：  
![图18](/PostsImgs/GitLearningNote_imgs/picture18.png)


## 标签管理
发布一个版本时，我们通常先在版本库中打一个 **标签**（tag），这样，就 **唯一** 确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

Git的标签虽然是版本库的快照，但其实它就是 **指向某个commit的指针**（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

Git已经有commit号，为什么还需引入tag？见以下场景：

“请把上周一的那个版本打包发布，commit号是6a5819e...”

“一串乱七八糟的数字不好找！”

如果换一个办法：

“请把上周一的那个版本打包发布，版本号是v1.2”

“好的，按照tag v1.2查找commit就行！”

所以，tag就是一个让人容易记住的有意义的名字，**它跟某个commit绑在一起**。
可以理解为 tag 就是域名，commit号 就是IP


### 创建标签
在Git中打标签非常简单，首先，切换到需要打标签的分支上：
```bash
$ git branch
* dev
  master
$ git checkout master
Switched to branch &#39;master&#39;
```
然后，使用命令 **`git tag tag_name`** 就可以创建一个名为tag_name的新标签，使用命令 **`git tag`** 即可查看所有标签，通过 **`git show tag_name`** 可查看标签的详细信息。
&gt; **注意：** 标签默认是打在当前分支的最新提交上，如果想要给以前的commit打标签，则需要先通过`git reflog`获得对应的commit_id，然后使用命令 **`git tag tag_name commit_id`**  
&gt;
&gt; 标签是按字母来排序的！  
&gt; 标签总是与commit_id相挂钩的！


### 删除标签
如果标签打错了，也可以用命令 **`git tag -d tag_name`** 删除

因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

如果要推送某个标签到远程，使用命令：
**`git push origin &lt;tagname&gt;`**
或者，一次性将全部标签推送到远程库，使用命令：
**`git push origin --tags`**

如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：
```bash
$ git tag -d v0.9
Deleted tag &#39;v0.9&#39; (was f52c633)
```
然后，从远程删除。删除命令也是push，但是格式如下：
```bash
$ git push origin :refs/tags/v0.9
To github.com:michaelliao/learngit.git
 - [deleted]         v0.9
```
最后，登陆GitHub查看看看是否真的从远程库中删除了此标签。


## 与远程库相关的操作
使用分布式版本控制系统时，是有一台电脑充当服务器的角色。这样，当我们有了修改时直接把修改提交到服务器的仓库里。其他人若是想获取这次修改，直接从服务器仓库中拉取即可。 而GitHub就扮演着这个服务器仓库的角色。你需要先注册一个GitHub账号，配置好你电脑的SSH Key，这样才能将你使用的电脑与你的GitHub账号绑定起来([如何配置SSH](https://liaoxuefeng.com/books/git/remote/))。

### 与远程库建立连接
与远程库建立连接有两种方式：
1. 使用 **`git clone`** 命令。  
   当你克隆某个远程库到本地后，Git会自动创建一个名为 `origin` 的远程库引用(如果已经存在此引用，则新值会覆盖旧值)，也可以指定Git创建的远程库引用名，命令： **`git clone &lt;仓库URL&gt; --origin &lt;自定义远程仓库名&gt;`**
1. 在本地仓库下运行命令：**`git remote add 远程库引用名 remote-url`**  
   如果不指定远程库引用名，Git会使用默认的`origin`。如果想要更改远程库的别名，则使用命令 **`git remote rename old-name new-name`**


### 查看远程库信息
如果想要查看现在和哪些仓库建立的连接，可以使用命令：**`git remote`** 或 **`git remote -v`**，后一个命令可以查看详细的远程库信息，比如远程库的地址(没有`push`权限则不能看到)。

### 与远程库断开连接
使用命令：**`git remote rm 远程库引用名`** ，即可断开与此远程库的连接

### 抓取或拉取远程库分支
当远程库有了新的提交(更新)，则需要将这些更新取回本地，这时就要用到命令：  
**`git fetch 远程库引用名`**  
将远程库的全部更新抓取到本地，如果只想要抓取某个分支的更新，则使用命令：  
**`git fetch 远程库引用名 分支名`**  
对于抓取的更新，在本机上需要用 ***远程库引用名/分支名*** 的形式读取。比如origin主机的master分支，就要用 **origin/master** 读取。可用[前面](#chapter-1)提到的命令来查看远程分支。在确定合并不会发生冲突后，就使用命令 **`git merge`** 在本地分支上合并远程分支。

___

也可使用命令：**`git pull`** 拉取远程库的某个分支并与本地的指定分支合并。即 **`git pull = git fetch &#43; git merge`**，其完整格式为：  
**`git pull 远程库引用名 远程分支名:本地分支名`**

如果远程分支是与当前分支合并，则可省略冒号及其后面的内容：  
**`git pull 远程库引用名 远程分支名`**

在某些场合，Git会自动在本地分支与远程分支之间，建立一种追踪关系（tracking，也叫上下游关系）。比如，在 **`git clone`** 的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的`master`分支自动 &#34;追踪&#34; `origin/master`分支。也可使用[前面](#chapter-1)的命令来手动建立追踪关系。如果当前分支与远程存在追踪关系，在拉取时就可省略远程分支名。

**`git pull origin`**  
上面命令表示，本地的当前分支自动与远程库对应的追踪分支进行合并。如果合并需要采用rebase模式，可以使用`--rebase`选项：  
**`git pull --rebase origin`**

如果远程主机删除了某个分支，默认情况下，`git pull` 不会在拉取远程分支的时候，删除对应的本地分支。这是为了防止，由于其他人操作了远程主机，导致`git pull`不知不觉删除了本地分支。

但是，你可以改变这个行为，加上参数 -p 就会在本地删除远程已经删除的分支。
```bash
$ git pull -p
# 等同于下面的命令
$ git fetch --prune origin
# 或下面这个命令 
$ git fetch -p
```


### 推送到远程库
`git push`命令用于将本地分支的更新，推送到远程主机。它的格式与`git pull`命令相仿。  
**`git push 远程库引用名 本地分支名:远程分支名`**

如果省略远程分支名，则表示将指定的本地分支推送到与之存在 &#34;追踪关系&#34; 的远程分支（通常两者同名），如果该远程分支不存在，则会被新建。  
```bash
$ git push origin master
```
上面命令表示，将本地的master分支推送到origin主机的master分支。如果后者不存在，则会被新建。

如果省略本地分支名和远程分支名，则表示将当前所在分支推送到其上游远程分支，如果该远程分支不存在，则会被新建。  
```bash
$ git push origin
```

如果只省略本地分支名，则表示删除指定的远程分支。
```bash
$ git push origin :master
# 等同于
$ git push origin --delete master
```

如果你想将本地的所有分支都推送到远程主机，不管是否存在对应的远程分支，这时需要使用`--all`选项。  
**`git push --all origin`**


___
推送时，如果远程库的版本比本地库的版本新，则推送时Git会报错，要求先在本地做 **`git pull`** 合并差异，然后再推送到远程主机。

## 参考资料
1. https://blog.51cto.com/u_15242250/2856081
2. https://www.bilibili.com/video/BV1vy4y1s7k6/?p=8&amp;spm_id_from=pageDriver&amp;vd_source=744dd2bfd43a3b6a0d6a04beeeb1f108
3. https://git-scm.com/book/en/v2
4. https://www.liaoxuefeng.com/wiki/896043488029600
5. https://www.cnblogs.com/linj7/p/14377278.html
6. https://www.ruanyifeng.com/blog/2014/06/git_remote.html

---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/a538cc4/  


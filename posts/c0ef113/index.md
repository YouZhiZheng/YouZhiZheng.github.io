# VsCode推荐插件

## Better C&#43;&#43; Syntax

&gt;该插件主要作用是提供 C&#43;&#43; 语法高亮

## Bookmarks

&gt; 该插件主要作用是允许用户在代码中添加书签，以便快速跳转到这些特定位置。这个插件对于需要在大型项目中导航和跟踪重要代码段的开发者来说非常有用。

该插件常用快捷键为：  
1. 添加书签：`Ctrl&#43;Alt&#43;K`
1. 删除书签：将光标放在带有书签的行上，然后使用 `Ctrl&#43;Alt&#43;J`
1. 切换书签：`Ctrl&#43;Alt&#43;Q`可以在书签之间切换，如果当前行有书签，则会删除它。
1. 跳转到下一书签：`Ctrl&#43;Alt&#43;L`
1. 跳转到上一书签：`Shift&#43;Ctrl&#43;Alt&#43;L`

## C/C&#43;&#43;
&gt;该插件主要作用是提供了一系列的工具和功能，例如代码分析功能、代码格式化功能、代码提示等。

## CMake
&gt;该插件主要作用是CMake语法高亮、CMake代码自动补全。

## CMake Tools
&gt;该插件主要作用是提供各种CMake编译相关的小工具，包括在底部状态栏显示一些快捷工具。

## cmake-format
&gt;该插件的主要作用是格式化 CMakeLists.txt 文件，使其保持一致和可读性。安装此插件前，需要先安装cmake-format工具。

## Code Runner
&gt;该插件的主要作用是运行选定的代码片段。  

该插件常用快捷键为：  
1. 运行选定代码 `Ctrl&#43;Alt&#43;N` 或 直接点击右上角的三角形 或 点击右键菜单中的 `Run Code`
2. 停止正在运行的代码 `Ctrl&#43;Alt&#43;M` 或 点击右键菜单中的 ` Stop Run Code`

插件详细说明[地址](https://marketplace.visualstudio.com/items?itemName=formulahendry.code-runner)

## Diff
&gt;该插件的主要作用是比较两个文件的不同之处，直接在资源管理窗口中选择两个要比较的文件，将会直接显示比较结果。

## Error Lens
&gt;该插件的主要作用是将代码中存在的问题突出显示(包括错误、警告和语法问题)，它不仅在代码行尾显示问题，而且会在整行进行高亮，使得诊断信息更加明显。

## GitLens
&gt;该插件的主要作用是可以更方便地在VsCode中进行Git相关操作，该插件的使用可看[此视频](https://www.bilibili.com/video/BV1AS4y1V7PG/?spm_id_from=333.337.search-card.all.click&amp;vd_source=744dd2bfd43a3b6a0d6a04beeeb1f108)了解。

**PS:** 建议能够熟练使用Git命令后再使用此插件，Git的学习可看[此教程](https://zyzhi.top/posts/a538cc4/)。

## GitHub Copilot
&gt;该插件的主要作用是辅助编码。该插件是GitHub的AI编码工具，能根据注释、函数名、函数参数编写代码。该插件的详细介绍可看[此文章](https://blog.csdn.net/Hyl_Aa/article/details/131520129)。

**PS:** 该插件需要进行GitHub学生认证才能免费使用，可参考[此教程](https://mdnice.com/writing/9e9fe24b16234c28a460aa45b99655ae)。

## Include Autocomplete
&gt;该插件的主要作用是提供编写 C&#43;&#43; `#include` 语句时自动补全功能

## Markdown All in One 和 Markdown Preview Enhanced
&gt;这两个插件都是用于帮助编写Markdown文件

## SVG Viewer
&gt;该插件的主要作用是在VsCode中编辑和预览 SVG 文件

## Todo Tree
&gt;该插件的主要作用是用于展示工作区中所有的待办事项，点击对应事项后可以跳转到对应位置

主要有以下几种注释格式：  
![图1](/PostsImgs/VscodeRecomPlugin_imgs/picture1.png)  

该插件的配置代码如下，直接粘贴进 **`setting.json`** 文件中即可。使用 `Ctrl &#43; ,` 命令，再点击右上角的打开设置，即可打开 **`setting.json`** 文件。  

```json
  &#34;todo-tree.tree.showScanModeButton&#34;: false,
  &#34;todo-tree.filtering.excludeGlobs&#34;: [&#34;**/node_modules&#34;, &#34;*.xml&#34;, &#34;*.XML&#34;],
  &#34;todo-tree.filtering.ignoreGitSubmodules&#34;: true,
  &#34;todohighlight.keywords&#34;: [
  ],
  &#34;todo-tree.tree.showCountsInTree&#34;: true,
  &#34;todohighlight.keywordsPattern&#34;: &#34;TODO:|FIXME:|NOTE:|\\(([^)]&#43;)\\)&#34;,
  &#34;todohighlight.defaultStyle&#34;: {

  },
  &#34;todohighlight.isEnable&#34;: false,
  &#34;todo-tree.highlights.customHighlight&#34;: {
    &#34;BUG&#34;: {
      &#34;icon&#34;: &#34;bug&#34;,
      &#34;foreground&#34;: &#34;#F56C6C&#34;,
      &#34;type&#34;: &#34;line&#34;
    },
    &#34;FIXME&#34;: {
      &#34;icon&#34;: &#34;flame&#34;,
      &#34;foreground&#34;: &#34;#FF9800&#34;,
      &#34;type&#34;:&#34;line&#34;
    },
    &#34;TODO&#34;:{
      &#34;foreground&#34;: &#34;#FFEB38&#34;,
      &#34;type&#34;:&#34;line&#34;
    },
    &#34;NOTE&#34;:{
      &#34;icon&#34;: &#34;note&#34;,
      &#34;foreground&#34;: &#34;#67C23A&#34;,
      &#34;type&#34;:&#34;line&#34;
    },
    &#34;INFO&#34;:{
      &#34;icon&#34;: &#34;info&#34;,
      &#34;foreground&#34;: &#34;#909399&#34;,
      &#34;type&#34;:&#34;line&#34;
    },
    &#34;TAG&#34;:{
      &#34;icon&#34;: &#34;tag&#34;,
      &#34;foreground&#34;: &#34;#409EFF&#34;,
      &#34;type&#34;:&#34;line&#34;
    },
    &#34;HACK&#34;:{
      &#34;icon&#34;: &#34;versions&#34;,
      &#34;foreground&#34;: &#34;#E040FB&#34;,
      &#34;type&#34;:&#34;line&#34;
    },
    &#34;XXX&#34;:{
      &#34;icon&#34;: &#34;unverified&#34;,
      &#34;foreground&#34;: &#34;#E91E63&#34;,
      &#34;type&#34;:&#34;line&#34;
    }
  },
  &#34;todo-tree.general.tags&#34;: [
    &#34;BUG&#34;,
    &#34;HACK&#34;,
    &#34;FIXME&#34;,
    &#34;TODO&#34;,
    &#34;INFO&#34;,
    &#34;NOTE&#34;,
    &#34;TAG&#34;,
    &#34;XXX&#34;
  ],
  &#34;todo-tree.general.statusBar&#34;: &#34;total&#34;,
```

## Doxygen Documentation Generator
&gt;该插件的主要作用是生成Doxygen能够读取的注释风格，配合[Doxygen](https://blog.17lai.site/posts/1acb0edb/)软件使用，可自动生成代码的说明文档。

默认使用方法为：在要生成注释的位置输入 `/**`后直接回车即可。

**PS:** 一般使用默认格式即可，如果要修改生成注释风格模板可直接在设置(`Ctrl &#43; ,`即可打开)➡扩展➡Doxygen Documentation Generator 里自行调式

## Remote-SSH
&gt;该插件的主要作用是允许通过 SSH 连接到远程服务器，并在 VSCode 环境中无缝地进行远程开发。

相关学习资料：  
1. [Remote-SSH的使用](https://www.cnblogs.com/qiuhlee/p/17729647.html)
2. [Ubuntu下安装ssh服务](https://blog.csdn.net/sdnuwjw/article/details/109786245)
3. [Remote-SSH的使用(视频)](https://www.bilibili.com/video/BV1s44y1G7E2/?spm_id_from=333.337.search-card.all.click&amp;vd_source=744dd2bfd43a3b6a0d6a04beeeb1f108)

---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/c0ef113/  


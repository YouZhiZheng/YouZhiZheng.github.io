# Tmux 使用教程


`tmux` 是一个**终端复用器**，全称可以理解为 **terminal multiplexer**。

简单说，它可以让你在一个**终端里管理多个终端会话**，并且这些会话可以在你断开 SSH、关闭 Cursor/VSCode 终端后继续运行。

使用该工具最主要的原因有：

- **防止远程任务因为断连而中断**
- **可以随时离开和恢复会话**
- **一个窗口里管理多个终端**（就不用开多个SSH终端啦！一个终端内即可创建多个窗口、面板）

## 基础概念
tmux中有三个核心概念：`Session, Window, Pane`, 可以查下面的示意图来理解这3个概念.
![图1](/PostsImgs/dev-tools/terminal/tmux_1.png)
可以发现在tmux中执行任何操作都需要先按下&lt;kbd&gt;Ctrl&lt;/kbd&gt; &#43; &lt;kbd&gt;b&lt;/kbd&gt;, 因为这是tmux默认的前缀功能键后续简称为`preFix`(该功能键可以进行修改), 其作用就是激活tmux的命令模式, 让用户可以通过接下来的命令操作tmux的`Session, Window, Pane`.

## 常用命令
### Session管理

- **新建会话:** `tmux new -s &lt;会话名称&gt;`
- **断开会话:** &lt;kbd&gt;preFix&lt;/kbd&gt; &#43; &lt;kbd&gt;d&lt;/kbd&gt;
- **列出会话:** `tmux ls`
- **进入/重连会话:** `tmux a -t &lt;会话名称&gt;`
- **杀死会话:** `tmux kill-session -t &lt;会话名称&gt;`

### 其他命令
想要**丝滑**的使用`tmux`，就必须进行个性化配置，直接复制下面的内容给AI，与AI一起生成适配你的配置文件吧！

```text
依次跟我讨论下面问题的答案, 帮我生成一份最适配我的tmux配置文档(所有推荐的按键设置都应该符合直觉, 操作丝滑):
- 是否更改`preFix`
- 创建/切换/关闭 Pane
- 调整Pane大小
- 创建/关闭/切换/重命名 Window
- 如何切换到指定Window
- Window编号从0开始还是从1开始
- 关闭Window后, Window是否自动重新排序
- 关闭Window/Pane确认机制
- 如何开启/退出复制模式
- 如何开始复制，复制中如何移动， 如何复制粘贴
- 状态栏（是否需要显示CPU, 内存使用情况等）
- 是否启用鼠标
- 是否支持配置重载
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/73a7b9a/  


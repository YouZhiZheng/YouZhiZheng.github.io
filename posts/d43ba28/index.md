# MarkdownLearningNote

## 代码
### 行内代码
格式：` ˋ代码内容ˋ `
### 块内代码
格式：
```markdown
ˋˋˋmarkdown
Sample text here ...
ˋˋˋ
```

## 标题
格式：
```markdown
# 一级标题
## 二级标题
...
###### 六级标题
```

## 水平线
格式：
```markdown
水平线有三种实现方式:
___:三个连续的下划线
---:三个连续的破折号
***:三个连续的星号
```

## 换行
### 分行
格式：两个空格 &#43; 回车 或 使用`&lt;br&gt;`
### 分段
格式：两个回车

## 强调
### 粗体
格式：`**加粗的内容**` 也可以选择需要加粗的内容后使用快捷键`Ctrl &#43; B`
### 斜体
格式：`*倾斜的内容* 或 _倾斜的内容_` 也可以选择需要加粗的内容后使用快捷键`Ctrl &#43; I`
### 删除线
格式：`~~倾斜的内容~~`
### 组合使用
```markdown
_**加粗和斜体**_
~~**删除线和加粗**~~
~~_删除线和斜体_~~
~~_**加粗，斜体和删除线**_~~
```

## 引用
格式：`&gt; &#43; 空格 &#43; 引用的内容`


例如：
```markdown
&gt; **Fusion Drive** combines a hard drive with a flash storage (solid-state drive) and presents it as a single logical volume with the space of both drives combined.
```

## 列表
### 无序列表
格式：
```markdown
无序列表有三种实现方式
* 一项内容
- 一项内容
&#43; 一项内容
```
例如：
```markdown
* Lorem ipsum dolor sit amet
* Consectetur adipiscing elit
* Integer molestie lorem at massa
* Facilisis in pretium nisl aliquet
* Nulla volutpat aliquam velit
  * Phasellus iaculis neque
  * Purus sodales ultricies
  * Vestibulum laoreet porttitor sem
  * Ac tristique libero volutpat at
* Faucibus porta lacus fringilla vel
* Aenean sit amet erat nunc
* Eget porttitor lorem
```
### 有序列表
格式：`1. &#43; 空格`
备注：如果对每一项都使用`1. `，Markdown会自动为每一项编号
### 任务列表
格式：`- [] 这是内容` 或 `- [x] 这是内容`
效果：
- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media

## 表格 &lt;a id=&#34;chapter-1&#34;&gt;&lt;/a&gt;
格式:
```markdown
| Option | Description |
| ------ | ----------- |
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |
```
效果：
| Option | Description |
| ------ | ----------- |
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |

备注：竖线无需垂直对齐，`------:`表示这一列的内容右对齐，`:------`表示这一列的内容左对齐，`:------:`表示这一列的内容居中对齐

## 链接
### 常用链接方式
格式：
```markdown
&lt;https://assemble.io&gt;
&lt;contact@revolunet.com&gt;
[Assemble](https://assemble.io) 推荐是方式进行链接
```
效果：

&lt;https://assemble.io&gt;  

&lt;contact@revolunet.com&gt;  

[Assemble](https://assemble.io)

### 定位标记
定位标记使你可以跳至同一页面上的指定锚点。例如，每个章节。格式：
```markdown
[跳转到Chapter 1](#chapter-1)
[跳转到Chapter 2](#chapter-2)
[跳转到Chapter 3](#chapter-3)
```
将跳转到这些地方：
```markdown
## Chapter 1 &lt;a id=&#34;chapter-1&#34;&gt;&lt;/a&gt;
Content for chapter one.

## Chapter 2 &lt;a id=&#34;chapter-2&#34;&gt;&lt;/a&gt;
Content for chapter one.

## Chapter 3 &lt;a id=&#34;chapter-3&#34;&gt;&lt;/a&gt;
Content for chapter one.
````
例如：点击[跳转到Chapter 1](#chapter-1)将跳转到表格章节，只需要使用上面的语法后，在`# 表格`后面加上代码`&lt;a id=&#34;chapter-1&#34;&gt;&lt;/a&gt;`即可

## 图片
格式：`![鼠标移动到图片上显示的描述](图片路径)`

## 脚注
脚注使你可以添加注释和参考，而不会使文档正文混乱。 当你创建脚注时，会在添加脚注引用的位置出现带有链接的上标编号。 读者可以单击链接以跳至页面底部的脚注内容。  

格式：
```markdown
这是一个数字脚注 [^1]
这是一个带标签的脚注 [^label]

下面的是点击上面的脚注后跳转显示的内容
[^1]: 这是一个数字脚注
[^label]: 这是一个带标签的脚注
```
效果：
巴拉巴拉巴拉巴拉巴拉 [^1]
[^1]: 这是参考内容或注释

备注：
要创建脚注引用，请在方括号中添加插入符号和标识符 `[^1]`。 标识符可以是数字或单词，但不能包含空格或制表符。 标识符仅将脚注引用与脚注本身相关联。在脚注输出中，脚注按顺序编号。

---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/d43ba28/  


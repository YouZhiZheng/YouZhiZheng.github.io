# Zotero &#43; Obsidian 文献阅读与笔记工作流


以前一直都是在 `ReadPaper` 上阅读文献，然后在 `Obsidian` 总结文献内容，翻译软件使用的是 `沙拉查词插件 &#43; Quicker`。自从 `Google` 浏览器升级后，已经无法使用沙拉查词插件了 且 `ReadPaper`自带的翻译有次数限制（也不好用），故打算切换文献阅读软件为 `Zotero`（该软件有翻译插件，通过配置 `API` 可调用`Gemini`、`GPT` 来实现准确的翻译）。此外，`Obsidian` 中也有插件可以直接导出 `Zotero` 内的指定文献中的批注，从而能够在 `Obsidian` 中快速梳理文献内容并形成笔记（再也不用手动截图文献中的批注啦！）。

## Zotero配置

配置步骤如下所示：

1. 在 [Zotero官网](https://www.zotero.org/) 下载软件并进行安装
2. 点击 **编辑→设置→高级** 修改数据库存储位置（这一步可选，默认是将数据放在 `C` 盘的）
3. 打开 https://zotero-chinese.com/plugins/ 下载插件： **Translate for Zotero**、**Better BibTeX for Zotero**
4. 打开 [Google AI Studio](https://aistudio.google.com/api-keys) ，申请 `Gemini` 密钥 (GPI或其他AI的申请地址自行Google)
5. 点击 **编辑→设置→翻译** 下划到服务，选择你要使用的翻译服务并填入对应密钥
6. 在 **翻译** 这一栏上划到通用，取消勾选 **自动翻译选择** 内容，这样设置后，只有按下 `Ctrl&#43;T` 时才会翻译选中内容（这一步可选）

## Obsidian配置

配置步骤如下所示：

1. 在 `Obsidian` 中下载第三方插件 **Zotero Integration**
2. 在插件配置中，对于 `PDF Utility` 一栏点击后面的 `Download`；`Database` 一栏选择 `Zotero`；`Note Import Location` 一栏选择你要存放导出内容的目录
3. 点击 `Citation Formats` 一栏的 `Add Citation Format`, 配置为:
      - **Name:** Format #1
      - **Output Format:** Formatted Citation
      - **Citation Style:** IEEE
4. 新建一个名为 **import模板** 的文件夹 (这个名称自己随意起), 在下面创建一个名为 `zotero integration template` 的笔记，然后写入以下内容:

    ```markdown
    **作者:** {{ authors }}
    **发表会议/期刊（时间）:** {{ publicationTitle }} {% if date %}({{ date | format(&#34;YYYY&#34;) }}){% endif %}
    **Zotero Link:** [Open PDF]({{ desktopURI }})

    ## 📝 文章解读

    {% for annotation in annotations %}
    {%- if annotation.annotatedText -%}
    &gt; [!quote] Highlight (Page {{ annotation.page }})
    &gt; {{ annotation.annotatedText | replace(&#34;\n&#34;, &#34;\n&gt; &#34;) }}
    &gt; [Link to PDF]({{ annotation.desktopURI }})

    {% endif %}

    {%- if annotation.imageRelativePath -%}
    &gt; [!image] Image Capture
    &gt; ![[{{ annotation.imageRelativePath }}]]
    &gt; [Link to PDF]({{ annotation.desktopURI }})

    {% endif %}

    {%- if annotation.comment -%}
    &gt; [!NOTE] **💡 My Insight:**
    &gt; {{ annotation.comment | replace(&#34;\n&#34;, &#34;\n&gt; &#34;) }}

    {% endif %}
    ---
    {% endfor %}

    ## 🧠 总结
    ```

5. 点击`Import Formats` 一栏的 `Add Import Format`, 配置为 (我存放笔记的目录为 `PaperNotes/`):
    - **Name:** Import #1
    - **Output Path:** PaperNotes/{{title}}/{{title}}.md
    - **Image Output Path:** PaperNotes/{{title}}/image/
    - **Image Base Name:** image
    - **Template File:** import模板/zotero integration template.md
    - **Bibliography Style:** IEEE

## 食用步骤

我的使用流程为:

1. 在 `Zotero` 中阅读文献并编写辅助理解的**注释**
2. 在 `Obsidian` 中按 `Ctrl &#43; P` 选择 `Zotero Integration: Import #1`，然后选择对应的文献，将该文献中的注释导入到`Obsidian`中
3. 整理注释，梳理该文献的主体内容，形成该文献的阅读笔记（比如记录下该文献的主要创新点，可优化点等）


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/ef03586/  


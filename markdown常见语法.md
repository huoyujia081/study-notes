[Markdown官方教程](https://markdown.com.cn/)

# 段落中换行

在一行的末尾添加两个或多个空格，然后按回车键,即可创建一个换行(`<br>`)。

```markdown
This is the first line.<br>
And this is the second line
```

This is the first line.<br>And this is the second line

# Markdown 强调语法

## 粗体

要加粗文本，请在单词或短语的前后各添加两个星号（asterisks）或下划线（underscores）。

```markdown
I just love **bold text**.
I just love __bold text__.
```

I just love **bold text**.
I just love __bold text__.

typora中可以选中要强调粗体的内容，然后ctrl+B，即可自动加粗

## 斜体

要用斜体显示文本，请在单词或短语前后添加一个星号（asterisk）或下划线（underscore）。

```markdown
Italicized text is the *cat's meow*.
Italicized text is the _cat's meow_.
```

Italicized text is the *cat's meow*.
Italicized text is the _cat's meow_.

## 粗体（Bold）和斜体（Italic）

要同时用粗体和斜体突出显示文本，请在单词或短语的前后各添加三个星号或下划线。

```markdown
This text is ***really important***.
This text is ___really important___.
```

This text is ***really important***.
This text is ___really important___.

# Markdown 引用语法

要创建块引用，请在段落前添加一个 `>` 符号。

嵌套块引用：块引用可以嵌套。在要嵌套的段落前添加一个 `>>` 符号。

> 一级引用
>
> > 二级引用

# Markdown 代码语法

要将单词或短语表示为代码，请将其包裹在反引号 (\`) 中。

```markdown
At the command prompt, type `nano`.
```

At the command prompt, type `nano`.

## Markdown 围栏代码块

~~~markdown
```json
`json代码块`
```
```java
`java代码块`
```
~~~

```json
`json代码块`
```

```java
`java代码块`
```

typora中可以直接输入（```语言）来直接创建围栏代码块，用于语法高亮显示

#  Markdown 链接语法

## 超链接

链接文本放在中括号内，链接地址放在后面的括号中，链接title可选。

超链接Markdown语法代码：`[超链接显示名](超链接地址 "超链接title")`

```text
这是一个链接 [Markdown语法](https://markdown.com.cn "最好的markdown教程")。
```

这是一个链接 [Markdown语法](https://markdown.com.cn "最好的markdown教程")。ctrl加鼠标左键可以跳转到对应的超链接地址

## 网址和Email地址

使用尖括号可以很方便地把URL或者email地址变成可点击的链接。

```markdown
<https://markdown.com.cn>
<fake@example.com>
```

<https://markdown.com.cn>
<fake@example.com>

## 引用类型链接

如果反复用这种格式引用同一个网站：`[超链接显示名](超链接地址 "超链接title")`，可以使用引用类型链接使用变量。

```markdown
[百度][1]
第一组方括号包围应显示为链接的文本。第二组括号显示了一个标签，该标签用于指向您存储在文档其他位置的链接。
[1]: http://www.baidu.com	"百度"
放在括号中的标签，其后紧跟一个冒号和至少一个空格（例如[label]:）。
链接的URL，可以选择将其括在尖括号中。
链接的可选标题，可以将其括在双引号，单引号或括号中。
```

[百度][1]

[1]: http://www.baidu.com	"百度"

之后只需输入`[百度][1]`即可表示链接

##  Markdown 标题编号

可以通过`[Heading IDs](#标题名字)`创建该标题的链接：[Heading IDs](#Markdown 标题编号)。

# Markdown 脚注

要创建脚注参考，请在方括号（`[^1]`）内添加插入符号和标识符。标识符可以是数字或单词，但不能包含空格或制表符。标识符仅将脚注参考与脚注本身相关联-在输出中，脚注按顺序编号。

在括号内使用另一个插入符号和数字添加脚注，并用冒号和文本（`[^1]: My footnote.`）。您不必在文档末尾添加脚注。您可以将它们放在除列表，块引号和表之类的其他元素之外的任何位置。

```markdown
Here's a simple footnote,[^1] and here's a longer one.[^bignote]

[^1]: This is the first footnote.

[^bignote]: Here's one with multiple paragraphs and code.

    Indent paragraphs to include them in the footnote.

    `{ my code }`

    Add as many paragraphs as you like.
```

Here's a simple footnote,[^1] and here's a longer one.[^bignote]

[^1]: This is the first footnote.

[^bignote]: Here's one with multiple paragraphs and code.

    Indent paragraphs to include them in the footnote.
    
    `{ my code }`
    
    Add as many paragraphs as you like.

# Markdown 任务列表语法

要创建任务列表，请在任务列表项之前添加破折号`-`和方括号`[ ]`，并在`[ ]`前面加上空格。要选择一个复选框，请在方括号`[x]`之间添加 x 。

```markdown
- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media
```

- [ ] Write the press release
- [ ] Update the website
- [ ] Contact the media

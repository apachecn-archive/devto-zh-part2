# 面向开发人员的降价备忘单

> 原文：<https://dev.to/rattanakchea/markdown-cheatsheet-for-developers-2bjj>

我喜欢用`Markdown`做我能做的任何事情，从写个人博客、科技文章甚至我的简历。只有几种语法是我最常用的。这篇文章是为那些不熟悉`Markdown`或者需要更新语法的人准备的。

## 标题和列表

`#`符号用于创建标题。`#`代表`H1`。`##`是为`H2`准备的。对于`H6`，您最多可以使用 6 个`#`

可以用`-`或者`*`做一个无序列表。要制作有序列表，请使用数字。下面是一个使用标题和列表的例子。

```
#Why markdown? (H1)
##Reasons I like markdown (H2)
- Minimal, clean and less distraction for writing
- Good support for code blocks
- Can be exported to many formats including html, pdf, doc, latex, etc.
##My favorite markdown editors (H2)
1\. Typora (Mac, Window)
2\. Caret (Mac, Window)
3\. Bear (Mac app store) 
```

# 为什么降价？

## 我喜欢减价的原因

*   简洁，干净，减少书写时的干扰
*   对代码块的良好支持
*   可以导出为多种格式，包括 html、pdf、doc、latex 等。

## 我最喜欢的降价编辑

1.  Typora (Mac，Window)
2.  插入符号(Mac，Window)
3.  熊(苹果应用商店)

### 代码块

您可以在一个句子中用一个反斜线标出代码或命令。反勾号内的文本将不会被格式化。若要将代码或文本格式化到它自己的独特块中，请使用三个反斜线。

```

```
def foobaz():
    print("you can use ` for code snippet")
```

```

```
def foobaz():
    print("you can use ` for code snippet") 
```

### 全部名单

```
- [ ] Write an article weekly 
- [ ] Repair windshield wipers of car 🚘
- [x] Publish **this** to dev.to 
```

*   [ ]每周写一篇文章
*   [ ]修理汽车挡风玻璃刮水器，添加洗涤液🚘
*   [x]将此发布到开发人员

### 造型

```
**STRONG**
*emphasis*
~~strikethrough~~
Math $e=mc^2$ 
Use *** or - - - for horizontal rule 
```

**强**

*强调*

~~删除线~~。

数学$e=mc^2$

* * *

**使用表情符号**

输入`:`将显示一个建议表情列表，即`:EMOJICODE:`。要查看表情符号的完整列表，请查看 https://www.webpagefx.com/tools/emoji-cheat-sheet/的[和 T4】](https://www.webpagefx.com/tools/emoji-cheat-sheet/)

```
`:unicorn:` :unicorn:
`:+1:`:+1: 
```

`:unicorn:`🦄(dev.to 不支持表情符号？)

`:+1:`👍

[![emoji](img/526d211381b0ad4d548975a0c6024a14.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gCtNTISe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x4ndj3k5qj6wxh56yrpy.png)

### 链接和图像

有两种方法来制作文本和图像的链接，内联和引用。

**内联链接** `[link title](uri)`。内联链接可以是绝对或相对 URIs(本地资源内的链接)。

**引用链接** `[link][refereceID]`与**内联链接**的不同之处在于它使用了一个`[]`来代替`()`。此外，您需要稍后添加一个`id`引用，以便 markdown 可以将它们链接在一起。

`[referenceID] : www.google.com "Google"`

**图片**类似于内联和引用链接，但是在前面多了一个`!`。

Syntanx `![Cat Image](catURI)`或`![Dog Image][huskyDogID]`

```
[Reference link to dev.to website](dev.to)

![image](https://i2.wp.com/fullstop360.com/blog/wp-content/uploads/2017/05/design-quotes-for-inspiration-2.jpg?) 
```

[开发网站的参考链接](//dev.to)

[![image](img/f98db036c4bc992c13bbde513d7d67f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0EPnwFF---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/fullstop360.com/blog/wp-content/uploads/2017/05/design-quotes-for-inspiration-2.jpg)T3】

```
Footnote References[^1]: 
```

脚注引用 <sup id="fnref1">[1](#fn1)</sup> :

### 更

您可以使用[toc]来创建目录。

内嵌 html 和`<pre>`标签`<span style="color: red">Red Text</span>`

红色文本(在完全支持的降价编辑器上应为红色)

**报价**

```
>And so, my fellow Americans: ask not what your country can do for you — ask what you can do for your country
```

> 因此，我的美国同胞们:不要问你的国家能为你做些什么，而要问你能为你的国家做些什么

* * *

1.  [https://help . github . com/articles/basic-writing-and-formatting-syntax/](https://help.github.com/articles/basic-writing-and-formatting-syntax/)“github”[↩](#fnref1)
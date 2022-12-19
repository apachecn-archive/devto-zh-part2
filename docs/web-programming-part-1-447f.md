# HTML/CSS/JS(第 1 部分)

> 原文：<https://dev.to/rinsama77/web-programming-part-1-447f>

在我们开始之前，我想介绍一些对网页设计有用的资源:

*   [设计布局](https://designschool.canva.com/)
*   [配色方案](http://colorschemedesigner.com/)
*   或者是你懒于编码:[建站工具](http://www.creativebloq.com/web-design/best-website-builder-8133804)

好了，现在让我们开始今天的话题。基本的 HTML！

## HTML 简介

HTML，或者超文本标记语言，是一种用于创建*网页*的标记语言(很明显)。HTML 格式写的文档是纯文本格式，也可以叫做 *HTML 文档*。它们包含带有**标记标签**的信息内容，以指示浏览器**如何在网页中显示**这些内容。

您可以使用任何文本编辑器来编写 HTML 文档，但是该文件应该有*。html* 或者*。htm* 扩展名。

所见即所得 <sup id="fnref1">[1](#fn1)</sup> HTML 编辑器示例:

> -Dreamweaver
> -front page
> -括号

其他流行的(但不是所见即所得)文本编辑器的例子:

> -记事本
> -记事本++
> -崇高
> - Visual Studio 代码

## 基本 HTML 标签规则

*   HTML 标签用尖括号括起来；<>。标签通常成对出现(开始标签和结束标签),它们是相同的标签名，前面有一个斜杠。例如`<tag-name>Content inside</tag-name>`
*   然而，并不是所有的标签都需要结束。例如，

    paragraph:意味着前一个段落标记的结束(尽管您也可以添加一个结束标记)。
    换行符:标记一个位置，不是一个区域。

*   HTML 标签**不**区分大小写。< b >与< B >含义相同
*   未知标签被*忽略*。

#### 嵌套

*   开始和结束标签定义受标签影响的区域。这些区域可以*嵌套*，但是*不能*重叠。 [![tags](../Images/2244b1e6522d9442deb6d3f7bbb7d872.png)](https://clearlydecoded.com/anatomy-of-html-tag) 点击图片为来源网站< 3 [ ![tagging](../Images/71f813c0ec4a35fb7fe5c797a6533788.png)](https://thepracticaldev.s3.amazonaws.com/i/ccp2ywlxb49q85qsv3of.png)

#### 标题标签

*   标题由

    # 到

    ###### 标签定义。而

    # 定义最大航向，

    ###### 定义最小航向。

*   浏览器会自动在标题前后添加一个额外的空行。

#### 段落标签

*   正如我上面提到的，

    定义为新段落。

*   浏览器会自动在段落前后添加一个额外的空行。

#### 换行标签

*   浏览器只有在到达当前行的末尾时才会转到下一行。所以打字..

```
Hello, 
I want
a new line. 
```

在您的文本编辑器中将只在浏览器中显示一行文本..

*   标签会在你放置它的地方强制换行。

#### 文本样式标签

```
<b>Boldfaced text</b><br>
<i>Italicized text</i><br>
<u>Underlined text</u><br>
<del>Strikethrough text</del> 
```

结果:
**粗体文本**
*斜体文本*
<u>下划线文本</u>

~~Strikethrough text~~

#### 标签属性

*   属性为您的页面提供附加信息；例如，标签中的“bgcolor”属性用于改变页面的背景颜色。

```
<body bgcolor="red"> 
```

*   属性通常以 **name="value"** 的形式出现。

#### 超文本链接

*   或者 HTML 中的超链接将一个文档连接到另一个文档。
*   超链接允许您在自己或他人的服务器上浏览其他 HTML 文档。
*   要创建一个超链接，你用开始和结束**锚**标签< a > < /a >将链接的文本括起来。
*   在开始锚标签

```
<a href="url">Text to be displayed</a>
or
<a href="url">Image to be displayed</a> 
```

*   我们可以链接到多种类型的路径:
    *   绝对路径:当链接到另一个域的网页时使用。
    *   相对路径:当链接到你的域中的网页时使用。

```
Absolute path:
<a href="http://www.google.com/intl/en/about.html">About
Google</a>

Relative path:
<a href="filename.html">Text/Image to be displayed</a> 
```

#### 文本对齐

```
<body>
    <p align="left">Left alignment text</p>
    <p align="center">Center alignment text</p>
    <p align="right">Right alignment text</p>
</body> 
```

#### 图形元素

*   默认情况下，所有浏览器都识别三种位图或绘画图像类型:
    *   GIF(图形交换格式)
    *   联合图像专家组
    *   png _ 便携式网络图形)
*   图像标签![](URL)用于向网页添加图像。属性 **src** 指定图像文件的 URL 或路径。
*   我们可以添加**宽度**和**高度**属性来调整图像的大小。
*   和 **align** 属性来设置图像对齐。

```
<img src="http://www.mahidol.ac.th/images/logo.gif" width="45" height="45" align="right"> 
```

#### HTML 中的列表

*   有两种类型的列表；有序列表和无序列表。例如

```
Ordered list:
<ol>
    <li>Number one</li>
    <li>Number two</li>
    <li>Number three</li>
<ol>

unordered list:
<ul>
    <li>Hey</li>
    <li>There</li>
</ul>

<ul type="circle">
    <li>What's</li>
    <li>up!</li>
</ul>

<ul type="square">
    <li>Yo</li>
    <li>baby~</li>
</ul> 
```

#### 文本属性

*   如果你想在 HTML 中操作字体及其大小，你可以使用<font>标签，带有**大小**和**字体**属性。</font>
*   size 属性的值从 1 到 7 不等(最小->最大)。
*   默认字体大小为 3。
*   **face** 属性包含您希望使用的字体的实际名称。
*   您可以使用**颜色**属性改变颜色。您可以键入`<font color="red">`或者您可以键入 RGB 十六进制值代码`<font color="#FF0000">`中的颜色

#### 人物实体

*   有些字符在 HTML 中有特殊的含义，例如< sign that defines the start of an HTML tag. If we want the browser to *实际上是*显示它们，我们必须使用字符实体。
*   角色实体有 3 个部分
    1.  与号&
    2.  一个实体名称或一个`# + entity number`
    3.  分号；

例如&lt;或`&#60;`

*   实体是区分大小写的。

#### HTML 中的注释

*   它用于在 HTML 源代码中插入注释，浏览器会忽略它

```
<!-- This is a comment --> 
```

#### 横规

*   标签插入了一条水平线。例如

```
This is some text.
<hr>
This is another text. 
```

这是一些文本。

* * *

This is another text.

#### 表格标签

*   用定义表格...

    标签。
*   表格分为行，每一行又分为列(或单元格)。
*   行由 决定，列由 决定。
*   数据只能放在 标签之间。
*   标题单元格是用 标签定义的，它们的作用类似于 标签，只是文本以粗体显示。
*   还有一个 **align** 属性来设置内容的对齐方式。
*   我们使用 **colspan** 和 **rowspan** 来分别跨越多个列和行。
*   您还可以使用 **bgcolor** 属性为表格应用不同的颜色。

```
<table border="1">
    <tr>
        <th colspan="3" bgcolor="blue">Student Grade Report</th>
    </tr>
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Grade</th>
    </tr>
    <tr>
        <td>478812</td>
        <td>John Smith</td>
        <td align="center">A</td>
    </tr>
    <tr>
        <td>479821</td>
        <td>Mary Jane</td>
        <td align="right">B</td>
    </tr>
</table> 
```

[![](../Images/38f28db875048268752ffe943ba267aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8nO4Hbzm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/je00498uh1dopbu8kct3.png)

其他基本的 HTML 标签，[点击这里](https://www.w3schools.com/tags/ref_byfunc.asp)！

* * *

1.  WYSIWYG 代表所见即所得。 [↩](#fnref1)
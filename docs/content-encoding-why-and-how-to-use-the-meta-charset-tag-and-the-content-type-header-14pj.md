# 内容编码:为什么以及如何使用元字符集标签和内容类型头

> 原文：<https://dev.to/borisschapira/content-encoding-why-and-how-to-use-the-meta-charset-tag-and-the-content-type-header-14pj>

提高网页显示速度通常意味着尽可能简化浏览器的工作。当浏览器收到 HTTP 响应时，它实际上接收的是以字节编码的文本，其中每个字节或字节序列代表一个给定的字符。如果浏览器没有关于所用编码的明确信息，它将浪费时间去猜测，并且在某些情况下可能会失败。

尽管网络旨在普及，但使用网络的不同人群有其自身的特殊性。这些特性之一是语言，尤其是书写时。所有文本内容都由目录中的字符组成，用于某种用途。例如，平假名是一种语音系统，旨在明确地记录日语。

<figure>

[![Hiragana](img/9ff6edabec393758c3222a6e47d06a87.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--47eoumxD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/75z7ckyyklwc4618qpl4.png)

<figcaption>Table showing the writing direction of hiraganas
by Karine WIDMER ([CC-BY-SA-3.0](http://creativecommons.org/licenses/by-sa/3.0/) · [Source](https://commons.wikimedia.org/wiki/File:Table_hiragana.svg)</figcaption>

</figure>

为了能够明确地指定每个字符，我们必须为每个字符分配一个唯一的标识符。整个标识符集将被称为字符集。一旦定义了这个对应表，每个字符都需要转换成一个字节序列，这样我们就可以在计算机之间存储或共享它们。这叫做字符编码。

想象一下，我使用一个字符集来编写文本，并使用相应的编码将其转换为字节，我稍后将这些字节发送给你。在不知道我使用了哪种编码或设置的情况下，你如何解码和阅读内容呢？最终，您将不得不使用一些您知道的最常见的字符集和编码，期望结果是有意义的…什么会出错呢？

> 替换分号(；)在你朋友的 JavaScript 中加上一个希腊问号(⋲)，看着他们为语法错误大发雷霆。
> 
> <cite>本·约翰逊( [@benbjohnson](https://dev.to/benbjohnson) )，[2014 年 11 月 16 日](https://twitter.com/benbjohnson/status/533848879423578112)</cite>

所以是的…不是个好主意。

例如，位序列 1100 0011 1010 1001 代表 UTF-8 编码中的字符“é”。如果你解码这个序列，假设你必须使用拉丁-1 编码，而不是 UTF-8 编码，你会读到“γ”。

在 Latin-1 中，字符“é”用序列 1110 1001 表示。

当浏览器从您的服务器接收字节时，它需要识别在书写转换成这些字节的文本时使用的字母和符号的集合，以及用于这种转换的编码，以便进行反向转换。如果没有传输此类信息，浏览器将尝试在字节中找到可识别的模式以确定编码本身，并最终尝试一些通用字符集，这将花费时间，从而延迟页面的进一步处理。

为了加速页面的显示，必须在 HTTP 响应中指定内容编码。

## 如何选择合适的字符集？

曾经有一段时间，数百种字符编码共存，但都很有限，无法包含足够的字符来覆盖世界上所有的语言。有时，一种语言的所有字母没有合适的编码。

如今，Unicode——一种通用字符集，定义了编写大多数语言所需的所有字符——已经成为一种标准，无论您的目标是什么平台、设备、应用程序或语言。UTF-8 是 Unicode 编码之一，根据 W3C:

> 每个开发内容的人，无论是内容作者还是程序员，都应该使用 UTF-8 字符编码，除非有非常特殊的原因需要使用其他编码。(如果您决定不使用 UTF-8，您必须选择一种可以跨所有浏览器互操作实现的编码。)
> 
> <cite>[介绍字符集和编码](https://www.w3.org/International/getting-started/characters)，W3C</cite>

*注意:如果你使用数据库在服务器端存储你的内容，你可能也想使用“utf-8”字符集。注意:在 MySQL 和 MariaDB 上，它是“utf8mb3”的别名，这是一种被称为“基本多语言平面”或 BMP 的 UTF-8 编码，每个代码点最多只能存储三个字节。相反，您更愿意使用“utf8mb4”，这是一种每个码位最多存储四个字节的编码。否则，您将无法使用一些流行的字符，例如🚀，又被称为“U+1F680 火箭”！*

## 如何宣传你的字符编码…以及最好的方法。

在进一步讨论之前，让我们先来看看正在使用的词汇。

> 历史上，“字符编码”、“字符映射表”、“字符集”和“代码页”在计算机科学中是同义词[……]。但是现在这些术语有了相关但不同的含义，[……]不管怎样，这些术语仍然可以互换使用，字符集几乎无处不在。
> 
> <cite>[字符编码](https://en.wikipedia.org/wiki/Character_encoding#Character_sets,_character_maps_and_code_pages)，维基百科</cite>

我们发现“字符集”或“字符集”的这种用法实际上是在 HTML 规范中指定一种编码。我们将在本文的其余部分做同样的事情。

在 HTML 页面中指定字符集最简单的方法之一是在元素
中放入一个`<meta>`标签

```
<meta charset="utf-8"> 
```

Enter fullscreen mode Exit fullscreen mode

以这种方式声明字符集[需要遵守某些约束](https://www.w3.org/TR/html5/document-metadata.html#specifying-the-documents-character-encoding)，其中之一是包含字符编码声明的元素必须在文档的前 1024 个字节内完全序列化，以确保浏览器将接收到通过网络传输的第一个 IP 数据包的信息，并可以使用它来解码文档的其余部分。由于 charset `<meta>`标签是唯一具有这种需求的标签，所以最常见的提示是将其直接放在元素开始标签之后:

```
<html …>
  <head …>
    <meta charset="utf-8"> 
```

Enter fullscreen mode Exit fullscreen mode

如果你害怕忘记这些，不要担心。这显然是 Dareboost 将在我们的[网站质量分析工具](https://www.dareboost.com/en/tool/website-analysis)中为您执行的检查之一。但是，您可能会发现自己处于这种声明不充分的情况，浏览器没有考虑到这一点。为什么？因为页面的内容类型元数据可能指示另一个字符集，并且在发生冲突时，该信息(在页面 HTTP 头中定义)具有优先权。

为了确保通过页面元数据传输的信息，您可以使用[我们的时间轴/瀑布特性](https://www.dareboost.com/en/doc/analysis-report/timeline-waterfall)。展开主文档的详细值以查看响应 HTTP 头，包括 Content-Type 头，其中包含编码元数据。

[![Content Type Header in an HTTP response](img/1d9aa50955ad19eed482a4c779d04a74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rckhXfZQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m347mpxur8ruczgvl395.png)

要更改此 HTTP 头，您可能需要服务器管理员的帮助，而这需要您的托管服务提供商或您组织中的负责人的帮助，因为 HTTP 头的配置对于正在使用的 web 服务器来说是非常特定的，您需要适当的管理权限才能修改这些服务器设置。

在 **Apache 2.2+** 上，UTF-8 作为你的`text/plain`和`text/html`文件的默认字符集的配置涉及到[`AddDefaultCharset`指令](https://httpd.apache.org/docs/2.2/en/mod/core.html#adddefaultcharset) :

```
AddDefaultCharset utf-8 
```

Enter fullscreen mode Exit fullscreen mode

对于其他类型的文件，你需要[`AddCharset`指令](https://httpd.apache.org/docs/current/en/mod/mod_mime.html#addcharset)；

```
AddCharset utf-8 .js .css … 
```

Enter fullscreen mode Exit fullscreen mode

在 **nginx** 上，你需要确保[ngx _ http _ charset _ module 被加载](http://nginx.org/en/docs/http/ngx_http_charset_module.html)，然后使用`charset`指令。

```
charset utf-8; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，也可以使用指令`charset_types` :
来细化范围，使得除了`text/html`之外的其他类型的文件也可以在 utf-8 中交付

```
charset_types text/html text/css application/javascript 
```

Enter fullscreen mode Exit fullscreen mode

当然，您也可以从您的服务器端脚本代码中配置`Content-Type` HTTP 头。比如在 **PHP** 中，可以使用[header()网络函数](http://php.net/manual/en/function.header.php)。除了字符集之外，不要忘记定义响应正文的媒体类型(或 MIME 类型)。

```
header('Content-type: text/html; charset=utf-8'); 
```

Enter fullscreen mode Exit fullscreen mode

*注意:如果你的页面是由内容交付网络(CDN)交付的，你可能需要在 CDN 配置中配置`Content-Type`头，因为它们中的大多数不会传递它们在你的服务器上找到的头。*

## 附加资源

*   [IANA 媒体类型列表](https://www.iana.org/assignments/media-types/media-types.xhtml)
*   [Unicode 网站](http://www.unicode.org/)
*   W3C 国际化网站上的“[字符编码:基本概念](https://www.w3.org/International/articles/definitions-characters/#httpheader)”
*   还认为字符编码是一个微不足道的问题吗？请看 Andrew Nacin 在 Loop Conf 2015 上发表的“[剖析一个关键的安全漏洞](https://www.youtube.com/watch?v=yQaRUEwEKxE)”。
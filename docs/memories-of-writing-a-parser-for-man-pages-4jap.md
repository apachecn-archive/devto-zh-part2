# 为手册页编写解析器的回忆

> 原文：<https://dev.to/roperzh/memories-of-writing-a-parser-for-man-pages-4jap>

我通常喜欢无聊，但有时适可而止——2015 年的一个周日下午，我决定开始一个开源项目来克服我的无聊。

在我寻求想法的过程中，我偶然发现了一个由[马蒂亚斯·拜恩斯](https://mathiasbynens.be/)提出的构建[“用 web 标准构建的 Man page viewer”](https://github.com/h5bp/lazyweb-requests/issues/114)的请求，并且没有想太多，我就开始用 JavaScript 编写一个 man page parser，经过大量的反复，最终成为了 [Jroff](https://dev.tojroff) 。

当时，我对手册页这个概念很熟悉，并且使用了相当多的时间，但我只知道这些，我不知道它们是如何生成的，也不知道是否有标准。两年后，以下是对此事的一些想法。

### 手册页是如何编写的

当时让我感到惊讶的第一件事是，联机帮助页的核心只是存储在系统中某个地方的纯文本文件(您可以使用`manpath`命令检查这个目录)。

这些文件不仅包含文档，还使用 20 世纪 70 年代的排版系统`troff`格式化信息。

> troff 及其 GNU 实现 groff 是处理文档的文本描述以产生适合打印的排版版本的程序。与其说是所见即所得，不如说是‘你描述的就是你得到的’。
> 
> — *摘自[troff.org](https://www.troff.org/)T3】*

如果你对排版格式完全不熟悉，你可以把它们看作是兴奋剂，但是作为灵活性的交换，你有一个更复杂的语法:

[![groff-compressor](../Images/c81266be5f0a3d078837f0534da83017.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JHTHCIk6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/4419992/37868021-2e74027c-2f7f-11e8-894b-80829ce39435.gif)

`groff`文件可以手动编写，或者使用许多不同的工具从其他格式(如 Markdown、Latex、HTML 等)生成。

为什么`groff`和手册页捆绑在一起与历史有关，格式随着时间[发生了变化](https://manpages.bsd.lv/history.html)，他的血统由一系列同名程序组成:决胜>罗夫> nroff > troff > groff。

但这并不一定意味着`groff`与手册页严格相关，它是一种通用格式，已经被用于[写书](https://rkrishnan.org/posts/2016-03-07-how-is-gopl-typeset.html)，甚至用于[照排](https://en.wikipedia.org/wiki/Phototypesetting)。

而且值得注意的是`groff`还可以调用后处理器将其中间输出转换成最终格式，终端显示不一定是 ascii！支持的格式包括:TeX DVI、HTML、Canon、HP LaserJet4 兼容、PostScript、utf8 等等。

### 宏

这种格式的另一个很酷的特性是它的可扩展性，你可以编写宏来增强基本功能。

在*nix 系统的历史中，有几个宏包根据您想要生成的输出将有用的宏组合在一起用于特定的功能，宏包的例子有`man`、`mdoc`、`mom`、`ms`、`mm`等等。

手册页通常使用`man`和`mdoc`编写。

通过标准的`groff`包大写宏名的方式，你可以很容易地区分本机`groff`命令和宏。对于`man`，每个宏的名字都是大写的，比如。PP，。TH，。SH 等。对于`mdoc`，只有第一个字母大写:。Pp，。Dt，. Sh。

[![groff-example](../Images/b02e7e7a819c1115d5ed934e45a57140.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PkxHOKuH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/4419992/37866838-e602ad78-2f6e-11e8-97a9-2a4494c766ae.jpg)

### 挑战

无论您是在考虑编写自己的`groff`解析器，还是仅仅出于好奇，这些都是我发现更具挑战性的问题。

#### 上下文相关语法

从形式上看，`groff`有一个上下文无关的语法，不幸的是，由于宏描述了不透明的标记体，包中的宏集本身可能不会实现上下文无关的语法。

这使我远离了(无论是好是坏)当时可用的解析器生成器。

#### 嵌套宏

`mdoc`中的大多数宏都是可调用的，这大致意味着宏可以用作其他宏的参数，例如，考虑以下情况:

*   宏`Fl` (Flag)给它的参数加了一个破折号，所以`Fl s`产生了`-s`
*   宏`Ar`(参数)提供了定义参数的工具
*   `Op`(可选)宏将它的参数括在括号中，因为这是定义可选内容的标准习语。
*   下面的组合`.Op Fl s Ar file`产生`[-s file]`，因为`Op`宏可以嵌套。

#### 缺乏适合初学者的资源

真正让我困惑的是缺乏一个规范的、定义明确的、清晰的来源，网络上有很多信息，这些信息假设了很多关于读者的东西，需要花时间去理解。

### 有趣的宏

最后，我将向您提供一个非常简短的宏列表，这些宏是我在开发 jroff 时发现的有趣内容:

**男人**

*   *TH:* 用`man`宏写手册页的时候，你的第一行不是注释的必须是这个宏，它接受五个参数:标题节日期源手册
*   *BI:* 粗体和斜体交替出现(对函数规范特别有用)
*   *BR:* 粗体与罗马体交替出现(特别适用于参考其他手册页)

**mdoc**

*   *。Dd，。Dt，。操作系统:*类似于`man`宏需要`.TH``mdoc`宏需要这三个宏，按照特定的顺序。它们的首字母代表:文件日期、文件标题和操作系统。
*   *。Bl，。它，。El:* 这三个宏用于创建列表，它们的名字不言自明:Begin list、Item 和 End list。

### 来源

*   [GNU Troff 手册:历史](https://www.gnu.org/software/groff/manual/html_node/History.html#History)
*   [UNIX 手册页的历史](https://manpages.bsd.lv/history.html)
*   [为 BSD 手动显示弃用 groff】](https://2009.asiabsdcon.org/papers/abc2009-P6B-paper.pdf)
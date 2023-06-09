# 为什么 Elm 是构建 MiniLatex 的合适工具

> 原文：<https://dev.to/jxxcarlson/why-elm-is-the-right-tool-to-build-minilatex-3b87>

LaTeX 是事实上的标记语言，用于撰写包含大量数学内容的技术文章——研究文章、课堂笔记等。在这篇文章中，我将描述我如何使用 Elm 为 LaTeX 构建一个实时渲染的 web 应用程序。你可以在 [MiniLatex Live](https://jxxcarlson.github.io/app/miniLatexLive/index.html) 上尝试演示，如下图。(本文在图片下方继续)

[![](img/630be69585c27644a6c8770cc3215ab7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GDpGvklc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tpwu40b7t1v152rvoyt9.png)

## 关于乳胶

关于乳胶的几句话。通过 PDF 将其打印出来的基础设施非常好，而且已经建立。对于网络，有一个部分的解决方案。LaTeX 文档由两种文本组成——数学模式和文本模式。公式是数学模式的，其余的—章节标题、目录、交叉引用、定理环境、宏、普通的旧散文等等。—处于文本模式。Mathjax 或 KaTeX 可以渲染数学模式的材质，但是据我所知，到目前为止还没有办法让 *live* 渲染所有的 LaTeX。这就是 MiniLatex，一个 3k loc Elm 库所做的。总之，它使用基于`elm/parser`库的定制解析器将 LaTeX 源文本转换成抽象语法树(AST)。一旦有了 AST，编写文本模式材料的呈现器就相当简单了，数学模式材料委托给 MathJax。

## 开头

一年多前，在 2017 年 6 月 Elm Europe 会议后不久，我开始从事 MiniLatex 项目。那时我意识到`elm/parser`可能有解析 LaTeX 的魔力。幸运的是，解析器存在于构建 Elm 应用程序的框架中，所以这种组合非常适合我的需要。即使是一年后的现在，解析器在 3K loc 库中也只有 300 行代码。但是写它对我来说是一个真正的挑战:(a)我是一个数学家，不是计算机科学家，我对解析只知道一点点；(b)通常从语法开始，然后编写解析器——但是没有为 LaTeX 编写的语法；(c)因此，必须对语法和解析器进行逆向工程；(LaTeX 的任何适当子集的任何语法必须是上下文敏感的；(e)因此不能使用标准的解析器-生成器工具。

## 障碍

尽管有这些障碍，我(幸运的是)在很大程度上只是模糊地意识到，我还是开始了解析器的工作。`elm/parser`库是一个解析器组合器库。它提供了以合理的方式从小片段构建解析器的工具。这是理想的实验，这是我在没有语法的情况下必须做的。第一步是为 AST 定义一个 Elm 类型。就是这里:

```
type LatexExpression
    = LXString String
    | Comment String
    | Item LatexExpression
    | InlineMath String
    | DisplayMath String
    | SMacro String (List LatexExpression) (List LatexExpression) LatexExpression 
    | Macro String (List LatexExpression) (List LatexExpression) 
    | Environment String (List LatexExpression) LatexExpression
    | LatexList (List LatexExpression)
    | LXError (List DeadEnd) 
```

Enter fullscreen mode Exit fullscreen mode

仅仅 11 行代码管理一个 300 行的解析器，它是一个 3000 行库的核心。我没有一口气写完 11 行。我写了几行代码，试图构建一个解析器来处理它们，然后进行迭代。

## 起初进展缓慢

起初进展相当缓慢。Evan Czaplicki 带来了`elm/parser`，解析器组合器库。尽管我读过一些关于解析器组合子的知识，但我从未使用过它们。谢天谢地，我得到了榆树疏林社区的慷慨帮助，经常在那里闲逛的伊利亚斯·范·皮尔帮助我度过了许多许多难关。(谢谢伊利亚斯！)再见再见我找到窍门了。

在接下来的几个月里，事情逐渐明朗起来，到 2017 年 9 月 Elm Conf 的时候，我已经有了一个不错的工作原型。但是有一个问题。解析器太慢了！我可以轻松地用 15 秒钟解析几页文本。我和 Ilias 反复讨论这个问题，他发现瓶颈是——字符被打包到一个 JS 对象中(据我所知)。这个问题可能会在 0.19 版本的编译器中得到解决，所以与此同时，使用 Ilias 的另一个建议，实现一个不同的策略，使增量解析-呈现操作(在初始的完整文档解析之后)**非常**快。

## 0.19！！！

当 0.19 的 alpha 出来时，所有这些努力都集中到了一起。库被重写，结果 MiniLatex 解析器(也必须重写)快得令人难以置信。这种加速，加上 diffing 策略，使得实时呈现 LaTeX 文档成为现实。我没有想到这是可能的，但是，是的，这是可能的！

## 注释

Elm 是我第一次使用类型化函数式语言。我花了一段时间才很好地理解了类型系统，也体会到了它的强大。我现在意识到这是 Elm 的一大优势。当然，作为开发人员，我们在日常工作中会遇到这种情况，当我们开始一个极端的重构操作，一两个小时后一切正常。(谢谢编译器，谢谢你有用的消息！)但是在这个项目中，类型系统起到了核心作用。MiniLatex 的真正开始是在我写下第一个`LatexExpression`类型的时候。解析器的代码就是从这种类型定义中发展出来的，并且自始至终发挥着指导作用。

Elm Slack 社区的慷慨帮助，尤其是 Ilias 的帮助，使得 MiniLatex 项目的成功成为可能。我还要感谢 Evan Czaplicki 和 Luke Westby，他们在我从 0.18 过渡到 0.19 的一些关键时刻帮助了我。和 Elm 一起工作是一种享受，我想对 Evan 和他的核心团队表示感谢。太棒了。你做了一件多么神奇的东西啊！！

### 告诫

MiniLatex 是两个东西:Latex 的真子集和该子集的解析器呈现。当然，在时间和精力允许的情况下，这个子集还可以扩充，但仍足以写一些相当沉重的讲稿。MiniLatex 文档也可以导出并通过标准机器运行，如`pdflatex`。

### 注

MiniLatex 嵌入在一个位于 [knode.io](https://knode.io) 的应用程序中，该应用程序提供了一个实时编辑环境、一个存储您编写的文档的可搜索存储库、一个图像上传程序和一个可搜索的图像目录。
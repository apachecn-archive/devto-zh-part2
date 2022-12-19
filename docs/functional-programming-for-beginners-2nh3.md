# 面向初学者的函数式编程

> 原文：<https://dev.to/tzemanovic/functional-programming-for-beginners-2nh3>

最近，我参加了 Manuel Chakravarty 的 YOW！夜谈的题目是揭开函数式编程的神秘面纱，这启发了我写这篇文章。

从我的工作中，我获得了一些直觉，试图通过首先解释理论来教 <abbr title="functional programming">FP</abbr> 并不奏效。事后看来，这似乎是显而易见的。我们很多人都是这样学习数学的，可悲的是，这让我们很多人误解甚至鄙视数学，尽管它可能在他们选择的领域非常有用 <sup>1</sup> 。

效果更好的是展示了 FP 如何优雅地解决了我们关注的许多问题；一种是需要使用解析器，另一种是我们使用反应式编程来实时处理和分析大量数据。这些是我在 Haskell 中学到的一些概念，并且能够应用到 Scala 中。它们很难用命令的方式来实现，这只会使它们更复杂，也使它们更难理解。

我的直觉确实与曼纽尔的谈话内容一致。凭借他与 Haskell 一起工作和教学的长期经验和承诺，Manuel 讨论了我们可以做些什么来将 FP 从创新者和早期采用者阶段推进到早期多数 <sup>2</sup> 阶段，他总结为:

*   首先举例
*   教授设计模式
*   紧密反馈回路
*   观想可能是一个有效的工具

另一位经验丰富的 Haskeller 加布里埃尔·冈萨雷斯为 Haskell 初学者写了一些很好的建议，他建议:

*   避免预先大规模设计
*   在术语级别开始编程(使用简单的值和函数)
*   建造一些有用的东西

如果您尝试了 Haskell，但仍然觉得它太吓人，我会建议您看看 Elm。Elm(它本身是用 Haskell 编写的)就其语法大小而言是一种非常小的语言，这使得它非常适合初学者。它不仅在上述所有方面都非常出色，还可以与 React 等其他前端框架竞争。语言是通用的这一概念很容易让人误解，Elm 故意不是一种通用语言 <sup>5</sup> 它很好地服务于它的预期目的。不过你不必相信我的话，相反，你可以看看 Richard Feldman 的[演讲，他已经在制作中同时使用 React 和 Elm 一年多了。](https://youtu.be/EStQa0QsUb8)

酷，你抓到我了，但是…

## 从哪里开始？

[![Welcome to Elm](img/1cba7efbe40f8f570d6da6bb42687e0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yRxyK12l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tzemanovic.github.img/elm-grove.jpg)

当然，不是每个人的学习方式都一样。作为一般建议，我会说:

尽量不要一次学太多东西，保持简单。如果你带着以前从命令式语言得到的经验来到 FP **，这一点尤其重要，因为命令式语言不能很好地转化为 FP 概念。你可能听过函数式程序员说，当你开始时，你将不得不忘记一些东西，这就是他们所指的。老实说，你需要耐心，因为一开始一切看起来都很慢。没关系，不要放弃，坚持下去！**

试着找一些已经有 FP 经验的人，请他们帮忙写一些简单的东西。当你看到 FP 应用于解决一个实际问题时，你很有可能学得更快。有人说他们能够在一周内以这种方式捡起榆树，但是不要把你的期望定得太高。再次，每个人的学习路径不同。寻找你所在地区的 FP [聚会](https://www.meetup.com/)，加入 [Elm on Slack](https://elmlang.herokuapp.com/) ，阅读综合 [Elm 指南](https://guide.elm-lang.org/)。不要开始尝试写一个成熟的单页 app，有办法[在今天的工作中逐步引入 Elm](http://elm-lang.org/blog/how-to-use-elm-at-work)。或者你可以直接从你的浏览器玩[，你甚至不用安装任何东西。](http://ellie-app.com/)

最后，不要害怕犯错误，因为这是任何学习过程中重要的一部分。

* * *

1.  莱斯利·兰波特:为什么计算机科学家不学数学？ ↩

2.  维基百科:创新的扩散 ↩

3.  Gabriel Gonzalez:给 Haskell 初学者的建议 ↩

4.  Bret Victor:一个技术专家能为气候变化做些什么？个人观点。 ↩

5.  埃文·恰普利基:关于“通用”语言 ↩
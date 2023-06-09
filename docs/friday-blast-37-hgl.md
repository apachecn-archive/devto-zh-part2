# 周五爆炸#37

> 原文：<https://dev.to/horia141/friday-blast-37-hgl>

今天的星期五链接遍布地图。不可能所有的都有主题。

[各种 IO 阻塞、非阻塞、多路复用和异步(2018)](https://www.rubberducking.com/2018/05/the-various-kinds-of-io-blocking-non.html) -核心观点是异步 I/O 独立于多线程。除此之外，从时间和工作的角度来看，它非常系统地涵盖了程序处理 I/O 的各种方式。

Docker 是新的 Git 吗？(2018) -作者指出，有一堆技术会胜出，因为它们对大多数情况来说都很好或足够好。即使你没有他们正在解决的问题，或者如果你投资了一个替代方案，采纳他们可能是有意义的。Git 是一个这样的工具，Java 是另一个，云是第三个，Docker 可能是一个很好的候选。

[更多的环境不会让事情变得更容易(2018)](https://bravenewgeek.com/more-environments-will-not-make-things-easier/)——链接到泰勒·特里克的博客总是一种享受。双关语。但是它们通常处理分布式系统的底层问题。今天的链接是关于架构问题和微服务的困难，以及在集成层面处理这些问题的一些策略。我喜欢消费驱动的契约测试的想法，以及对保持这些事情运行的是好的团队而不仅仅是实践 X 或 y 的认识。

[事件采购变得简单(2018)](https://kickstarter.engineering/event-sourcing-made-simple-4a2625113224) -事件采购是大型应用架构的一种有趣方法。可悲的是，它确实需要纪律和大量的基础设施来使它们工作。这对于较小的项目来说可能不值得。但是本文只是介绍了在这种情况下的工作方法。这很有趣，但它主要是 ES 概念的改进——事件表和集合由来自单个请求处理程序的同一个事务编写，更紧密的代码耦合等。这是我实际上*在一定程度上在项目中使用*的东西。所以没什么特别的。

[概念压缩意味着初学者不需要知道 SQL - hallelujah (2018)](https://m.signalvnoise.com/conceptual-compression-means-beginners-dont-need-to-know-sql-hallelujah-661c1eaed983?source=rss----668e14b18fb1---4) -一篇关于我们如何作为一个领域前进的 DHH 文章，昨天是*基本知识*的东西，变成了今天的奥秘。这个例子是关于 SQL，但是 CPU 和内存结构，汇编程序，操作系统内部等等。都在光谱的某处。我认为这里的杀手锏是，我们做所有这些抽象的目的是让一个开发人员或一小组开发人员更容易完全构建一个应用程序。我认为这是一件非常强大的事情。因为当你需要 10 多种类型的开发人员来做一些事情的时候，这一点都不好玩。
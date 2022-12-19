# 如果您以前从未看过 Clojure...

> 原文：<https://dev.to/headwinds/if-youve-never-looked-at-clojure-before-5158>

[![women of nasa lego](img/ccac1afb4cbd108244ec89ccf37edafe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0b75xHDJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lac6r23g1clszaar0xfe.jpg)

如果您以前从未看过 Clojure，这篇简短的文章可能是您的第一次尝试，我希望您能体会一下。Clojure 是 Java 的替代品。它具有与 Java 互操作的特性，这意味着您可以在 Clojure 中访问核心 Java 函数。它是一种服务器端语言，因此您还需要 Javascript 或 ClojureScript 来编写基于 web 的客户端代码。

我正在做一系列 Clojure & ClojureScript 课程来帮助我学习语言，并且我已经完成了[第一课](https://glacial-badlands-20785.herokuapp.com/)介绍地图，过滤&减少，我想和你分享一下。

为什么要学 Clojure？如果您是函数式编程设计模式的爱好者，您将会欣赏 Clojure 函数的不可变特性和它在服务器端开发中产生高度可测试代码的 FP 方法。像 Python 一样，Clojure 也有一个 REPL(T1 ),在这里你可以实验和测试你程序的功能，大大加快开发速度。

在编写 Clojure 并从经验丰富得多的 Clojure 开发人员那里收到宝贵的反馈之后，我已经设法将几个 pull 请求合并到我们的主分支中，并且我主要学会了通过提取复杂的部分并将它们分解成更小、更易管理的函数来简化我的 Clojure 函数，从而增加代码可读性。一旦你开始重构深度嵌套的函数，所有这些括号开始变得有意义，你可以获得一些简洁明了的代码。
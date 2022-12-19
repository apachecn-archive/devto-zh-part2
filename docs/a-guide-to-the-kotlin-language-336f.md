# 科特林语指南

> 原文：<https://dev.to/raulmonteroc/a-guide-to-the-kotlin-language-336f>

[![](../Images/9fffbb5718cc3cae0d47414dbab8ce86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F5e5_4Fk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raulmonteroc.com/wp-content/uploads/2018/07/kotlin_800x320-1.png)

你听说过科特林吗？这是一种相对较新的语言[，由](https://blog.jetbrains.com/kotlin/2016/02/kotlin-1-0-released-pragmatic-language-for-jvm-and-android/) [JetBrains](https://www.jetbrains.com/) 于 2016 年正式推出。他们最初创建这种语言是为了作为 java 的更灵活和更具表现力的替代品，能够在 JVM 上运行，允许开发人员在一个项目中互换使用 kotlin 和 Java 代码。

从那时起，发生了很多事情，科特林现在可以做一些额外的把戏:

*   自谷歌[在 2017 年谷歌 I/O 上宣布](https://www.youtube.com/watch?v=d8ALcQiuPWs)以来，它在 Android(和 Android Studio)上受到官方支持。
*   转换编译成 javascript(就像 CoffeeScript 或 Typescript 那样)
*   使用 [kotlin native](https://kotlinlang.org/docs/reference/native-overview.html) 在没有虚拟机的情况下运行
*   使用 Objective-C 的互操作在顶级 iOS 和 mac os 上运行

我不久前开始学习 kotlin，发现它处理事情的方式和它在一个以冗长著称的环境中增加的表现力非常有趣。我对这门语言了解得越多，就越喜欢它。所以我决定通过写一个迷你博客系列来分享我的想法和经验，这个系列可以揭示这种语言的实用主义本质和哲学。

我希望这对你和对我一样有用，即使你最终没有使用它，至少你可以带着科特林的思维方式。

# 让我们开始吧

Kotlin 有许多特性，有些你可以在其他语言中找到，有些是 kotlin 独有的，但所有这些特性的共同点是作为语言哲学的一部分，促进了简洁和富有表现力的代码。

我将在这里留下一个目录，简要描述您将会遇到的内容。你可以随意按你喜欢的顺序阅读。

不幸的是，由于这是一个正在进行的系列，并非所有的帖子都可用，但欢迎你阅读那些准备好的。我会在这里和 [twitter](https://twitter.com/raulmonteroc) 发布更新，所以别忘了订阅。

*   [变量和类型。](https://raulmonteroc.com/mobile/kotlin/variables-and-types-in-kotlin/)kot Lin 中的变量和我们习惯的有点不一样。在这里，我们将探讨不同之处，以及所使用的类型系统，以及它与其他语言的比较。
*   **数组和集合。**我们使用数据与我们的应用程序进行交互，我们这样做的方式是使用数组和集合。在本节中，我们将了解这些数据结构的特点以及如何充分利用这些数据结构。
*   [可空性。在 kotlin 上，可空性是一个非常有趣的话题，主要是因为与其他强类型语言不同，它不允许空值开箱即用，从而保护您的代码免受著名的 NullPointerException 的影响](https://raulmonteroc.com/mobile/kotlin/nullability-in-kotlin/)
*   **操作员**。每种语言都有运算符来以直接的方式表达逻辑。Kotlin 使用我们在 C-descendants 语言中习惯使用的那个，并且也提供了一些自己的。
*   **流量控制和循环。如果你已经用其他语言编程过，你一定习惯了流控制和循环，在 kotlin 上，我们在上面加了一点糖，使它更有表现力，更容易阅读。**
*   **类和面向对象编程。**如果你已经用 _any _OOP 语言开发过，你肯定会对这个话题感到舒服* *但是** 像往常一样，Kotlin 用可组合对象、默认实现甚至更简单的方法来表达和声明类，把这个话题带得更远。
*   **仿制药。**你用过某一特定类型的系列吗？类似阵<弦>的东西？在这里，我们将学习如何在你的类中使用一个模板，你可以在以后传递一个类型来提供额外的灵活性和可重用性，就像集合一样，你会觉得你有多个类，但只有一个。
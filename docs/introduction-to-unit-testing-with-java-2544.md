# Java 单元测试简介

> 原文：<https://dev.to/chrisvasqm/introduction-to-unit-testing-with-java-2544>

<center>*Cover image by [Hans-Peter Gauster](https://unsplash.com/@sloppyperfectionist) on [Unsplash](https://unsplash.com/)*</center>

我们都有过这样的经历:我们在一个项目上花费了如此多的时间，我们确保运行我们可能想到的每一个可能的场景，以便使它尽可能好。但是，当你让其他人尝试时，他们会发现一些小的边缘情况，可能会让你的应用程序以意想不到的方式运行。

为了防止这种情况经常发生，我们使用不同类型的测试技术:单元测试、集成测试和端到端测试。虽然后者(端到端测试)可以由开发人员或质量保证团队成员进行。

这也被称为测试金字塔

[![Testing Pyramid](img/8e9d2536652566abc43bffe8b94dc36b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pk16J2XW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0duko03p8lltyies0deu.png)

试着不要被前面提到的另外两种类型的测试分散注意力，而是集中在这样一个想法上，我们可以期望我们的单元测试在数量上比其他的多得多，但是要记住它们不能互相替代。**都很重要**。

闲聊够了，让我们开始编码，好吗？

## 要求

*   在你的机器上安装 JDK(废话！).
*   一个 IDE(我会推荐 IntelliJ IDEA 社区版)。
*   就 Java 语言而言，你需要熟悉 a `variables`、`constant`、`function`、`class`和`object`的概念，才能完全理解这篇文章。(这个我也许能帮上忙[这个](https://dev.to/chrisvasqm/how-i-finally-understood-what-a-class-is--24pl))。
*   将这个 GitHub Gist 的[中的数学`class`添加到你的项目中。](https://gist.github.com/chrisvasqm/440920cc73b766d61239716249adcb5a)
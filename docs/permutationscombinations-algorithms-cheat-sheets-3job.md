# 排列/组合算法备忘单

> 原文：<https://dev.to/trekhleb/permutationscombinations-algorithms-cheat-sheets-3job>

### TL；速度三角形定位法(dead reckoning)

本文简要描述了数学上的*排列*和*组合之间的区别，*解释了排列和组合算法背后的主要思想，并包含 JavaScript 中算法实现的链接。

> JavaScript 代码示例可以在 [JavaScript 算法和数据结构](https://github.com/trekhleb/javascript-algorithms)库中找到。更多详情请阅读。

### 什么是组合和排列

假设我们有某个东西的**集合**或**集合**(数字、字母、水果、硬币等的集合。)我们需要**从一个收藏中挑选物品**来组成另一个收藏。例如，假设您正在从可用号码集合中挑选彩票号码({0，1，2，3，4，5，6，7，8，9} → {4，5，9})。或者你从一堆可用的水果中挑选水果来做水果沙拉({橘子、苹果、香蕉、葡萄}→{苹果、香蕉})。或者您正在尝试猜测锁定密码，并且您正在从{0，1，2，3，4，5，6，7，8，9}组中选择 3 个数字来猜测正确的密码({1，1，2}、{1，1，3}、{1，1，4}、…)。在所有这些情况下，您都是通过遵循一些规则从另一个集合中创建一个集合。这些规则定义了你的新集合是一个**排列**还是一个**组合**。

如果你的新集合**中元素的**顺序**很重要**，那么你就要处理**排列**(例如，在锁定密码的情况下，{1，1，2}的集合与{2，1，1}的集合不同)。否则，如果**顺序无关紧要**，那么你就是在做一个**组合**(水果组合{苹果、香蕉}来做沙拉——不管你先挑苹果还是香蕉)。

您的新收藏**可能包含也可能不包含重复(或重复)**。例如，在“锁定密码”中，你可以使用重复的数字，但是当你处理一组中奖者({Black，Smith，Brown})时，你不可以使用重复的数字，因为同一个人出现在两个中奖位置上是没有意义的。

为了说明所有这些差异，我制作了一些小备忘单，以便更容易记住它们。

### 排列概念备忘单

[![](../Images/03d76d431ef1abd049a3acda7c7c4b12.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E9z5AHCM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AJNK-n0Pt0Vbxk0lxVpgT5A.png)

### 组合概念备忘单

[![](../Images/9f00eb7a250e7dfc08009935c00fad49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---gWPRp-N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A7cFRn8jW4g_91YgDAbmxRQ.png)

### 排列/组合算法备忘单

如果你想实现排列和组合算法，你可能会发现下面的排列/组合算法备忘单很有用。

[![](../Images/6cbbb9286755d13a2b164d72a85a6345.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G_B-omF1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AvLsSsZMnesCFPCYTYMbxrQ.png)

### JavaScript 中的组合和排列

如果您想查看这些算法在 JavaScript 中的具体实现，可以点击以下链接:

*   [JavaScript 中的组合](https://github.com/trekhleb/javascript-algorithms/tree/master/src/algorithms/sets/combinations)
*   [JavaScript 中的排列](https://github.com/trekhleb/javascript-algorithms/tree/master/src/algorithms/sets/permutations)

我希望你会发现这些备忘单很有帮助，它们会帮助你顺利地编写组合/排列算法。

编码快乐！
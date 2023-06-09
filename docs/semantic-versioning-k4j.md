# 语义版本控制

> 原文：<https://dev.to/zellwk/semantic-versioning-k4j>

[https://www.youtube.com/embed/rTZ35Subk9U](https://www.youtube.com/embed/rTZ35Subk9U)

当我们在 Git Flow 上工作时，我们为版本和补丁创建了像`1.0.0`和`1.0.1`这样的数字。这些数字代表什么，我们为什么要用它们？

这些数字代表我们在全球推出的产品的版本号。我们使用它们是因为我们遵循了一种叫做语义版本化的最佳实践。

当我们使用语义版本控制时，开发人员将知道一个改变是否会破坏他们的代码。这些数字为已经发生的变化提供了线索。

许多流行的项目使用语义版本控制。例如 React 和 Vue。

## 理解语义版本化

一个语义版本有三个数字。最右边的数字是补丁版本。

### 补丁版本

补丁版本用于修复错误。功能没有变化。(这就是为什么我们在[前一课](https://dev.to/blog/git-flow)中发布修补程序时使用补丁版本的原因)。

当你增加一个新的补丁时，最右边的数字加 1。从 1，你把它增加到 2，然后到 3，以此类推。

**如果你的补丁号是 9，**当你再次增加补丁版本，**你增加到 10，**然后 11，然后 12，依此类推。(人数没有限制)

[![A patch version is the rightmost number](img/1d257c31f4c91f831ef09302f0c5d5e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ex0nbxWE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/semantic-versioning/patch.png)

### 次要版本

第二个数字称为次要版本号。当您在项目中发布新功能时，会用到它**。**

当您增加次要版本时，您也增加了 1。但是**当你增加次要版本时，你必须将补丁版本重置为零。**

[![A minor version is the second number](img/59bba0882b213906cfac4fbd21def961.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eD3KqXFf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/semantic-versioning/minor.png)

### 主要版本

最左边的数字是主要版本。当你增加主版本时，你告诉人们有**向后不兼容的变化**。如果人们使用下一个版本，他们可能会遇到破损。

当您增加主版本号时，您会重置补丁版本和次版本。

[![A major version is the leftmost number](img/0e96a0a9f889be62bf507a0127698c5a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YzTtQkoy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/semantic-versioning/major.png)

## 预发布

如果你想创建一个预发布版本(比如一个 alpha 或 beta 版本)，你可以添加一个`-`，后面跟着单词`alpha`或`beta`。

对于预发布的没有硬性的规定，所以你可以给它们起任何你想要的名字。通常我们用 alpha 或者 beta，后面跟一个数字，像`alpha1`。

## 开始一个项目

大多数人以`0.1.0`开始项目。当您准备向公众发布项目时，您将版本增加到`1.0.0`。

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。
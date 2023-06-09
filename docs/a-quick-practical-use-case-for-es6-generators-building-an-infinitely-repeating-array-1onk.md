# ES6 生成器的快速实用用例:构建无限重复的数组

> 原文：<https://dev.to/sreisner/a-quick-practical-use-case-for-es6-generators-building-an-infinitely-repeating-array-1onk>

[![](img/0872b04194a042c2a37131ec4895871f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_nzSaA-V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6mytns4vyiea6h7gtsew.png)

### 前言

您可能听说过 ES6 生成器，也许您甚至学习过语法，您可能想知道它们在现实生活中到底有什么用。

#### 定义(来自 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)

> *发电机是可以退出并在以后重新进入的功能。它们的上下文(变量绑定)将在重入时被保存。*

你可能会想，“好吧，但是我为什么要这么做呢？“事实证明，有从简单到复杂的一系列用例，其中许多都涉及到承诺异步请求(async/await 构建在生成器之上)。我的目标是通过一个简单的、真实的例子，向您提供理解它们如何工作的第一步，以便您开始注意到什么时候生成器是您自己代码中问题的最合适的解决方案。开始了。

### 用例

我正在构建一个应用程序，我的用户可以计算 3 周的锻炼周期，并设置在周期内每周工作 3 至 7 天。每个个人健身程序都基于以下 4 个举重项目中的一个:*深蹲*、*卧推*、*硬拉*和*仰卧推*，并且每个连续健身程序都必须以该顺序中的下一个举重项目为基础:

*   蹲着的
*   长凳，长椅
*   硬举
*   高架压力机
*   蹲着的
*   长凳，长椅
*   硬举
*   高架压力机
*   …

你大概可以看到这是怎么回事。

我需要我的代码来说，“*给我一次锻炼的机会，然后下一次，然后下一次，等等。当到达列表的末尾时，从头开始，永远重复，直到我生成了 3 周周期的所有锻炼。*“这是我最初实现它的简化版本，没有生成器的**:**
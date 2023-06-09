# 我能要回我的 CPU 吗？

> 原文：<https://dev.to/rionmonster/can-i-have-my-cpu-back-visual-studio-hc7>

*这篇文章最初发表在[我的博客](http://rion.io)上。*

Visual Studio 2017 无疑是旗舰 IDE 上一次迭代的重大改进；然而，它也不是没有缺陷。

如果你是一名 web 开发人员，你可能已经习惯了 Google Chrome 基本上占用了你的机器的所有内存(可能更多)，但是今天我们将关注另一个有价值的资源:CPU 周期。Visual Studio 2017 有一个令人讨厌的习惯，它会偶尔冒出来，让您和您的开发机器陷入瘫痪；谢天谢地，这篇文章将帮助你解决这个问题。

## 你在 Visual Studio 做什么？

[![What is you doing Visual Studio](img/48cc858fb43173c64374694a2b308e36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qo1-etw9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jqbhuzwnj80wyzydr2pg.png)

Visual Studio 2017 通常可以在用户不做任何事情的情况下，毫无理由地将机器的 CPU 限制在 75%以上。

整件事情的奇怪之处在于，有时当您真正开始执行操作(例如，在编辑器中滚动、键入等)时，高 CPU 使用率实际上会恢复到正常水平。).诊断和忍受这种情况似乎令人难以置信地沮丧。

在回顾了一些堆栈跟踪并与 Visual Studio 团队的一些成员交谈后，问题本身似乎与源代码控制以及 Visual Studio 如何处理与 Git(或您正在使用的任何东西)的同步有关。似乎给定项目/解决方案中的文件数量越多，问题就越严重。

幸运的是，我们可以使用一些变通方法来缓解这个问题，直到它得到解决。

## ~~宝贝~~ CPU 回来了，你可以把这一切都归咎于~~我~~饭桶。

由于我们可以确认该问题与源代码控制同步有关，这为我们提供了两种方法之一:

*   **在 Visual Studio 中禁用源代码管理同步**
*   **清除用户解决方案设置**

第一个选项是在 Visual Studio 中禁用源代码管理同步，如果您已经在使用命令行来跟踪您的更改和管理您的工作流，这可能是一个选项。修复相对简单，**进入工具- >选项- >源代码控制- >当前源代码控制插件，并将其设置为“无”**:

[![Disabling Source Control in Visual Studio](img/a16bdb7a8bd55725981734e32b80af5d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ElfWAHZN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/64b2acgq6mepuhc3gz6v.PNG)

这将防止 Visual Studio 管理您的更改跟踪，从而在空闲时消耗宝贵的 CPU 周期。

对于那些喜欢 Visual Studio 的源代码管理的人来说，前面的方法实际上是行不通的。

幸运的是，您可以“重置”给定解决方案的同步行为，这似乎可以让事情回到正轨，至少可以阻止 Visual Studio 在空闲时大量消耗 CPU 周期。**要做到这一点，找到隐藏的。vs 文件夹，并删除。锁内文件:**

[![Removing the Hidden SUO Directory](img/b732ad735403cb4e587bf4f251e4cf21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AiMN7ndp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8kdbb24q1ldx5nj0m6dm.gif)

这应该有助于暂时缓解这个特定解决方案的随机 CPU 限制，但是如果问题再次出现，您可能必须再次清除这个隐藏文件。

您可能希望在 Visual Studio 2017 即将发布的版本中解决这个问题，但如果这一直是您的眼中钉，这应该有望让您的生活变得轻松一些。
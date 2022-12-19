# 我们现在有了自我修复的自动化测试

> 原文：<https://dev.to/razgandeanu/we-now-have-self-healing-automated-tests-4ojj>

有些时候，我确实觉得我们生活在未来。

人工智能和机器学习正在帮助我们解决日常问题的未来。

几天前，当我发现 [Endtest](https://endtest.io) 引入了自我修复的自动化测试时，我就经历了这样的时刻。

[![Self-Healing Tests Endtest](../Images/e1f74988e5c83117d6e38a3d5420ca33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZSRk7ug7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/8gYBnBA.png)

但是它是如何工作的呢？

根据他们的常见问题:
***每当你运行一个测试，我们的平台就了解更多关于你的应用。
如果您的应用程序中的一个元素被更改，测试被破坏，我们的引擎将通过更新定位器自动修复被破坏的步骤。***

令人着迷。

似乎他们的技术使用了某种机器学习。

这意味着我们不必花费宝贵的时间来维护我们的测试。

我已经试过这个功能了。我是这样做的:

1.  我更改了应用程序中一个输入的 ID。
2.  我在自我修复模式下进行了测试。
3.  测试检测到了变化，并从我的测试中更新了定位器。

太棒了。
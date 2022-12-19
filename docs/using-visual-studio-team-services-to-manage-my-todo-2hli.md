# 使用 Visual Studio Team Services 管理我的待办事项

> 原文：<https://dev.to/ankitvijay/using-visual-studio-team-services-to-manage-my-todo-2hli>

我是一个相当没有条理的人。我大部分时间都在办公室或开会时坐在电脑前。在家里，我通常忙于我 2 岁的孩子或家务。结果，我的待办事项列表持续增长。当我找到时间做清单上的工作时，我无法按照正确的顺序排列它们，或者在最坏的情况下，完全忘记了一项重要的工作。

这是一个普遍的问题，我相信你们大多数人都遇到过。

过去，我尝试过使用一些 ToDo 应用程序，Windows 便笺，OneNote，提醒等。跟踪我的物品。然而，这些方法中的每一个都会在几天内很好地工作，然后我会回到起点。

我的一些痛点是:

*   一个任务(或一个待办事项)在本质上可能非常复杂。它可能涉及不同时间点的多个子任务。这些应用程序都不能让我轻松做到这一点。虽然我可以使用 OneNote 或便笺条上的自由文本创建这些任务，但我发现它非常混乱和不一致。
*   任务可以属于不同的**类别**。例如，像续保、支付账单这样个人任务，或者像跟踪同事这样的官方任务。同样，ToDo 应用程序在对任务进行分类时没有达到预期。
*   任务可能处于不同的**阶段**，如尚未开始、正在进行、等待响应/被阻止等。我不能在 ToDo 应用程序中这样做。
*   基于它们的**类别**，每个任务可以有**优先级**。例如，我可能有多项需要关注的任务，但它们分属不同的类别。将它们放入 ToDo 应用程序或 OneNote 本身就是一项艰巨的任务。

这些问题让我对我的问题陈述有了不同的看法。我意识到一个待办事项应用程序或提醒应用程序不足以解决我的问题。那时我切换到 Visual Studio Team Services (VSTS)来管理我的待办事项任务列表。

## **为什么是 VSTS？**

在入围 VSTS 之前，我考虑了以下工作流程工具:

*   吉拉
*   特雷罗
*   VSTS

在办公室，我们使用吉拉进行软件开发项目。然而，我发现吉拉是相当沉重和笨重的使用。总的来说，我对任何 Atlassian 产品的体验都不是很好，比如吉拉、Bitbucket、Confluence 等等。

我需要的是轻量级的，易于创建/修改任务，易于定制，但管理开销最小的东西。Trello 看起来勾选了几个框，但我个人并没有怎么用过。还有什么？现在被亚特兰蒂斯 [![🙂](../Images/1bb31e891282bfa40812655c9c9ace9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DvXCaIjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.org/images/core/emoji/11/72x72/1f642.png) 收购

另一方面，与吉拉相比，Visual Studio Team Services 是一个熟悉的产品，并且易于使用。有一个被不同的特性(比如存储库，构建配置等等)所困扰的风险，这是我不需要的，但是因为它检查了我所有的框，我决定继续使用 VSTS。

## VSTS 配置

我试图保持事情非常简单，因为我只需要 VSTS 管理工作项目。

以下是我的配置:

*   没有存储库或构建配置的新 VSTS **项目**(恰当地命名为 ToDo)
*   **看板**流程，带有“新建/预览”VSTS 导航。
*   **故事板**管理我的待办事项
*   **泳道** / * *标签** 分类不同的工作类型，如办公室、个人等。
*   根据故事的复杂程度，每个故事有零个或多个(子)任务。

[![ToDoBoard](../Images/32a6b36e4d5018ba2d091b71122cb2e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tqRRplT6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/ankitvijay.net/wp-content/uploads/2018/09/Untitled.png%3Fresize%3D810%252C427%26ssl%3D1) 

<figcaption>**我的待办事宜**</figcaption>

[![SubTasks.png](../Images/82416936bef1ffda801331503b7de6fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xujLNrAl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/ankitvijay.net/wp-content/uploads/2018/09/SubTasks.png%3Fresize%3D504%252C345%26ssl%3D1) 

<figcaption>**【子】待办事项/故事下的任务**</figcaption>

我使用 VSTS 看板来维护我的待办事项清单已经有两个多月了，它极大地帮助了我总是在事情的顶端。

我使用 VSTS 的一个问题是，它没有一个好的移动应用程序来维护工作项目。此外，其移动网站不允许轻松移动工作项目。幸运的是，因为我的大部分时间都是在电脑屏幕前度过的，所以这不是什么大问题。如果在一天的某个特定时间有什么需要注意的事情，我仍然会用手机提醒。这种* *混合** 模式对我来说效果很好，至少目前如此。

希望这个建议能帮助你更好地管理你的任务。

帖子[使用 Visual Studio Team Services 管理我的待办事项](https://ankitvijay.net/2018/09/02/using-visual-studio-team-services-to-manage-my-todo/)最早出现在[嗨，我是 Ankit](https://ankitvijay.net) 。
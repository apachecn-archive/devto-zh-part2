# 使用 AWS Sumerian 渲染您的第一个 AR 对象(1/2)

> 原文：<https://dev.to/wandererstudio/rendering-your-first-ar-object-with-aws-sumerian-12-27po>

[![](../Images/21f3a6564d18ac741eccaf21da593fda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6wwpzk70--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lnva5n1wd6lmq9edzstr.png)

#### 什么是 AWS 苏美尔语？

随着我们看到苹果 ARKit 的推出，以及谷歌 ARCore 的最新发布，我们将看到全新一代的应用程序将增强现实融入到其功能堆栈中。

亚马逊忍不住加入了这场竞赛，它提供了一种不同的方式来允许开发者创建 AR 环境。虽然在目前的状态下，开发人员必须在各自的应用程序中创建场景(无论是 ARKit 还是 ARCore)，但 AWS 为我们提供了一个单独的发布选项，并直接从互联网上加载我们的场景。

这意味着，只要你愿意妥协，让你的 AR 体验依赖于互联网连接(这可能是一些人的一个相当大的障碍)，你就可以开发一个单一的 AR 场景，可以快速整合到原生 ARKit *和* ARCore 应用程序中。

在这一系列文章中，我们将了解如何开始使用 AR 应用程序，然后我们将了解如何将您自己的自定义实体添加到您的场景中，并利用状态管理和 AWS Sumerian 的脚本为它们添加一个交互层。

#### 要求

*   活跃的 [AWS 账户](https://aws.amazon.com/)

#### 入门

首先，前往你的 [AWS 控制台](https://console.aws.amazon.com/console/home)，在 **VR & AR** 部分，你会想要点击**亚马逊苏美尔**。这将提示一个新的标签启动，你应该被送到你的仪表板，现在我们准备开始！

[![](../Images/2e3510ead8bfb88836f02256c1630426.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eCxoXbrs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x5f18ivvjg61ukiyag1l.png)

##### 1。创建您的项目

现在我们已经准备好了，注意你的屏幕是如何被分成 3 个主要部分的。在你的左边是你的导航菜单，在那里你可以创建新的项目来管理它们，在中间你会注意到一个大的**创建新场景**按钮，但是我们先不要点击那个按钮！在右边的部分，你有一个侧边栏，可以看到你在其他部分选择的信息

我们想从创建一个新项目开始，在这个项目中，我们将处理教程系列中的所有场景和实体。我们将通过以下方式做到这一点:

1.  点击我们导航菜单上的**项目**选项
2.  点击**新建项目**
3.  输入**实践苏美尔工程**，或者任何你内心渴望的东西
4.  点击**创建**按钮

**重要提示:为了能够将 3D 资产导入到您的 AR 场景中，它们需要成为同一个项目的一部分**

##### 2。创建您的场景

现在我们已经建立了一个项目，我们将想要创建以下场景:

*   要在 AR 中呈现的 3D 实体
*   AR 场景本身

###### 3D 实体场景

1.  首先回到你仪表板的**主页**
2.  点击**创建新场景**。AWS 苏美尔人会提示您输入一个名字，所以我们将输入`example_entity`
3.  点击**创建**

如果一切按预期进行，我们现在将被发送到编辑器屏幕，其中有一个空场景。

但是等等，还有呢！

在苏美尔人的当前版本中，你不能在一个项目创建的时候就给它添加场景。为此，您必须执行以下操作:

1.  点击左上角的**场景**下拉菜单，然后点击**退出仪表盘，返回你的仪表盘...**。
2.  点击你刚刚创建的场景(确保不要点击它的底部，因为你会直接进入编辑器屏幕)
3.  在右边栏，查看**动作**部分，选择**移动**选项
4.  点击你刚刚创建的项目，点击**移动**

唷！
现在是为 AR 设置项目的最后一部分！

###### AR 场景

我们将使用以下步骤创建 AR 场景:

1.  在你的仪表板上转到**主页**，向下看中间的**从模板**创建场景部分。
2.  你会注意到我们已经准备好了一个增强现实模板。点击它，为你的场景输入一个名字，然后**创建**它。
3.  就像上一步一样，你可以回到仪表盘，将这个场景移动到你的项目中

#### 包装完毕

如果您已经完成了上述所有事项，您应该:

1.  一个练习项目，您可以在其中管理所有场景和资源。
2.  您将创建 3D 实体的一个场景
3.  一个 AR 场景，我们将在其中导入 3D 资产

你现在可以进入本教程的第二部分了，在这里，我们将回顾如何创建 3D 实体，并使用 AWS Sumerian 的脚本将它们编程添加到 AR 场景中。

#### 奖金信息

*   AWS 苏美尔社区在懈怠:[https://amazonsumerian.slack.com](https://amazonsumerian.slack.com)
*   AWS 苏美尔语教程的官方链接:[https://docs.sumerian.amazonaws.com/](https://docs.sumerian.amazonaws.com/)
*   如何使用苏美尔语的 Twitch 教程:[https://www.twitch.tv/jsmeester/videos/all](https://www.twitch.tv/jsmeester/videos/all)
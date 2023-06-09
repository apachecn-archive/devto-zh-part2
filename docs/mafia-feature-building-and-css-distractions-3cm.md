# 黑手党:功能建设和 CSS 分心

> 原文：<https://dev.to/christianhoward/mafia-feature-building-and-css-distractions-3cm>

在我关于黑手党项目的第一篇文章中，我概述了一些想法和计划。从那以后，我执行了其中的一些想法，却被其他想法分散了注意力。

# 规划

[![Scrum Board](img/124dbea0a1fb1c377f9a2fa6d6c60ec7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lnDD9nb1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ipmw9wmudfcygl53qzrc.png)

首先，我跳进 Trello，建立一个 scrum 板来跟踪我构建的各个部分。我试着把我的项目分解成后端任务、前端任务、数据库任务和部署任务。在很大程度上，大多数事情都属于这个项目的前端部分，因为大部分推动器逻辑都属于这个区域。

# 初步进展

在我的沙盒回购中使用 Pusher 之后，我选择开始一个新的回购，并移动我编写的代码片段，以完成我在这个项目中想要的一些事情。一切就绪后，我开始改进聊天，增加了风格和管理信息，当玩家加入游戏、离开游戏或从游戏中被淘汰时，就会公布这些信息。希望一旦游戏开始，玩家会发现这很有帮助。

之后，我着手构建一个`PlayerPanel`组件来容纳游戏中的所有玩家。这花费了比预期更长的时间，因为当你或其他玩家加入房间时，我必须想出如何正确地更新玩家数组。一旦我明白了这一点，就一帆风顺了。我可以写一些 CSS 来管理一个球员图标被淘汰后的样子，我认为它看起来非常漂亮。接下来，我开始研究`ActionsPanel`的特性。

# 特色建筑

功能构建是构建 web 应用程序最好的部分之一，因为这是项目的主要部分！这也是最难自己测试的事情之一。最初，我构建了一个通用的`Button`组件，它可以接受道具并可重用(这是我从未在任何 React 项目中做过的事情)。这导致了大量的重构和探索以及兔子洞。如果离截止日期更近的话，我可能会偷工减料，但我很高兴它发生在我项目的这个点上。

在那之后，我致力于构建部分消除功能来测试我的消除 css，然后开始每轮的`Timer`组件。因为所有回合和部分回合都是计时的，所以玩家知道自己有多长时间是很重要的。这非常简单，因为我在之前的教程项目中已经构建了一个倒计时器。

接下来，一旦游戏开始，我就开始给玩家随机分配角色，并为这些角色构建`DoctorsVote`和`DetectiveVote`组件。`DetectiveVote`组件背后的一般思想将适用于其他一些`Vote`组件，因此成功完成该组件背后的逻辑将加速其他组件的完成。

我的许多测试和试验都是通过将这些特性加载到动作面板中，然后进行试验。一旦我有了`playGame`函数，组件将在需要时移入和移出`ActionsPanel`。这是一个大概的样子(注意还有一堆 css 在玩):

[![Sneak Peak](img/b164ecbdf9e9d524dc64c6594b95d727.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vw-DjHL8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dvegwzrstnpmc87m8qxo.png)

请注意我在右侧的`ActionsPanel`中测试的所有随机动作😂

# 明白了

我遇到的一个大问题是我的`Button`组件中的 disabled 字段。我不知道如何在早期正确地将真/假值传递给它，这样一旦玩家提交了投票，他们就不能更改它。一旦我弄清楚了数据在组件中的正确流动，一切似乎都变得井井有条了。

此外，确保您的路线前面有`/`。我花了半个小时试图调试一个 404 问题才发现路线 lol 开头的`/`不见了。

# 向前移动

接下来的步骤是继续游戏架构的工作，希望在事情完成之前不要被 css 分心。接下来几天的想法是“现在构建特性，以后再做”。我知道这很模糊。如果任何人有问题或评论，我很乐意提供更多细节！
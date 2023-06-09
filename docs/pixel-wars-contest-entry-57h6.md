# 像素战争:参赛作品

> 原文：<https://dev.to/avalander/pixel-wars-contest-entry-57h6>

# 我造了什么

我受 [r/Place](https://en.wikipedia.org/wiki/Place_(Reddit)) 的启发，打造了一款多人浏览器游戏。游戏的目标是控制大部分棋盘。棋盘(目前)有 625 个格子，玩家可以通过点击任何一个格子来获得它。

如果你关注了我的博客，你会注意到我最终没有给声明单元添加任何限制。玩家可以随心所欲地要求细胞，而不必等待其他玩家或受到任何其他方式的限制。

还有，没有赢的条件。计划是游戏将永远运行下去(或者直到我决定把它关闭)。也许这不会成为今年最令人兴奋的游戏，但它足够有趣，可以和 Pusher 一起玩。

## 演示链接

[游戏可以在这里玩](https://projects.avalander.com/)。

## 链接到代码

该代码可在 [GitHub](https://github.com/Avalander/pixel-wars) 上获得。

# 我是如何建造的

我用 [Elm](https://elm-lang.org) 做前端。我以前尝试过，但是我还没有用它建立任何大的东西，我还在学习，所以加深我的语言知识也很棒。有一件事我以前没有遇到过，但我必须为这个项目解决，那就是弄清楚如何让 Elm 与 Javascript 库交互。结果比我想象的要简单，可能是因为 Elm 的 Javascript 互操作性和 Pusher API 都是围绕订阅和传递消息而设计的。

后端是使用 node.js 构建的。我想尝试将 HTTP 请求捕获到流中，我将映射并简化这些流以构建游戏状态，并使用侦听器通过 Pusher 发送消息。我花了几个小时考虑这个概念，但是以一种干净的方式解决它被证明是很重要的，所以由于时间不够，我放弃了这个想法。

查看下面的附加资源以获取更多信息！

# 我是如何使用推杆的

这个游戏的要点是每个玩家都需要实时看到棋盘的更新。因此，每当一个玩家要求一个单元时，一个消息通过 Pusher 发送给所有连接的玩家，并带有更新的单元。

我实现了一个排行榜，计算每个玩家当前拥有多少个单元格，显示得分最高的十个玩家。排行榜的更新也是通过 Pusher 实现的。

没有必要实时更新排行榜，它可以每隔几秒甚至几分钟更新一次。因此，我想把它从更新电路板单元中分离出来。然而，由于我不期望有太多的流量，所以我不需要廉价地使用推送消息，而且每当有用户认领一个手机时，我都会发送更新。

# 附加资源/信息

我写了一些记录我的过程和发现的帖子。你可以在这里查看。

[设计](https://dev.to/avalander/pixel-wars-design-358p)
[在榆树中使用推动器](https://dev.to/avalander/pixel-wars-using-pusher-in-elm-4ifb)
[认领单元格](https://dev.to/avalander/pixel-wars-claim-cells-191g)

总的来说，我很高兴我能提交一些东西。这很有趣，也很好玩，但是生活阻碍了我，我不能投入我想要的那么多时间(我想这是大多数人的情况，:P)。

那都是乡亲们！请，给游戏一个尝试，并欢迎任何评论。
[![](img/cd2c9037ccaea659b814b3f31dfe5c33.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--MxzbNTXf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encrypted-tbn0.gstatic.com/images%3Fq%3Dtbn:ANd9GcSjiXIvWj_xAtA9q6Lr_VH9TD0gEzXR-1DoY4pacmwImQkbqv9-_Q)
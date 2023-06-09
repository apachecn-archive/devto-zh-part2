# 1000 颗星星下的牛逼项目(2) - Gridland

> 原文：<https://dev.to/gmartigny/awesome-projects-under-1000-stars-2---gridland-398c>

# [格里兰](https://github.com/doublespeakgames/gridland)

这个月，我想做一些不同的事情。我的意思是，我从来没有说过这个帖子必须是严肃的，毕竟那是我的帖子。所以这一次，我向你们展示了 GridLand。一个非常上瘾和有趣的消磨时间的游戏。

[![Quiet start](img/a1b1aaac39627343832a8fbc14d1a157.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7pRwoyKT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qm33nqtwk6hs51g783jo.png)

围绕一个简单的“比赛 3”游戏，Gridland 允许你建造和升级建筑，以提高你的能力。每次你匹配足够多的方块，你就会赢得一笔新的资源，可以花在你的营地上。有了足够的资源，你这个小神通就会壮大你的阵营。

但不要搭配松散，的确，每一个动作都让时间流逝一点，当夜晚来临...像大多数游戏一样...怪物被释放了。到了晚上，你的资源网格变成了怪物和防御网格。
游戏玩法也有点变化。小心翼翼的“最后一天尽可能多的比赛”变成了疯狂的“给我一把该死的剑，而不是生出几十个怪物！".

[![yeah, I know there's a sword match on the left ...](img/4b8b9f9cf2a2a4e867f0982a218fea34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ovxZhwIM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3whgsbbp6zmg9ly5lcri.gif)

当然，如果我今天展示这个，不仅仅是因为这个游戏很有趣。是因为整个游戏的代码都在 github 上开源了。像许多项目一样，文档资料严重匮乏🙄。然而，代码的格式和架构都非常好。你可以找到(并从中学习)每一个游戏开发的传统部分:事件管理器、游戏状态保存、本地化、音频、实体...
这都是基本的 Javascript，所以即使你不是开发高手，你也能理解并从代码中挑出好的部分。

在深入研究代码之前，先尝试一下。首先它会减少剧透。然后你会有一个关于游戏如何工作和每个文件的想法。

以下是一些建议:

*   匹配 3 个以上的瓷砖有很大的好处，尽量多做
*   不要用魔法把夜晚变成白天，你需要战斗的经验
*   你可以通过点击它们来区分建筑的优先级

和一些开发技巧:

*   事件管理器很简单，但是非常有用
*   [gamestate.js](https://github.com/doublespeakgames/gridland/blob/master/www/js/app/gamestate.js) 文件处理保存，这很难做对
*   查一下 [Require.js](https://requirejs.org/) 那些奇怪的`define(['...', '...'], function(`

下个月再见。

DoubleSpeak 工作室的下一款游戏名为“ [a dark room](https://github.com/doublespeakgames/adarkroom) ”非常惊艳。也试试吧。
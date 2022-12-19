# 亚马逊贮木场:痛苦的尖叫

> 原文：<https://dev.to/alexastva/amazon-lumberyard-a-scream-of-anguish-230e>

视频游戏是最受欢迎的软件产品之一。现在，一个新的游戏引擎亚马逊木材场(Amazon Lumberyard)加入了这个庞大的行业。该项目目前处于测试阶段，仍然有时间来消除错误和改进。它的作者有很多工作要做，以确保他们不会在不久的将来让数百万游戏玩家和游戏开发者失望。

[![Amazon Lumberyard copy-past bugs](img/d632ca9a8fb4821c3d7eb493e0ef426f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wqfAsuVh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lt0lpyqa05bgr6jo9fzi.png)

[亚马逊伐木场](https://aws.amazon.com/lumberyard/)是亚马逊开发的一款免费的跨平台三 A 游戏引擎，基于 [CryEngine](https://www.cryengine.com/) 的架构，于 2015 年从 Crytek 获得授权。我已经检查了 CryEngine 两次，分别是在【2016 年 8 月和【2017 年 4 月，很抱歉这么说，但代码质量从第一次检查开始就下降了。前几天，我想知道亚马逊是如何使用该引擎的，并看了一下新产品。我必须承认他们确实创造了一个很棒的环境。用户文档和环境部署软件也非常棒。但是代码又乱了！我希望亚马逊能够为这个项目分配更多的资源，并最终开始关心代码质量。通过写这篇评论，我希望引起他们对这个问题的注意，并说服他们对开发过程采取一种新的方法。

分析中的源文件指的是最新的亚马逊木材场版本，1.14.0.1，是从 [Github](https://github.com/aws/lumberyard/) 库下载的。[星际公民](https://robertsspaceindustries.com/)是第一个建立在木材场引擎上的游戏。如果你想玩它，那么欢迎和我一起来一探究竟。

更多阅读-[https://www.viva64.com/en/b/0574/](https://www.viva64.com/en/b/0574/)
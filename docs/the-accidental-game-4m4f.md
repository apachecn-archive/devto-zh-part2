# 偶然的游戏

> 原文：<https://dev.to/izandotnet/the-accidental-game-4m4f>

大家好。这是我的第一个帖子。所以，请原谅我。这里有一个我如何制作一个游戏并在苹果商店上发布的故事。

TL；博士
[陀螺蛇 App store](https://itunes.apple.com/us/app/gyro-snake/id1244436888?ls=1&mt=8)
[Github 上的源代码](https://github.com/izandotnet/ULAQ)

这要追溯到一年前，当时我正在 App Store 上寻找可以发布的东西。一开始，我有了跟踪每月支出的想法，并利用浮动操作按钮(FAB)来记忆最频繁的支出类型。当时我在玩 Swift，开始研究如何在 Swift 上做 FAB。当时，我对构建移动应用程序的混合框架一无所知，不知何故，StackOverflow 的一个答案让我开始研究苹果精神。

对我来说这是件新鲜事。一个游戏引擎，内置物理和其他很酷的东西。然后它把我带到了 Youtube 上一个关于如何制作一个简单乒乓球游戏的视频。我成功地遵循了教程，我认为如果我能使用陀螺仪控制球会很酷。这个时候，我已经打消了当初做一个金钱追踪器的想法，决定做一个疯狂的蛇形游戏。

## 起源

这个游戏的创意来源于在老一辈人中很出名的游戏。在创作这个游戏的过程中，我回忆起了以前整天玩这个游戏的时光。通过重新制作游戏，使它只能通过倾斜你的智能手机来玩，它很可能会吸引许多人来玩。

## 逻辑

背后的逻辑其实很简单。我创建了一个 Snake 对象，它有一个 head 和 body 类型的子对象，驻留在一个数组中。还记得我之前提到的 Pong 游戏吗？是的，我让它变成了黑鱼。因为头部的运动已经被陀螺仪控制了，所以，那部分已经完成了。身体将根据它的前身对象移动，该对象在数组[n-1]中，当你倾斜手机时，它会做出蛇形运动。每当蛇击中一个苹果，就有一个新的蛇身被推到数组中。因此，当更多的蛇体被推到阵列时，控制蛇变得越来越有挑战性。

如果你一直读到这里，你是幸运的。这里是[链接](http://rohaizanroosley.com/2018/06/09/eid-special/)到促销代码。

## 最难的部分

我想开发这个游戏最难的部分是让蛇头看向它前进的方向。幸运的是，我通过使用下面的代码找到了一种算法。

```
action1 = SKAction.moveTo(x: destX, duration: snake.snakeSpeed)
action2 = SKAction.moveTo(y: destY, duration: snake.snakeSpeed)
v1 = CGVector(dx:0, dy:1)
v2 = CGVector(dx:destX - snake.snakeHead.position.x, dy: destY - snake.snakeHead.position.y)
angle = atan2(v2.dy, v2.dx) - atan2(v1.dy, v1.dx)
body.zRotation = angle
body.run(action1)
body.run(action2) 
```

Enter fullscreen mode Exit fullscreen mode

## 图形

好吧，我不是图形专家，所以我只是打捞我在谷歌图像中找到的任何与青蛇有关的东西。通过一些小技巧，我成功地做到了。

## 声音特效——这部分很有趣

我决定给这个游戏使用旧的 8 位 SoundFX，这是我的 Youtube 关键词。我找到了很多样本。[这里可以看到](https://www.youtube.com/results?search_query=8-bit+soundfx)。为了从 Youtube 上下载声音，我不确定这是否合法，我使用了一个叫做 [youtube-dl](https://rg3.github.io/youtube-dl/) 的小工具。

## 不是钱的问题

我希望你喜欢读这篇文章。直到今天，还没有人能掌握这个游戏并进入决赛。让我知道你是否能到达 21 级。:)
# 初创公司 CliX:拥有私人游戏和移动用户界面的 RC1

> 原文：<https://dev.to/kayis/startup-clix-rc1-with-private-games--mobile-ui-1hmd>

明天我将去参加一个[节日](http://www.wave-gotik-treffen.de/english/info/info.php)，所以我试图在今天准备好第一个可玩的版本。如果我没有在周末醉酒而死，我会在最终提交之前再多润色一点，修正一些错误。

它并不漂亮，但至少还能用...我认为:D

## 已知的臭虫

*   后端在计算出你的新资本后发送`round:end`，这可能导致销售额在`round:end`事件到达客户端之前到达，但不再计入后端。

代号:[https://github.com/kay-is/startup-clix](https://github.com/kay-is/startup-clix)T2】演示:[https://kay-is.github.io/startup-clix/](https://kay-is.github.io/startup-clix/)

## 私人游戏

如果你一直在加入随机游戏，尝试测试游戏并不容易。所以我加了一个私密模式。

你输入你的名字和最大玩家数量，开始一个私人游戏，然后你可以和你的朋友分享一个链接。

如果达到你的最大玩家数量，游戏开始。

## 游戏玩法

每个人得到 3 万美元资本。

你等着第一轮，尽可能快地点击，运送你的产品。

你的产品的价值是点击数/提交前的时间，所以每秒更多的点击数会产生更好的结果，但是过早发布会导致没有足够的点击数等等。

一轮结束时，每个人都会因为烧钱率损失 5000 美元。

如果有人失去了所有的资本，他们将被从游戏中移除。

如果只剩下一个玩家，他们就赢了这场游戏。

## 手机 UI

改变了一些宽度和球员列表的显示，现在它也可以在手机上运行了。

## 建筑

我做了一个建筑的图形。它少了一个我最近加的新功能，不过总体来说我觉得是对的。

[![architecture](img/641d20583b217da0d813100238708886.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rBhjQQJO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b183hlqe7rha4xtgs0d7.jpg)

## 接下来

当我下周有时间的时候，我想用一个像火绒一样的刷卡来代替这些按钮。所以你可以点击点击和滑动来销售。

此外，一些用户界面的变化可以更好地沟通，当一轮结束或开始时，你会看到，但资本的变化不是很突出。

游戏的结尾也是目前比较...虎头蛇尾:/

等你的报告，万岁~ XD

-编辑-

添加了一些音效。
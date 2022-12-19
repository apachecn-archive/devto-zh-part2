# 开发竞赛参赛作品- Tweng！

> 原文：<https://dev.to/markuswillems/dev-contest-entry---tweng-223n>

# 我造了什么

*Tweng！*，两个玩家之间的回合制卡牌游戏，每个玩家控制一支军队，用来在战场上消灭对方！

*Tweng！*基本上是来自 [*《巫师 3:疯狂猎杀*](https://en.wikipedia.org/wiki/Gwent:_The_Witcher_Card_Game) :D 的 *Gwent* 的一个糟糕且有缺陷的克隆

[![Tweng!](../Images/3bf250077d5da650f5d13a438aa807af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2dll-TIF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/df3h0d338dt2eovpywtf.png)

## 演示链接

[http://chemical-dock . surge . sh](http://chemical-dock.surge.sh)

## 链接到代码

客户端:[https://github.com/markus-willems/tweng](https://github.com/markus-willems/tweng)T2 服务器:[https://github.com/markus-willems/tweng-server](https://github.com/markus-willems/tweng-server)

# 我是怎么建的(栈是什么？在这个过程中，我遇到了问题或发现了新的东西吗？)

## 堆栈

我必须创建一个客户端和一个服务器。对于这两者，我或多或少使用了不同的堆栈。两者当然都使用推动器。

### 客户端堆栈

我用 React 创建的 UI。为了构建和捆绑，我使用了 Webpack 和其他一些很酷的库和工具，比如 Babel。更多信息请查看 package.json。客户端托管在[浪涌](http://surge.sh/)。

### 服务器堆栈

对于服务器，我使用了 Express 和 PostgreSQL。使用 Express，我创建了一些 API 端点，并使用 PostgreSQL 来保存一些匹配数据。更多信息请查看 package.json。服务器托管在 [OpenShift](https://openshift.com) 。

## 问题

CSS 和 UI 设计很难。向掌握它的人致敬！我想添加更多的测试/让它更健壮，并重构代码，因为有许多较小的黑客和重复的代码，但这样我就不会有一个工作演示，直到最后期限。

## 吸取教训

在未来，我可能应该计划更多。有一天，我坐下来，没有一个合适的计划就开始了黑客活动。我脑子里已经有了计划——或者至少是我试图建立的一个粗略的想法。但是，孩子，周密的计划是非常重要的。

# 附加资源/信息

## 怎么玩 Tweng！

1.)访问[http://chemical-dock . surge . sh](http://chemical-dock.surge.sh)
2。)创建比赛/频道，并通过 link
3 邀请朋友。)轮流互相战斗，直到有人失去 2 条生命

### 快速启动

每个玩家在一场比赛中有 10 张牌。一场比赛至少包括两轮。当双方都通过这一轮时，这一轮就结束了。军队的总实力决定了谁赢了这一轮。力量较小的玩家将失去一条生命。如果两个玩家实力相同，那么两人都将失去一条生命。当另一个玩家失去两条生命时，谁至少还有一条生命，谁就是赢家。

要出牌，请选择该牌并单击▲图标。当一张牌在一轮中被打出，它在下一轮就不可用了。因此，如果你想赢的话，不要在第一轮就把所有的牌都打出来了。放聪明点。

### 卡片

#### 前线

前线牌是显示为剑的牌。前线卡牌受到*冰霜*卡牌/法术的影响——力量降为 1。

#### 中档

中档牌是显示为弓箭的牌。中档卡牌受到*迷雾*卡牌/法术的影响——它们的力量减少到 1。

#### 长距离

远程卡牌是显示为投石机的卡牌(可以发射 90 公斤的炮弹超过 300 米)。远程卡受到*雨*卡/法术的影响——它们的力量减少到 1。

#### 霜、雾、雨

降低牌的力度。看上面哪个法术影响什么牌。

#### 阳光

去除霜、雾和雨。法术将不再起任何作用。牌又有了正常的力度。

## 对未来的计划

我有很多有趣的黑客行为，并希望基于我在开发过程中获得的经验来创建一个重写。这将包括更好的规划、更好的架构，当然还有更好的 UI。
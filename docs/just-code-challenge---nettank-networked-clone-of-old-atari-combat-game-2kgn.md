# 公正的代码挑战- NetTank 网络克隆旧雅达利战斗游戏

> 原文：<https://dev.to/lefebvre/just-code-challenge---nettank-networked-clone-of-old-atari-combat-game-2kgn>

在#JustCode 挑战赛的第 10 周，我研究了网络。在我的项目中，这次我创造了一个战斗游戏的网络版本，在屏幕上有两辆坦克互相射击。网络版可以让你在电脑上运行应用程序来控制一个坦克，让其他人在电脑上运行应用程序来控制另一个坦克。我称之为 NetTank。

不久前我用 Xojo 制作了一个战斗版本(包含在下载的 Xojo examples 文件夹中)，所以我想我应该从那里开始。为了给游戏提供网络功能，你通常想要快速的东西，这意味着 UDP(用户数据报协议)，它是由 UDPSocket 提供的。Xojo 中还包含了一个 UDPSocket 示例，我将其用作参考。

开始时，我首先决定要来回发送什么数据。我把范围缩小到这些行为:

*   共同诉讼
*   移动动作
*   双作用

这些都作为 JSON 通过广播发送到一个多播组。

**JoinAction** 在游戏首次开始时发送，允许两个客户端 NetTank 应用协商谁是玩家 1，谁是玩家 2。这两个动作是“就绪”和“播放器 2”。

包含你的坦克刚刚发送给另一个客户端的动作，这样它就可以更新它的显示。最初，我只是发送“向前”、“向左”、“向右”和“开火”的动作。但是因为 UDP 不能保证消息被接收，当网络拥塞时，如果大量的移动被快速发送，坦克有时会失去同步。因此，我决定发送实际的 xy 坐标和旋转角度来向前移动，这样，如果消息丢失，它至少会在未来的向前消息中有正确的信息。

当一辆坦克被击中时， **HitAction** 包含该坦克的新位置(当被击中时，它被移动到屏幕上的一个随机点)以发送给其他客户端。

因为战斗游戏已经使用游戏处理和图形更新的计时器运行，所以我真的不需要为 NetTank 修改太多原来的战斗游戏代码。我在主战斗窗口中添加了一个 UDPSocket，在它的 DataAvailable 事件中，我有代码来检查消息并根据需要更新坦克对象。

我还必须对主要代码进行一些调整，以发送关于移动和命中的 JSON 消息，并正确识别要移动的坦克。

要尝试这个游戏，你需要在两台不同的电脑上运行它。第一个开始的是 1 号玩家(蓝色)，第二个是 2 号玩家(红色)。使用箭头键移动坦克，并按空格键发射。

[![](../Images/b541bd05456de4d901e22bbdb6ccfeea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--37_aOWnd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/08/2018-08-22_14-05-40.png)

在 GitLab 上查看[项目。](https://gitlab.com/xojo/NetTank)

将您的 Just Code 项目添加到[第 10 周论坛对话](https://forum.xojo.com/49555-just-code-challenge-week-10-projects)中。
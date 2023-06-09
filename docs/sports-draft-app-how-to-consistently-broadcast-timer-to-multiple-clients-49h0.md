# 体育选秀 app:如何向多个客户端一致播放定时器？

> 原文：<https://dev.to/allison_seboldt/sports-draft-app-how-to-consistently-broadcast-timer-to-multiple-clients-49h0>

在创作我的幻想体育衍生游戏《幻想大会》的时候，我认为提供一个现场直播的选秀是很重要的。我实现了这一点，但它需要改进。

我遇到的最大问题之一是向所有客户端广播一个一致的倒计时定时器。每个人都有一分钟的时间来做出选择。所有连接的客户端都需要知道每个回合还剩多少时间。

目前的应用程序使用 websockets(通过 socket.io)。每秒钟都会向所有连接的客户端广播一次计时器更新。然而，有时计时器可能会暂停，然后突然提前 10 秒跳到原来的位置。我的假设是，当某人的连接速度很慢时，就会发生这种情况。

我在考虑在每个浏览器中运行单独的定时器来避免这种跳跃。但是转弯变化仍然需要以某种方式进行沟通。假设最好的方法仍然是通过 websocket，一些人可能会在其他人之前收到话轮转换。这意味着他们可能会认为还没轮到他们，或者认为轮到别人了，而实际上轮到他们已经 5 秒了。

你会如何处理这样的事情？
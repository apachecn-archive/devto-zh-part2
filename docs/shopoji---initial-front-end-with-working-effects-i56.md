# Shopoji -具有工作效果的初始前端！

> 原文：<https://dev.to/itsasine/shopoji---initial-front-end-with-working-effects-i56>

# 最新消息

[![Initial screen when you join Shopoji](../Images/4f4dbf7956f589d2a410eb617bd6d601.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y7FN6iTS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d51eyo130nhi2a8yxxn3.png) 
*你能分辨出我不在 UX 吗？*

目前没什么特别的，但是前端不只是一个有一些控制台日志的白页。

但是！当一个新的玩家加入市场时，会发生这样的情况:

[![A new shop has entered the market notification](../Images/50a250feb64f6c4c55c009e4e7ef92e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mBZ5oB71--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qjfmpqk5j76b830zc6ot.png)

有一个小横幅，上面写着一家新店进入市场，另附 10 个新的🍏已经进入市场。如果另一家商店加入，再加 10 家🍏都是加进去的，等等等等。

# 什么都完了

*   我再也不碰 AWS 了(因为它是为我所需要的而做的)
*   我掌握了推杆的工作原理。这很简单。向通道发射事件。频道里的人得到事件的东西。嘣，搞定。
*   开始有一个关于当前市场状态的主表的想法(假装你每天早上在报纸的表情符号经济部分读到它)
*   我有一个带着这个“MVP”去哪里的想法
*   我也知道以后我想在哪里扩展。存在频道会让我的想法变得很酷，但是我不认为一个最小的可行演示现在需要这个。我喜欢这是一个真正自由的市场的想法，而不是锁在一个授权页面后面😛

# 接下来是什么

1.  让消息区更有用一点，这样以后可以添加更多的事件
2.  加入销售的概念🍏美国，从市场上移除它们
3.  如果价格太高或太低，东西就卖不出去(如果你在市场上降价那么多，苹果一定是坏的！)

# 还剩什么

*   充实全球市场部分，以拥有更多表情符号、更多类型的表情符号，而不是看起来像垃圾，等等
*   添加向客户(NPC)销售表情符号并为您的表情符号设定价格
*   如果时间允许，添加销售物品的偏好。一个骑士可能会花更多的钱买一把🗡️，但不会愿意为一把🎈

* * *

此外，目前这种架构的设计存在相当大的疏漏。我完全打算让它成为一个“特性”而不是一个 bug🐛跟随“新特性”部分可能有助于展示它😇
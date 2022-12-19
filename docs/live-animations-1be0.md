# 实时动画

> 原文：<https://dev.to/pekosog/live-animations-1be0>

[![some screenshot](../Images/dd7d4b89def84dbb807917bd74ccc57c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wf1LiOjW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pb7d91kshy8tlcfwkvb6.png)

你好。嗯，我在做一些在看了[这个视频](https://www.youtube.com/watch?v=6ucTdeRcr0k)后想到的随意的东西。

我的目标是:

*   在任何浏览器上打开链接(客户端 A)
*   开始渲染默认动画
*   获得“房间”id/钥匙
*   在另一个浏览器中打开链接，例如电话(客户端 B)
*   记下“房间号/钥匙”
*   一旦房间通过验证，显示一些控件来改变颜色，速度，动画，无论我想出的设置。
*   ？？？
*   利润！

我的架构计划大概是这样的:
[![Architecture Diagram](../Images/edb477382554c8837e820afcc64e49f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QGP6DljN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m65v89sh12a79841yncy.png)

目前我只有 JS Animation + Animation change 事件的一个简单例子和 POC(基于一个输入事件监听器)。

你可以在这里尝试一下

现在只有两个动画:

*   启动(这是默认设置)
*   滴剂

您可以通过按 **S** 开始和 **D** 下降来改变它们。

如果你想跟踪进展，参与进来或者发布一些功能请求[这是我的 github 报告](https://github.com/pekosoG/live-graphics)
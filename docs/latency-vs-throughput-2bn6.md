# 延迟与吞吐量

> 原文：<https://dev.to/nbosco/latency-vs-throughput-2bn6>

[![From CMU Parallel Computer Architecture and Programming Course](../Images/1488ab613be51ae7785b02847b46a796.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6eUOWk3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qt7fwxo5kvboxuhzjmlb.jpeg)

延迟:事件经过的时间。

从 A 点走到 B 点需要一分钟，等待时间是一分钟。

吞吐量:单位时间内可以执行的事件数量。

带宽是吞吐量的量度。

我们可以增加带宽来提高吞吐量，但不会改善延迟。

以 RPC 为例——分布式系统中的消息通信延迟有两个组成部分，第一个组成部分是硬件开销，第二个组成部分是软件开销。

硬件开销取决于网络与计算机的接口方式，这主要由网络控制器来管理。

我们如何减少软件开销？这是每个产品所有者需要问自己的问题，以改善他们的产品。

如果你喜欢这个，你会喜欢我的其他帖子！
原帖在此:[https://medium . com/@ nbo SCO/latency-vs-throughput-d7a 4459 b5 CDB](https://medium.com/@nbosco/latency-vs-throughput-d7a4459b5cdb)
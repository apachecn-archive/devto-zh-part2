# 用 Guzzle 构建 HTTP 客户端

> 原文：<https://dev.to/titasgailius/building-http-clients-withguzzle-d5e>

每当您集成一些公开 HTTP API 的外部服务时，您将最终编写一个客户端来与该服务进行交互。

有许多不同的方法来构建这些 http 客户端，尽管下面列出了我遇到的 3 种最常见的方法。

**1。创建一个扩展 guzzle 的客户端类。**
[![extends guzzle](img/f1833684879b293d62e4fdcf4f559a2b.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--3iZgnOjc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uhg145ur34u0a2cdqq9n.png)

**2。创建一个处理所有逻辑和调用 guzzle 的客户端类。**
[![extends guzzle](img/738539902915594371136b96a5d01ea4.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--wPhHvJvM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5wa2kr0owxqzeaacasnb.png)

**3。创建一个客户端类，其中所有的逻辑都由 guzzle 中间件处理。**
[![extends guzzle](img/04c61606e7da41a40f97b1f9b91331b2.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--LNKixM7N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f5ddo3lmx3vp0ao8oaxr.png)

# 你更喜欢哪个？你有其他选择吗？讨论一下。
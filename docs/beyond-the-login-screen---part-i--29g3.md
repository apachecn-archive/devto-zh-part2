# 登录屏幕之外-第一部分

> 原文：<https://dev.to/shyamala_u/beyond-the-login-screen---part-i--29g3>

最近我看到了这条推特，

> ![██ Oliver █ L██████ profile image](img/94e4af3c3bef82a35df293db563b9e4f.png)██·奥利弗·█l██████@ ee y0 re![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)如果你在收集个人数据，“我该如何保护这些数据？”实际上是你的第三个问题。
> 
> “我该不该收这个？”只是第二个问题。
> 
> 第一个问题是“最坏的人如果得到这个会做什么？”2018 年 03 月 04 日上午 03:49[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=970144255745212416)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=970144255745212416)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=970144255745212416)

这条推文让我想到，个人数据即使是多么微不足道，在错误的人手中也会很危险。但是我们能停止收集用户的数据吗？我们能肯定地说我们正在建立的系统是不可渗透的吗？

作为产品团队的一员，我们有责任对用户保持透明，让他们知道我们的产品需要哪些用户数据，以及为什么用户要分享这些数据，这样用户就会意识到这些数据被分享了。

让我们不要非常贪婪地要求用户一次提供所有数据。

[![](img/f5c0628b4af58b85c596288a98eed6b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--058NFlED--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/izfgmkdmn4w11rtz0ocj.gif)

我认为我们也可以应用精益原则从用户那里收集数据，只在我们需要的时候才询问用户的数据，当然还要解释是什么和为什么。

我们可以自己为我们的每一套产品设计一个独特的解决方案，或者我们可以抽象出收集一些最常见的用户数据的方法，这些数据是我们以安全可靠的方式收集的。

有许多用于在用户和服务/产品之间交换认证和授权数据的开放标准。其中最受欢迎的是 SAML、OAuth2 和 OpenId Connect。这些标准让我们相信，用户数据是以对用户透明的安全方式进行交换的。

让我们改变思维过程，使认证或授权不仅仅是一个登录屏幕。让我们在产品设计阶段的早期考虑这些方面。

因此，也许是时候让我们考虑这些联合身份验证和授权标准了，它们赋予用户在任何时候授予和撤销对其数据的访问权限的**权利？**

在下一部分中，我将深入研究这些开放标准之一 OpenID Connect..
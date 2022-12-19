# Chrome 不信任 SSL 证书

> 原文：<https://dev.to/funkysi1701/chrome-distrusts-ssl-certificates-2eme>

我一直在做的一个网站一直在控制台显示一个错误。错误如下所示。

```
The SSL certificate used to load resources from https://example.com will be distrusted in M70\. Once distrusted, users will be prevented from loading these resources. See https://g.co/chrome/symantecpkicerts for more information. 
```

Enter fullscreen mode Exit fullscreen mode

但这意味着什么呢？让我们从提供的[链接](https://security.googleblog.com/2017/09/chromes-plan-to-distrust-symantec.html)开始。

2017 年 1 月，据披露，赛门铁克运营的证书颁发机构(包括 Thawte、VeriSign、Equifax、GeoTrust 和 RapidSSL)一直在颁发不符合基准标准的证书。

从 Chrome 66 开始，谷歌决定取消对这些证书的信任。Chrome 66 将于 4 月 17 日左右发布。我的错误提到了 M70，那么它指的是什么？

将于 2018 年 10 月发布的 Chrome 70 将取消对另一批赛门铁克证书的信任。

如果你因为使用了一个不被信任的证书而出现这些错误，那么你的网站在 Chrome 66 或 Chrome 70 中会是什么样子呢？

Chrome 66 现在在开发频道，所以我们可以试一试。[![](../Images/0bf9ca037f5003e7cab7c8f1dad94521.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1tZwZnzu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/www.funkysi1701.com/wp-content/uploads/2018/02/tempsnip.png.jpg%3Fresize%3D662%252C443%26ssl%3D1)

对你的用户来说不是很好，是吗？现在是时候订购一个新的 SSL 证书，以避免这种情况发生在您的网站上。

几个月前我第一次看到这个错误，一直在阅读它，等待 Chrome 66 到达 dev 频道，这样我就可以测试它对我的网站做了什么。然而，现在我安装了 Chrome 66，我发现我工作的公司的内部网也受到了影响。我不直接在内联网上工作，所以我通知了安全团队，他们可能想调查此事。

不幸的是，我收到的回复是谷歌需要在 Chrome 66 发布前解决这个问题。我并不是在批评我的雇主或安全团队，然而这不是谷歌能够解决的事情。

颁发的证书是由有问题的 CA 颁发的，因此为了保持所有证书的可信度，Google 别无选择，只能不信任它们。谷歌和安全专家需要对此大惊小怪，而我也加入了大惊小怪的行列，写了这篇博客。Scott Helme 估计大约有 7000 个网站可能会受到 M66 和 M70 不信任的影响。
# 你在 HTTPS 做得对吗？

> 原文：<https://dev.to/ankitvijay/are-you-doing-https-right-2l64>

去年，互联网在网络安全方面达到了一个重要的里程碑。全世界超过一半的网站现在都在 HTTPS 上。随着 HTTPS 知名度的提高，越来越多的组织和个人将他们的网站转移到 HTTPS。像 [Lets Encrypt](https://letsencrypt.org/) 这样提供免费证书的认证机构使得迁移到 HTTPS 变得前所未有的容易。除此之外，像谷歌、Mozilla 这样的大公司也在努力争取 HTTPS。从 7 月份开始，Chrome 将把所有的 [HTTP 网站标记为不安全的](https://www.theverge.com/2018/2/8/16991254/chrome-not-secure-marked-http-encryption-ssl)。

虽然搬到 HTTPS 是保护你的网站的重要一步，但这只是许多重要步骤中的一步。HSTS 就是这样一个例子。想了解更多关于 HSTS 的信息，请参考我之前在 HSTS 的帖子。

许多人在移居 HTTPS 时面临的另一个常见问题是内容混杂。根据[谷歌开发者文档](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/what-is-mixed-content)

> **混合内容**当初始 HTML 通过安全的 HTTPS 连接加载，但其他资源(如图像、视频、样式表、脚本)通过不安全的 HTTP 连接加载时，就会出现混合内容。这被称为混合内容，因为 HTTP 和 HTTPS 内容都被加载以显示同一个页面，并且初始请求在 HTTPS 上是安全的。现代浏览器显示关于这类内容的警告，以向用户表明该页面包含不安全的资源。

## 网站上混杂的内容

如今有许多网站面临着内容混杂的问题。我最近偶然发现了一个这样的网站，[Jetstart.com](http://www.jetstar.com/au/en/home)。

在撰写本文时，捷星的登陆页面不在 HTTPS，这本身就是一个问题。即使当你进入像“**管理预订**”这样的页面时，需要你的电子邮件地址和预订参考等个人信息来查看你的预订，也是加密的，但有一点需要注意。该页面在 HTTP(或不安全的连接)上加载一些资源。

当你在 HTTPS 上加载一个网站时，所有主流浏览器都会在地址栏显示“安全”符号。比如你在 Chrome 浏览器上打开我的帖子，地址栏显示“安全”，如下图。

[![Secure-website.png](../Images/79be4a74a91f7d89037500aaab028050.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_nhe2Qly--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/ankitvijay.net/wp-content/uploads/2018/02/Secure-website.png%3Fresize%3D517%252C227%26ssl%3D1) 

<figcaption>安全符号(Chrome 浏览器)</figcaption>

安全符号还表示在网站上加载时请求的所有资源，无论是图像还是第三方脚本，都是 HTTPS。然而，如果你的网站加载混合内容，那么你的网站的用户将不会在地址栏看到可信的“安全”符号，即使该网站本身是在 HTTPS 上。以下是捷星“管理预订”页面在 Edge 浏览器上的外观示例:

[![](../Images/106a998eb0b0999cb60aa45e08210dd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6p98BsCZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/ankitvijay.net/wp-content/uploads/2018/02/InsureWebsiteWarning-1.png%3Fresize%3D681%252C396%26ssl%3D1) 

<figcaption>网站内容未加密警告(边缘浏览器)</figcaption>

更糟糕的是，捷星还会收集用户的信用卡信息。因此，当用户从浏览器上看到这样的警告时，他可能会对该品牌失去信任，并在购买前三思。

## 如何避免这种情况？

嗯，避免这个问题的一个最显而易见的方法是将你的所有内容通过 HTTPS 传送。不幸的是，这并不像听起来那么容易。特别是，如果你在你的网站上加载了数百个不同来源的资源。将所有资源迁移到 HTTPS，然后更新页面上的链接，这并不简单。除此之外，我们仍然无法控制我们可能在网站上使用的任何第三方库的内容。

这就是内容安全策略(CSP)**upgrade-unsecured-requests **可以提供帮助的地方。根据 [MDN 文件](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests):

> HTTP [`Content-Security-Policy`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) (CSP) `upgrade-insecure-requests`指令指示用户代理将站点的所有不安全 URL(通过 HTTP 提供的 URL)视为已被安全 URL(通过 HTTPS 提供的 URL)所替代。该指令适用于包含大量需要重写的不安全旧 URL 的网站。
> 
> `upgrade-insecure-requests`指令在 [`block-all-mixed-content`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) 之前评估，如果设置了，后者实际上是一个空操作。建议设置一个指令或另一个指令，但不能两个都设置。

要将其添加到您的网站，只需将下面的 meta 标签添加到您的 HTML 中

`<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">`

> **注意事项**当资源在 HTTPS 可用时，CSP:升级-不安全-请求只能在 HTTPS 上迁移请求的资源。然而，如果所请求的资源在 HTTPS 上实际上不可用，那么请求**将失败**而没有任何回退到 HTTP。

遗憾的是， **Edge **和**Safari** 浏览器目前仍不支持这一 CSP 指令。Chrome、Firefox 和 Opera 都支持这一指令。

希望这篇文章对你有用。编码快乐！！！[![🙂](../Images/1bb31e891282bfa40812655c9c9ace9e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DvXCaIjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.org/images/core/emoji/11/72x72/1f642.png)

你在 HTTPS 过得好吗？首次出现在[大家好，我是 Ankit](https://ankitvijay.net) 。
# 重定向影响搜索引擎优化

> 原文：<https://dev.to/franken/redirects-affect-prestashop-seo-43e>

[![](img/71139aea4cf29f9a1a4f1b1ed66242bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uwOoTkTO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/redirects-prestashop-seo.jpg)

## 重定向是将一个 URL 转发到另一个 URL 的过程。

它们是 SEO 生活中必不可少的一部分，有助于改善 SEO 和用户体验，从而增加你的销售。

## 重定向的类型

从旧网站重定向到新网站是用户体验最重要的因素之一。有时，你网站上的一个旧网址被删除了——你需要告诉你的客户和谷歌机器人这一变化。重定向有几种类型:

```
301, "Moved Permanently"—recommended for SEO
302, "Found" or "Moved Temporarily"
303, "Replaced". 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记 404 错误——你需要控制它来改善 SEO Prestashop 和处理客户的感受。

### 301 永久移动

一个域名过期不代表所有指向那个网站的反向链接一夜之间就消失了。链接几乎可以无限期地保持活动状态。301 重定向是一种永久重定向，它将 90-99%的链接价值(排名能力)传递给被重定向的页面。

301 是指这种重定向的 HTTP 状态代码。在大多数情况下，301 重定向是在网站上实现重定向的最佳方法。

这有助于将链接重定向到您正在处理的其他网站的相关内容、其他支持内容，或者将网站重新定位为 PBN 节点。我认为过期的域名不再像 PBN 网站那样好用了——尽管我们已经看到了更奇怪的事情发生。

一个更实际的用途是发送重定向到你代表客户贡献给其他主要网站的文章，特别是如果它和重定向链接在同一个网站上。被删除的页面也会失去它们的反向链接。

事实上，如果你的网站已经上线很长时间了，它可能已经获得了大量的反向链接。大量的反向链接意味着网页的排名会更高。但是在一个新的电子商务网站上，旧的链接结构消失了，所有的反向链接都断了。因此，它的排名可能会远远低于旧页面。

当您更改网页的 URL 时，重定向允许您保留网页的反向链接和搜索排名。

在这里，当你为你的业务创建/重建一个新的 Prestashop 网站时，你将尽可能地通过镜像旧的网站结构来保持事情的简单。

但是有时候，改变是必要的。现在你需要使用 301 重定向。

我们所知道的是，谷歌更新了他们的算法，使 301 重定向不再有任何权威惩罚——我们现在可以 301 重定向页面，而不会有任何排名损失(当然，假设页面内容是相同的)。

### 302 找到(HTTP 1.1) /暂时移动(HTTP 1.0)

有人指出，在某些情况下，301 和 302 可能会被类似地对待，但我建议，确保搜索引擎和各种浏览器给予完全信任的最安全的方法是在永久重定向 URL 时使用 301。

它有两个主要版本，1.0 和 1.1。在第一个版本中，302 表示状态代码“临时移动”这在 1.1 版中被改为表示“找到”

### 303、“被替换”

303 重定向是对 HTTP 状态代码 303 的响应，也称为“查看其他”状态代码。专家将特定类型的重定向描述为对标识现实世界对象的统一资源标识符(URI)请求的响应。

许多 HTTP/1.1 之前的用户代理不理解 303 状态。SEO 通常知道对绝大多数重定向使用 301，但是如果有 web 开发人员或设计团队想要使用 303，应该避免使用。

使用服务器端 301 HTTP 重定向将您的用户和搜索引擎重定向到 HTTPS 页面或资源。

## 谷歌讨厌 404 错误

这是个问题。消失的不仅仅是旧网址。现在，旧页面消失，新页面取而代之是很正常的。那是互联网上生死轮回的一部分。但是当谷歌搜索一个网站，突然发现一大堆 404，这让谷歌对这个网站有点怀疑。它开始变得不那么可信了。

这些不仅会影响用户体验，而且对于 SEO 来说通常也是不可取的。你可以通过查看谷歌的搜索控制台来发现你的 404 错误，它会给你所有有 404 错误的网址。

基本上，谷歌的思维过程是，“虽然有些内容是好的，但这个网站上有很多断开的链接。如果我把一个访问者送到这里，他们可能会因为所有的链接中断而感到沮丧。我不应该突出这个网站。”

结果是该网站的搜索排名整体受损，因为谷歌降低了其可信赖度和有用性的感知水平。现在，你必须找到一种方法将这些 404 页面重定向到一些有效的 URL，或者至少，你必须设计一个 404 页面来改善用户体验。

您可能希望使用 [Prestashop SEO 重定向高级](https://www.prestasoo.com/prestashop-modules/seo-redirection-premium.html?utm_source=devto&utm_medium=article&utm_campaign=redirects_affect)模块，这是一个简单而完整的解决方案，用于管理 301 / 302 / 303 重定向和 404 错误，这些错误来自 Prestashop 多年的 SEO 经验，专为不同的 SEO 需求(迁移到 Prestashop、频繁删除产品或类别、URL 模式修改或 404 问题)而构建。这些强大的功能将提高你的搜索引擎优化水平！
# 网站搜索引擎优化指南

> 原文：<https://dev.to/jsguru_io/seo-guide-for-websites--2g2e>

这个指南将涵盖一个非常重要的话题，搜索引擎优化。SEO 有时会像一个黑匣子一样出现，它的背后有很多魔力，并且它在不断地变化和发展。在这篇文章中，我们将通过一些 SEO 的最佳实践来尝试消除它。

* * *

除了[最新的谷歌算法更新](https://seo-hacker.com/google-algorithm-update-july-2018/)之外，在我们公司的网站 SEO 上工作并试图在雪崩的信息中战斗是促使我写这篇指南的原因，如果没有什么作为未来自我的参考的话。

这绝不是一个全面的指南，如果你想出现在谷歌的第一页，我会仔细阅读你需要覆盖的 SEO 基础知识，我们都知道这在实践中是如何进行的，如果你不在第一页，就好像你不存在一样，老实说，你多久去一次第二或第三页？是啊，我也这么想…

如果你对谷歌算法的历史以及它如何随着时间的推移而变化感兴趣，Moz 的伙计们已经将它组织在一个漂亮的[可搜索列表](https://moz.com/google-algorithm-change)中。

#### 让我们从头说起，什么是搜索引擎？

你知道你的网站有不同的页面，它有一个链接到每个页面的导航栏吗？嗯，你可以看看谷歌，或任何其他搜索引擎，作为互联网的导航菜单。谷歌“收集”了世界上所有的网站，并将它们编入索引。但是他是如何收集它们的呢？

嗯，谷歌有三种方法可以发现你的网站:

*   来自 DNS 注册管理机构——如果你不熟悉术语 DNS，我推荐你阅读这篇[文章](https://www.cloudflare.com/learning/dns/what-is-dns/)。当我们说到这个的时候，你知道吗[谷歌本身就是一个 DNS 注册商](https://www.theregister.co.uk/2005/02/01/google_domain_seller/)，尽管他们不卖域名。

*   如果其他网站链接到您的，Google 将跟踪这些链接并发现您(除非它们有一个 [rel="nofollow"](https://support.google.com/webmasters/answer/96569?hl=en) 属性)。这些链接被称为入站链接，它们是决定你的 SEO 排名的主要因素之一。

*   通过将你的网站和网站地图添加到谷歌搜索控制台，你基本上是在告诉谷歌“嗨，我的名字是某某，这是我的地址，这里你可以看到我的客厅，那是我的厨房，哦，我差点忘了门廊…”；).

[![An illustratin depicting a network of devices](img/33f9d8010d9662736ac7ca812608f34b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BuvxV1Br--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://delivery.eusi.cloud/api/v1/f1a4305c-e431-4668-ae4c-02f78c656a41/media/s3/1538239337662_JSGuru-Body-Image-1.png)

# 1。谷歌搜索控制台

这应该是你走向 SEO 荣耀之路的第一步。转到谷歌搜索控制台，添加你的网站，并通过验证过程，现在你在你的第一个十字路口。你有两个选择:

*   您可以添加您的两个网站版本，非 www 和 www 版本(例如 [https://jsguru.io](https://jsguru.io) 和 [https://www.jsguru.io](https://jsguru.io) )，然后选择一个首选版本。在我们的例子中，我们将首选设置为非 www。

*   或者你可以只添加一个版本到谷歌搜索控制台，无论你喜欢哪个，并配置你的服务器从其他版本重定向到你喜欢的版本(例如，只添加 [https://jsguru.io](https://jsguru.io) 到谷歌搜索控制台，并从 [https://www.jsguru.io](https://jsguru.io) 重定向到 [https://jsguru.io](https://jsguru.io) )。它必须是一个永久重定向，所以您的 HTTP 响应代码应该是 301。

无论哪种方式，你都必须处理这两种域名变体，如果你只是把它们留在野外，而没有像前面描述的那样处理它们，那么很有可能谷歌会把它们视为重复内容，而重复内容会受到惩罚。

另一方面，如果你只提供一个域名变体，而你的网站在另一个上出错，这将是非常糟糕的用户体验，并且有损于你的品牌。

# 2。网站地图

顾名思义，网站地图是所有网站页面的地图，以一种谷歌或其他搜索引擎容易理解的格式呈现。它是所有链接的集合，通常是 XML 文档，但也可以是不同的格式，比如 HTML。把它们看做你网站的骨架。有很多免费的在线工具可以为你生成它们。完成后，你应该[将它提交到谷歌搜索控制台](https://support.google.com/webmasters/answer/183668?hl=en)的网站地图部分。这将使谷歌索引你更容易，但仍然有耐心，这可能需要时间。

如果你有一个像新闻杂志或博客这样的发布网站，如果你能找到一种方法来自动更新它们以反映你网站上的变化，那将是最好的。

因为你可以向谷歌搜索控制台提交尽可能多的不同站点地图，所以一个好的方法是至少有两个站点地图，一个用于网站的“静态”部分，不经常改变，另一个用于网站的“动态”部分，经常更新，比如博客部分。

在我们的例子中，我们有两个站点地图，一个覆盖了我们的主导航，包含了到主页、关于我们、我们做什么等等页面的链接，另一个是我们的博客，我们每两周在这里发表文章。

# 3。HTTPS vs HTTP

从 2018 年 7 月起，谷歌 Chrome 开始将没有 HTTPS 的网站标记为不安全，这可能会对你的流量产生负面影响，用户可能会因为认为你的网站不安全而决定离开，或者他们可能会担心这是某种骗局，此外，据报道，HTTPS 网站比 HTTP 网站略有排名优势。

在互联网的早期，获得 SSL 证书并通过 HTTPS 保护您的网站是一个繁琐而昂贵的过程，但多亏了开源社区和 Let's Encrypt Foundation，现在设置起来既免费又容易。
点击以下链接，查看如何将 HTTPS 添加到您的网站的详细教程:

*   [阿帕奇教程](https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-16-04)

*   [Ngnix 教程](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)

# 4。元标签

*是的，meta 标签还是一个东西！*
描述元标签允许你定制将在搜索结果中显示的简短描述，否则，你将由谷歌来决定什么是重要的，什么将被显示，这可能会导致一些讨厌的和完全不相关的描述。传统上，描述长度被限制在 155 个字符，但最近谷歌开始允许更多，所以为了安全起见，你的描述应该在 50 到 300 个字符之间。

虽然关键词 meta 标签已经过时一段时间了，但我仍然把它包括在内，因为我使用的 SEO 工具让我觉得它不见了，而且因为我有点强迫症，所以我不得不把它加上去。

og，不要与原始 gangsta 混淆，meta 标签被脸书、LinkedIn 和其他社交媒体用来确定当有人分享你的页面时会显示什么内容。twitter 元标签也是如此。

```
<meta name="description" content="JSGuru is a high-quality software development shop where clients come first. We build your desired products and provide excellent, long-term customer support so that you can focus on the core of your business.">

<meta name="keywords" content="software development, web development, mobile development">

<!-- Facebook-->
<meta property="og:url"                content="https://jsguru.io">
<meta property="og:type"               content="website">
<meta property="og:title"              content="JSGuru">
<meta property="og:description"        content="JSGuru is a high-quality software development shop where clients come first. We build your desired products and provide excellent, long-term customer support so that you can focus on the core of your business.">
<meta property="og:image"
      content="https://jsguru.io/img/jsguru.png" />

<!-- Twitter -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:site" content="@jsguru_software">
<meta name="twitter:creator" content="@jsguru_software">
<meta name="twitter:title" content="JSGuru">
<meta name="twitter:description" content="JSGuru is a high-quality software development shop where clients come first. We build your desired products and provide excellent, long-term customer support so that you can focus on the core of your business.">
<meta name="twitter:image"
      content="https://jsguru.io/img/jsguru.png"> 
```

Enter fullscreen mode Exit fullscreen mode

# 5。规范链接

什么是规范链接？对于所有不熟悉这个术语的人来说，规范链接是放在文档头部的 HTML 标签，它们的目的是指向内容的原始来源。规范链接可以指向与您当前所在页面不同的页面/链接，或者它们可以是自引用的，这意味着它们可以指向您当前所在的同一页面。

当谷歌遇到一个指向不同页面的规范链接时，它会告诉他“嘿，不要索引这个页面，你应该索引的实际来源就在那里。”。

如果规范链接是自引用的，这意味着谷歌“好了，这是内容的确定的根”。如果谷歌在不同的网站上再次发现相同的内容，他肯定会知道你的内容实际上是原创的。

```
<head>
    JSGuru - What We Do
    <link rel="canonical" href="https://jsguru.io/what-we-do">
</head>
<body>
 ...
</body> 
```

Enter fullscreen mode Exit fullscreen mode

你还记得我之前提到过我们应该同时处理非 www 和 www 版本的域名吗？嗯，注意不要在规范链接中混淆它们，选择一个首选版本并坚持使用它！

# 6。本地化- hreflangs

如果你有一个多语言网站，你想根据访问者的国家提供不同的语言版本，该怎么办？该如何着手呢？

一种常见但不推荐的方法是通过用户的 IP 地址对其进行地理定位。这通常是通过将 IP 表存储在您的数据库中并与用户的进行比较来完成的，或者使用第三方服务，如 [Ipinfo](https://ipinfo.io/) 。
在确定用户的位置后，你将把它存储在一个 cookie 中，并提供适当的语言。

这似乎是一个明智的方法，不是吗？但是为什么不鼓励呢？

首先，通过 IP 进行地理定位是不精确的，尤其是现在人们重视他们的在线隐私，并且越来越多地使用 VPN，此外，一些互联网提供商自己在提供动态 IP 时也使用代理。

其次，如果用户是德国人，但他在英国出差怎么办？他会得到英文版本，我们不希望这样。

第三，您取消了用户手动选择语言版本的可能性。

最后但并非最不重要的是，你阻止了谷歌正确地索引你所有的网站版本，因为他们的抓取服务器主要在美国。

### 既然我们已经过了什么没有做，那就让我们过一遍你应该做什么吧！

首先，你的 hreflang 链接指向的页面应该有相同的内容，只是语言不同，它们不应该有完全不同的内容，这是不行的。

说到实际链接，您有两种选择:

#### a)在 HTML 文档的头部添加 hreflang 链接

```
<!-- English (default) language page -->
<head>
    JSGuru - What We Do
    <link rel="canonical" href="https://jsguru.io/what-we-do">
    <link rel="alternate" 
          hreflang="x-default"  
          href="https://jsguru.io/what-we-do" />
    <link rel="alternate" 
          hreflang="en" 
          href="https://jsguru.io/what-we-do" />

    <link rel="alternate" 
          hreflang="de" 
          href="https://jsguru.io/de/was-wir-tun" />
    <link rel="alternate" 
          hreflang="de-ch" 
          href="https://jsguru.io/de/was-wir-tun" />
    <link rel="alternate" 
          hreflang="de-at" 
          href="https://jsguru.io/de/was-wir-tun" />
</head>
<body>
 ...
</body> 
```

Enter fullscreen mode Exit fullscreen mode

重要的是，您要在所有页面上添加 hreflang 链接，而不仅仅是您的默认语言页面。

```
<!-- German Language Page -->
<head>
    JSGuru - Was Wir Tun
    <link rel="alternate" 
          hreflang="x-default"  
          href="https://jsguru.io/what-we-do" />
    <link rel="alternate" 
          hreflang="en" 
          href="https://jsguru.io/what-we-do" />

    <link rel="alternate" 
          hreflang="de" 
          href="https://jsguru.io/de/was-wir-tun" />
    <link rel="alternate" 
          hreflang="de-ch" 
          href="https://jsguru.io/de/was-wir-tun" />
    <link rel="alternate" 
          hreflang="de-at" 
          href="https://jsguru.io/de/was-wir-tun" />
</head>
<body>
 ...
</body> 
```

Enter fullscreen mode Exit fullscreen mode

注意不要将 hreflang 标签与指向不同页面的规范链接组合在一起。Hreflang 标记只能与自引用规范链接结合使用。

在我们的代码示例中，我们只在英语(默认)页面中包含规范链接，因为这是我们内容的原始来源，德语页面不应该包含规范链接，因为这是不同语言的相同内容，如果我们包含它，我们只会混淆 Google crawler。

#### b)将 hreflang 链接添加到站点地图

```
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
        xmlns:xhtml="http://www.w3.org/1999/xhtml">
  <url>
    <loc>https://jsguru.io/what-we-do</loc>
    <xhtml:link rel="alternate" 
                hreflang="x-default" 
                href="https://jsguru.io/what-we-do" />
    <xhtml:link rel="alternate"
                hreflang="de"
                href="https://jsguru.io/de/was-wir-tun"/>
    <xhtml:link rel="alternate"
                hreflang="en"
                href="https://jsguru.io/what-we-do"/>
    <lastmod>2018-08-28T09:24:19+00:00</lastmod>
    <priority>1.00</priority>
  </url>
</urlset> 
```

Enter fullscreen mode Exit fullscreen mode

**重要提示——您应该始终提供完整的链接，包括指向规范或 hreflang 链接标签的协议(也称为绝对链接)!**

你可以阅读更多关于本地化、hreflang 标签和其他本地化方法的内容，我在这里没有提到[。
这里有一些有用的工具:](https://support.google.com/webmasters/answer/189077#language-codes)

*   [Hreflang 标签生成器](https://www.aleydasolis.com/english/international-seo-tools/hreflang-tags-generator/)

*   [Hreflang 标签验证工具](http://hreflang.ninja/)

# 7。图像和视频

当涉及到图像时，您应该总是添加 alt 属性，它包含图像的文本描述。添加 alt 属性的好处是双重的，首先，它会使你的网站更容易访问，有视觉障碍的人会得到图像的描述，其次，谷歌将能够理解你的图像的上下文，从而增加你在一般搜索和图像搜索结果中的可见性。

视频也是如此。不管你是直接把它们放在你的网站上，还是放在 Youtube/Vimeo 这样的平台上，你都应该提供文字记录。谷歌不能处理图像和视频，搜索引擎识别其内容的唯一方式是通过文本描述。

# 8。谷歌结构化数据

如果你曾经认为 SEO 是一个黑匣子，等你从 Google 结构化数据开始，那就是一个黑色的潘多拉盒子。当你打开这个盖子的时候，它是关不上的。

这是一个如此庞大的主题，它本身值得一个全新的博客帖子，但我将尝试在这里涵盖基础知识，并为您提供一些有用的链接。

Google 结构化数据是 Schema.org 富数据标准的一个实现。谷歌用这些数据来填充它的知识图表，并向你展示丰富的搜索结果，也称为谷歌财富卡。

此外，您可以做任何正确的事情，但您的结构化数据可能仍然不会出现在搜索结果中。

> 使用结构化数据使特征能够存在，但并不保证它会存在。谷歌算法根据许多变量，包括搜索历史、位置和设备类型，定制搜索结果，为用户创造它认为最好的搜索体验。
> 在某些情况下，它可能会确定一个功能比另一个更合适，甚至确定一个普通的蓝色链接是最好的。-根据[谷歌的政策](https://developers.google.com/search/docs/guides/sd-policies)

您可以提供三种格式的结构化数据，但是 Google 推荐使用 [JSON-LD](https://json-ld.org/) ，所以我们将专注于这种格式。这里有一个看起来如何的例子:

```
<script type="application/ld+json">
    {
         "@context": "http://schema.org",
         "@type":"LocalBusiness",
         "address": {
             "@type": "PostalAddress",
             "addressLocality": "Manhattan",
             "addressRegion":  "NY",
             "postalCode":"10036",
             "streetAddress": "400 Broadway"
         },
         "description": "This is your business description.",
         "name": "Craig's Car Repair",
         "telephone": "555-111-2345",
         "openingHours": "Mo,Tu,We,Th,Fr 09:00-17:00",
         "geo": {
             "@type": "GeoCoordinates",
             "latitude": "20.75",
             "longitude": "13.98"},
             "sameAs" : [
                 "http://www.facebook.com/example",
                 "http://www.twitter.com/example",
                 "http://plus.google.com/example"
             ]
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个 *LocalBusiness* 实体的例子，它是*组织*实体的子集。对于结构化数据，您应该总是尽可能地具体，并使用最狭义的实体定义，如本例所示。此外，您应该始终尝试提供所有可能的属性。

您可以在此[链接](https://jsonld.com/)上找到社区已经尝试和测试过的示例片段，只需用您自己的值替换即可。

对于初学者，我还推荐使用 [Merkle 结构化数据生成工具](https://technicalseo.com/seo-tools/schema-markup-generator/),稍后当您更好地理解了一切是如何工作的时候，您可以使用额外的属性扩展生成的数据。

另一个选择是使用[数据高亮器](https://support.google.com/webmasters/topic/2774098?hl=en&ref_topic=2692946)，它是谷歌搜索控制台中搜索外观部分下的一个工具，但它目前只在旧视图中可见！谷歌目前正在推出一个新的搜索控制台，所以我不确定这个工具的未来，这是待定的。

### 你的结构化数据应该放在哪里？

结构化数据应该放在 HTML 文档的头部。

```
<head>
    <script type="application/ld+json">{...}</script>
</head> 
```

Enter fullscreen mode Exit fullscreen mode

有一条通用的经验法则是你应该遵守的，你放在脚本标签中的所有数据在页面上都应该是可见的。要了解更多信息，你应该查阅谷歌指南。

以下是一些有用的链接:

*   [结构化卡简介](https://developers.google.com/search/docs/guides/intro-structured-data)
*   [导轨](https://developers.google.com/search/docs/guides/search-gallery)
*   [富卡搜索结果测试工具](https://search.google.com/test/rich-results) *(目前处于测试阶段，仅限于以下实体:[职位发布](https://developers.google.com/search/docs/data-types/job-posting)、[菜谱](https://developers.google.com/search/docs/data-types/recipe)、[课程](https://developers.google.com/search/docs/data-types/course)、[电视电影](https://developers.google.com/search/docs/data-types/media)、[事件](https://developers.google.com/search/docs/data-types/event) )*
*   [谷歌结构化数据验证工具](https://search.google.com/structured-data/testing-tool)
*   [谷歌简单教程如何添加富卡](https://codelabs.developers.google.com/codelabs/structured-data/#0)

# 9。谷歌本地业务

其中，Google Business 本质上是一种添加 LocalBusiness 结构化数据的更加用户友好的方式，您绝对应该这样做！

如果你的公司有多个地点，你应该把它们都加进去。最好将这种方法与手动添加结构化数据结合起来，因为如果谷歌的知识图表中包含了你的社交媒体页面和 B2B 平台，如 [Clutch](https://clutch.co/profile/jsguru) ，它会自动将它们链接到你的业务，你的评论会显示在丰富的结果中。

这里没什么好补充的，继续吧！

# 10。博客

博客仍然是搜索引擎优化和数字营销的圣杯。尽管许多主题的文章已经饱和，但这仍然是建立链接和增加网站流量的最佳有机方式。

你应该尝试定期发布，但你不应该牺牲质量而忽视数量，谷歌的算法近年来更多地转向质量而不是数量。他们不仅关注内容的质量，也关注链接的质量。

当谈到博客时，你唯一的困境是是否把它放在一个子域上，例如 blog.jsguru.io 或一个子文件夹[jsguru.io/blog](https://jsguru.io/blog)。
嗯，实际上这不是一个两难的问题，你应该[把它放在一个子文件夹](https://www.smartinsights.com/search-engine-optimisation-seo/internal-linking-strategy/which-is-best-for-blog-seo-subdomain-or-subfolder/)里，因为子域名被谷歌视为不同的域名，所以它们需要独立于你的主域名建立自己的声誉。这种方法不会给你增加任何价值。

当谈到博客的实现时，你仍然应该至少部分地坚持老派的服务器端渲染方法，因为谷歌索引是两遍的，第二遍是针对 JS 网站的，这是在第一遍之后一周，特别是如果你的商业模式是以出版为中心的话。保罗·金兰在推特上证实了这一点:

> ![Paul Kinlan profile image](img/b713c3aa8e92a24455e0e7e915a8a2df.png)保罗·金兰[@保罗 _ 金兰](https://dev.to/paul_kinlan)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ HenrikJoreteg](https://twitter.com/HenrikJoreteg)索引对于纯客户端侧是延迟的。Google 索引器是两遍的，第一遍运行是不带 js 的，然后一周以后是带 is 的2018 年 9 月 12 日下午 12 点 26 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1039852756113080320)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1039852756113080320)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1039852756113080320)

# 11。谷歌页面洞察

表现是你排名的主要因素之一。你网站的加载时间越短，你的排名就越高，尤其是在移动设备上。

谈到性能调整，你可以利用的一个很好的工具是 [Google Page Insights](https://developers.google.com/speed/pagespeed/insights/) 。

提高网站性能的一些基本和常见的方法是:

*   缩小你的 javascript 和 css 文件。
*   当添加 jpg/jpeg 图像时，将它们压缩到 80%的质量，这种差异在 web 环境中是看不到的，但它们的大小会显著减小。
*   如果可能的话，使用一个无 cookie(无状态)域来服务你的静态资产，比如图像。为不同的屏幕尺寸提供不同的图像。不需要在 320px 的宽屏上加载 1920x1080px 的图像。
*   延迟加载图像——只在图像进入设备的视窗时才加载图像。
*   Gzip 压缩和浏览器缓存(图像、javascript、css)。这里有一个很好的教程来帮助你提高 ngnix 服务器的页面洞察力排名。如果你用的是 apache，你应该查一下 *mod_deflate* 和 *mod_expires* 扩展。
*   使用[脚本延迟和异步](https://www.growingwiththeweb.com/2014/02/async-vs-defer-attributes.html)。

# 12。移动响应和移动优先索引

越来越多的互联网用户来自移动设备，实际上，他们总体上已经超过了桌面用户，根据这一趋势，谷歌几年前就已经将重点转移到了移动设备上。如果你记得谷歌曾经在它的搜索结果中显示一个标签*手机友好的*。

现在没有必要了，拥有一个手机友好的网站，在所有屏幕尺寸上提供愉快的体验是显而易见的。为了提供更好的移动 UX，一些人牺牲了他们网站的移动版本的内容。

随着谷歌算法的最新变化，现在他们可能要为此付出代价了。Google 已经开始 [**移动第一索引**](https://webmasters.googleblog.com/2018/03/rolling-out-mobile-first-indexing.html) ，但是这意味着什么呢？

以前谷歌会抓取桌面版本的网站进行索引，从现在开始，移动版本将用于索引。因此，如果你为了让所有内容都适合较小的屏幕而忽略了内容，你的搜索引擎优化可能会受到影响。

# 13。摘要

如果你已经走了这么远，我祝贺你，写这些对我来说已经很累了，我只能想象阅读它的感觉！是的，你值得拍拍自己的肩膀。如果我想让你从这篇文章中得到什么，那就是下面的内容。

#### 优秀 SEO 最重要的三个因素是:

1.  优质内容，
2.  高质量流量和
3.  质量链接。

正如生活中的一切一样，质量胜于数量，你只需为一些不知名网站上的链接付费，或者粗制滥造一些浅显的文章，而这些文章几乎没有触及它们应该涵盖的主题，这样的日子已经一去不复返了。最后，要有耐心，这些事情需要时间，谷歌没有无限量的资源，索引新网站并不是他们的首要任务，所以可能需要几周甚至几个月的时间，才能开始有一些真正的结果。

OMG！我差点忘了，我强烈推荐你观看这个来自 [Digital4Startups](http://digital4startups.com/) 的丽娃问答节目，我们有幸在办公室接待了她。视频质量很好，但是音频是最重要的部分！

[https://www.youtube.com/embed/dFi2JBXG4dg](https://www.youtube.com/embed/dFi2JBXG4dg)

## 出发前…

如果你喜欢读这篇文章，请分享它。你应该看看我们的其他出版物，你可能也会喜欢它们！我们不时地写一些关于软件开发、技巧和诀窍，以及如何成为一个更好的开发人员和商业人士的文章。加入我们不断改进的旅程吧！

在[脸书](https://www.facebook.com/jsguruio/)、[推特](https://twitter.com/jsguru_software)、 [LinkedIn](https://www.linkedin.com/company/jsguru) 、 [Medium](https://medium.com/jsguru) 上关注我们，或者在 **DEV.to** 上关注我们。
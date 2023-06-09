# 性能原理

> 原文：<https://dev.to/joshghent/principles-of-performance-8mn>

<figure>[![](img/cc4465b736059b3991db3f70d1f6f91c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XYW6ghS0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AauCh4IotYWV1tCLL.) 

<figcaption>照片由[卡拉·富勒](https://unsplash.com/photos/34OTzkN-nuc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/running-cheetah?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

在网络上，速度就是一切。但你知道，对吗？与其向你抛出网站保留率的百分比和统计数据，不如让我们来看看在寻求改善你的应用程序或网站性能时需要记住的一些关键原则。

无论您使用什么技术，这些都是要使用的原则，并且它们的范围更广。目的是把这个做成小手册，而不是手册。就像一旦你学会了开车，理论上你就可以开任何其他的车一样，本文的目的是教授原理而不是实现。

<figure>[![](img/80e1605e99974c36732ee4136f19d92e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i0Z9MbLw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/480/0%2A-PJFFL6w3b2sbjF5.) 

<figcaption>这是你的网站</figcaption>

</figure>

### 更多的网络往返=更慢的加载时间

毫无疑问，页面加载速度慢的主要原因之一是网络往返。通过下载更多的资源(javascript 库、CSS 模块、图像)，最终用户将不得不建立更多的网络连接。无论网速如何，这都会对页面加载时间产生负面影响，网速越慢，这种影响就越明显。

解决这个问题的第一个方法是汇编你的资产。您可以尝试像 Webpack 这样将各种样式表和脚本编译成包——这意味着您有一个可以全面使用的 CSS 和 JS 文件。此外，如果你的页面上有很多图标或精灵，也可以把它们放在一张图片上，然后像精灵表一样被引用——这是游戏开发者常用的技巧，但也可以在 web 上使用。

另一种方法是减少你正在使用的资产——质疑你是否真的需要那个库。像 [youdontneedjquery](http://youmightnotneedjquery.com/) 这样的网站大量涌现，说明了为什么你可能不需要包含那个特定的库。此外，如果您决定需要那个库或框架，那么通常您可以只导入您需要的。在 [Lodash](https://lodash.com/) 的情况下，你可以指定一个单独的函数来保存到你的依赖项中(为新的贡献者改进“npm 安装”时间),并且只导入那个函数，这样就不会用未使用的代码来膨胀你的应用程序。通过 Bootstrap，我发现自己敏锐地意识到了这一点。bootstrap 经常被仓促地导入并用于简单易用的网格布局。说实话，bootstrap 包含了大量**CSS 模块，用于超大屏幕、图标、井、面包屑和任何你能想到的建立网站的东西。但是在 99%的情况下，你不需要它的所有特性。使用 [Bootstrap 4，您可以使用 webpack](https://getbootstrap.com/docs/4.0/getting-started/webpack/) 来导入特定的插件。使用 Bootstrap 3.3，您可以更加细化并创建自己的定制版本 Bootstrap[和](https://getbootstrap.com/docs/3.3/customize/)，仅包含您需要的内容。**

注意这个技巧只适用于 HTTP 1.1。随着 HTTP2 的兴起，拥有大量资产比一个单一的包更快。然而，由于性能原因，HTTP2 还没有被广泛采用。

### 更大的资产=慢

Addy Osmani 在 CSSConf 上的精彩演讲展示了在你的页面上拥有大量图像资产的不利影响(尤其是在可视视图中)。要有一个快速运行的应用程序或网站，你必须摆脱那些让你慢下来的东西。

<figure>[![](img/942350dfc1c829a2aba19a5f7ea994e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y94wclvh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/449/0%2AweuFQ40LFR1eJQyR.) 

<figcaption>我找不到一张尤塞恩·博尔特拿着一袋沙子的照片，但这里有一张最好的！</figcaption>

</figure>

尤塞恩·博尔特能以 9.58 秒跑完 100 米。那是非常快的。但是如果他带着一袋沙子，他会慢很多。这听起来很傻，但是这袋沙子说明了你正在使用的所有那些繁琐的库和图像，它们试图让网站看起来更时尚，但最终却降低了速度。当然，你的图像会弹出来，并做一些旋转，但是在动画库加载之前，人们早就离开网站了。

如果你决定，不，我需要在第一次画图时加载 10Mb 的 4k 图像，然后通过 CDN(如 [cloudflare](https://www.cloudflare.com/) 或 [cloudinary](https://cloudinary.com/) )交付它，然后在服务器和客户端级别积极缓存它。这将有利于经常性用户，并可以在第二次加载时将页面加载时间减少 10 倍。客户会感谢你尊重他们的数据计划。超额的移动数据费用会很快增加，如果你的应用程序是造成这种情况的罪魁祸首，那么你可能会发现用户放弃了这项服务。

### 感觉快=就是快

当装载东西时，如果它“感觉”快，那么它就会快。但是“感觉”快是什么意思呢？例如，当加载一个网站时，优先考虑用户可以首先看到的部分——这被称为初始可见视口。它会因设备而异，但是使用像 [Penthouse](https://github.com/pocketjoso/penthouse) 和[critical class](https://github.com/addyosmani/critical)这样的工具，你可以捆绑和内嵌呈现网站顶部的样式。

这也意味着在尽可能短的时间内进行互动。你想让一个人在你的网站上向下滚动，而不是点击一堆我称之为“塔斯马尼亚滚动条恶魔”的东西。我相信你自己也有过这样的经历，向下滚动一个网站，然后可见视窗上方的图像加载并向下推送你试图查看的内容。令人难以置信的恼人的 UX，并占用宝贵的 CPU 时间。

<figure>[![](img/6170dd9879884c953d0564ca61df65b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mY4ipiNE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/250/0%2AsOAULjFJsJE0_kYf.) 

<figcaption>你不希望这个家伙在你的地盘上</figcaption>

</figure>

为了解决这个问题，在加载图像之前，放置具有图像宽度和高度的不可见 div，这将防止滚动条恶魔出现。你也可以对大面积的文本或类似的东西使用同样的技巧，例如，你可能已经看到脸书和吉拉使用背景蒙版。这里有一篇很棒的文章专门介绍它们是如何工作的。

但是最初可见的视窗下面的页面内容呢？可以偷懒装的。简单。利用异步脚本和其他方法来确保您的资产快速交付。尝试将任何后台任务或其他资产推迟到以后。例如，如果你有一个待办事项应用程序，我会将主菜单栏和任务的占位符屏蔽严格地内联呈现。然后我会优先加载 JS，然后从数据库中请求待办事项。其他任何东西，如帐户设置，用户的个人资料图像等。可以留着以后用。该应用程序的目标是显示待办事项。让它成为最快的事情，忘记其他的一切。

### 托管

将你的主机提供商视为一个可能的性能瓶颈。尽管我们在本文中已经谈了很多关于初始加载时间的问题，但是考虑某些交互的性能还是值得的。

使用上面的待办事项应用程序示例，最重要的交互是将待办事项标记为已完成。当用户将项目标记为完成时，可能会触发 AWS lambda 上托管的无服务器功能。如果您发现此操作很慢，请调查瓶颈。是数据库连接时间吗？分配给 lambda 函数的内存够用吗？在无服务器的情况下，可能该功能会变冷，因此启动时间会很慢——所以最好将其托管在长时间运行的服务器实例上。关键是，即使是最简单的行动，也有许多考虑因素和可能的瓶颈。

如果您使用的是关系数据库(如 MySQL 或 PostgreSQL ),那么值得看一看您的表架构。糟糕的数据库设计可能需要更多的连接。此外，将未索引的列与大型数据集连接会对查询性能造成不利影响，因此建议查看您正在执行的查询并对其进行优化。您甚至可以考虑使用 Redis 或 Memcached 来缓存常见的查询响应。

### 设定预算和目标

很有可能，如果你是开发人员，那么你将不能容忍任何缓慢的事情。此外，你将会很好地了解应该以多快的速度运行，以及人们最常使用的设备。

现在您对最常见的用例有了清晰的了解，下一步是创建性能预算。换句话说，它应该以多快的速度加载？有一个明确的目标会给你一些目标，并密切关注每一个新的代码变更。保持警惕，坚持低于 1 秒的加载时间，不要接受任何超过这个限制的新代码。

希望你能在将来利用这些原则，并将它们应用到你自己的网站或应用程序中。无论你使用什么技术，它们都应该是可转移的。让我知道你在[hola@joshghent.com](//mailto:hola@joshghent.com)的任何表现原则，或者在下面评论！我也在 twitter [@joshghent](https://twitter.com/joshghent?lang=en) 上发布关于网络性能和更多的信息。

* * *
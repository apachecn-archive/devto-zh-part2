# 30 分钟内解决 90%的谷歌页面速度洞察问题

> 原文：<https://dev.to/joshghent/solve-90-of-google-pagespeed-insights-issues-in-30-minutes-6fk>

<figure>[![](../Images/60d11183c6bcb18b9aad35bb8fa65ccb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QvjBXXyN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASooLbBGN7oheUAWcNCevjg.jpeg) 

<figcaption>来源:[https://unsplash.com/photos/fxAo3DiMICI](https://unsplash.com/photos/fxAo3DiMICI)</figcaption>

</figure>

绩效是网站保持率的一个关键因素。时间就是金钱，有一长串的例子证明[人们期待近乎即时的加载](http://loadstorm.com/2014/04/infographic-web-performance-impacts-conversion-rates/)，如果[没有在 3 秒钟内加载](https://www.nytimes.com/2012/03/01/technology/impatient-web-users-flee-slow-loading-sites.html?pagewanted=all)，他们就会离开网页。

虽然 Google Page speed Insights 的得分并不能保证一个网站会很快，但它确实给出了这个事实的一些迹象。此外，它现在是谷歌和其他搜索引擎在其 SEO 排名算法中使用的数百个因素之一。事实上，从商业角度来看，web 性能是一个值得关注的问题。

因为开发者和企业都以“物有所值”为目标，在这里，我将带你经历三个简单的步骤，你可以采取行动，将你的分数提高至少 20 分(如果不是更多)。这不是一个确凿的证据，但是，应用[帕累托原则](https://betterexplained.com/articles/understanding-the-pareto-principle-the-8020-rule/)，最好花时间做 20%的工作，这样你就能获得 80%的收益。

### 缩减资产

大的页面尺寸是因为他们不屑于臃肿的 javascript 和 CSS 资产。减小这些尺寸的一个简单方法是缩小它们。这可以通过像[吞咽](https://gulpjs.com/)或[咕噜](https://gruntjs.com/)这样的任务运行器来完成。如果你赶时间，那么你可以使用在线工具，如[迷你器](https://www.minifier.org/)——然而，这将意味着你每次改变 Javascript 时都需要重新运行。我建议设置一个自动任务。

如果你喜欢冒险，那么你可以进一步优化，将 Webpack 与[树摇动](https://webpack.js.org/guides/tree-shaking/)结合使用。这将从您的 Javascript 中删除任何未使用的代码，从而减小您正在缩小的底层模块的大小。

深入探究缩小化，你会很快意识到减少资产规模的最佳方式是**减少资产数量**。因此，试着减少你正在导入的 [Lodash](https://lodash.com/) 模块的数量，或者 [Moment.js locales](https://momentjs.com/docs/) ，或者也许你正在导入整个 [Bootstrap](https://getbootstrap.com/) 只是为了使用行和容器系统。

图像是大文件问题的最大元凶之一。根据 [HTTP 档案，截至 2018 年 3 月 15 日](http://httparchive.org/interesting.php?a=All&l=Mar%2015%202018)，**超过一半的平均网站有效载荷在图像中。因此，集中精力减少你使用的图片数量并优化它们是至关重要的。在把图片放到你的页面上之前，一定要先把它们压缩。如果你赶时间，那么试试这个叫做[优化器](http://optimizilla.com/)的工具。解决方案是使用您的任务运行(Gulp、Grunt 或 Webpack)以及一个插件(如 [ImageMagick](https://www.imagemagick.org/script/index.php) 等)来自动化这个过程。如果你通过博客发布图片，甚至还有一个 [Wordpress 插件](https://en-gb.wordpress.org/plugins/ewww-image-optimizer/)。**

### 缓存资产

速度上的巨大节省将来自于客户的浏览器不必首先下载资产。这不仅会大大改善页面加载时间，还会减少服务器所需的带宽(这取决于你的主机提供商可能会减少账单)。此外，如果客户正在他们的手机上查看网站，他们将能够加载您的网站，安全的知识，他们不需要担心他们的数据计划。

你可以用几种方法缓存你的资产，最简单的是在你的请求上设置一个缓存控制头。在 Apache 服务器上，您可以如下进行操作。

<figure>[![](../Images/4dea95d1833deb0e5226aec9d3ec8c86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z6f8MYlQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ASsZKehPZW3P9knAC.) 

<figcaption>此处代码:[https://gist . github . com/joshghent/fcca 761d 006 AE 34 a1 a2 AAA 0406 a9 E0 f1](https://gist.github.com/joshghent/fcca761d006ae34a1a2aaa0406a9e0f1)</figcaption>

</figure>

应用程序缓存也非常有用。许多网站面临的一个主要问题是长时间运行的查询。对此，一个简单的解决方案是在应用程序中缓存查询响应，前提是查询值得缓存，并且其响应不会有太大的变化。Laravel 内置了这个功能， [Express 也可以扩展到这个功能](https://www.sohamkamani.com/blog/2016/10/14/make-your-node-server-faster-with-redis-cache/)。这将减少服务器响应时间，从而加快客户端的页面加载速度。

### 启用压缩

最后，在减少和缩小你的站点负载后，你可以启用压缩，这将确保它们以尽可能小的形式传输。有两种流行的算法， [Gzip](http://www.gzip.org/) 和 [Brotli](https://github.com/google/brotli) 。后者是一个更近的趋势，实际的压缩率比长期以来的 Gzip 更好。尽管如此，我仍然推荐使用 Gzip，因为 Brotli 在客户端解压缩需要更多的 CPU 能力(因此也需要更多的时间)。

你可以在网上找到关于如何做到这一点的指南，这些指南在这篇博文发表后很长一段时间内都是最新的，但是这里有一个适用于 Apache 的[指南(我假设它会保持不变！).](https://varvy.com/pagespeed/enable-compression.html)

我赶紧补充，如果你的站点必须支持 [Netscape 3 和低于](http://schroepl.net/projekte/mod_gzip/browser.htm)的版本，那么压缩在这里将是多余的，因为它们只支持 HTTP/1.0，不发送 Accept-Encoding 头。尽管如此，由于全球只有不到 1%的人在使用“五大”浏览器之外的其他浏览器，我想你不会受到影响。

性能应该被认为是一个特征，虽然在性能上花费大量时间是很好的，但公司倾向于优先考虑其他任务。虽然这并不理想，但是使用上面的 3 个技巧(每个点应该不超过 30 分钟)，你可以快速提高你的站点或应用程序的性能，此外还可以有更多的时间来分配进一步的性能。

你还有其他的表演快速技巧吗？请在[hola@joshghent.com](//mailto:hola@joshghent.com)告诉我或者在下面评论！我也在 [twitter @joshghent](https://twitter.com/joshghent?lang=en) 上发布关于网络性能和更多的信息。

* * *
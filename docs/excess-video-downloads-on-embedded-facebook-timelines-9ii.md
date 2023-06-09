# 嵌入式 facebook 时间轴上的超额视频下载

> 原文：<https://dev.to/dougsillars/excess-video-downloads-on-embedded-facebook-timelines-9ii>

成千上万的网站使用脸书插件将社交媒体内容嵌入他们的网站。在研究这个插件时，我发现(对于 Chrome 和 Android 用户来说)它可以自动下载超过 30%的视频，不管视频是否被观看。这将导致大量数据被下载(有时高达 2MB 的视频)，这对一些用户来说[非常昂贵](https://whatdoesmysitecost.com/test/180821_QZ_ef338a7895c86ed285c83e6d6b28087a)。此外，相同的视频以不同的维度被下载多次。这种“脸书惩罚”在 Chrome 和 Chrome mobile (Android)上观察到了，但在 Safari 或 iOS 上没有。

### 背景

我一直在挖掘 HTTPArchive 并检查使用视频的网站。在我的研究中，我发现 HTTPArchive 中三分之二的视频来自脸书的 CDN fbcdn.net(移动网站，2018 年 8 月 1 日运行):

为了测试这些视频是如何被使用的，我从有脸书视频内容的网站列表中随机选择了[美国柴油零件](https://usdieselparts.com/)。下面两张截图分别是桌面(左)和移动(右)。脸书微件以黄色突出显示。在移动网站上，橙色线条表示屏幕的一整页。这个小部件在桌面的第 4 个视窗中，在移动的第 7 个视窗中。

[![](img/b40684034183f8dc718d4f3880adba61.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s-e7ynxc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/414/0%2Au1UoPgnHg5xjIFhb)

[![](img/038a73b45ea02538ad69e71a1f660e76.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--knq-8rsu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/87/0%2A26jTLfm18IM7qKem)

使用一个小脚本和代码，或者使用一个 iframe，可以非常容易地创建和添加小部件。这是 iframe 装载美国柴油零件的脸书时间表:

```
src="[https://www.facebook.com/plugins/page.php?href=https%3A%2F%2Fwww.facebook.com%2FUSDieselParts&tabs=timeline&width=500&height=500&small\_header=true&adapt\_container\_width=true&hide\_cover=true&show\_facepile=true&appId](https://www.facebook.com/plugins/page.php?href=https%3A%2F%2Fwww.facebook.com%2FUSDieselParts&tabs=timeline&width=500&height=500&small_header=true&adapt_container_width=true&hide_cover=true&show_facepile=true&appId)" align="center" width="500" height="500" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowTransparency="true" allow="encrypted-media"\> 
```

看起来没那么糟，这只是打给脸书的一个小电话…

### 真的只是一个要求吧？

为了检查脸书时间线小部件的性能，我创建了一个只有小部件的测试页面。在这个测试中，iframe 中加载了 4 个脸书帖子，只有第 3 个和第 4 个帖子有视频文件。

在测试页面上运行 WebPageTest 显示，这个简单的 iframe 代码产生了额外的 3.7 MB 内容(1.8 MB 视频！)，在 [Chrome](https://www.webpagetest.org/result/180821_P7_86437b0c37af60ae641c95c1f3c1fbd5/) 和[手机 Chrome](https://www.webpagetest.org/result/180821_QZ_ef338a7895c86ed285c83e6d6b28087a/) 上都有近 100 个请求。(注意:我会指出，但完全忽略这篇文章上下文中的 1.5 MB JavaScript…)。

这个小小的请求最终会产生大量的流量。

### 为什么正在下载视频文件？

脸书正在时间线内预加载视频内容，以最大限度地减少视频播放的启动时间。

### 实际被请求的是什么？

查看瀑布图，有许多视频请求—许多不同的视频文件:

[![](img/23a90b46274f1e8d67a3cd730fbcb9f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dQNWDhyy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2A6JpVwOQRYoVg5Zjy)

有 33 个视频字节数请求，这意味着每个请求只要求完整视频文件的一小部分。查看根文件名，在脸书时间线内总共有 9 个视频被下载。

但是等等！时间线中只有 2 个视频—为什么有 9 个视频被下载？？

有 2 个“主”视频，2 个音频轨道，其他 5 个视频是重复的视频-只是调整到不同的维度！深红色和蓝色线条表示“主”视频，浅红色和蓝色表示同一视频的“其他”版本。

[![](img/9d27cc2493062f26a78a0671b0b8bcbf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Lcs1M8Tl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2Ak6t1DOvKo7bAcHye)

主要的视频(和它们的音轨)都有 25-30%被脸书的小工具下载。“其他”版本看起来每个下载了大约 1KB，但是每个文件有 2 个请求。

### 视频尺寸

如果我们回头看看 iframe 代码，它被定义为 500 像素宽。然而,“蓝色”视频是以 1280 像素的宽度下载的——是页面上分配空间的两倍多。小部件可以下载大小更合适的 640×360 版本的视频(我们在跟踪中看到对该视频的两个请求),这将是 2.5 倍小(2.1 MB 对 800 KB)。

### 再现性:

这种效果很容易在任何脸书页面上重现。脸书有一个[插件生成器](https://developers.facebook.com/docs/plugins/page-plugin)，可以为任何页面创建 iframe 代码。简单地创建一个插件，在时间线中使用带有视频的脸书源，粘贴到一个页面上，然后测试结果(在 Chrome 中)。当我使用 Chrome 模拟 iPhone 时，这种效果不会出现，只有在 Android 和原生 Chrome 上才会出现。

### 发行范围:

在 HTTPArchive 中，有 116k 脸书时间线视频请求。它们出现在 5564 个网站上(其中 37000 个网站有视频，120 万个网站在总数据集中)。这意味着 15%的视频网站受到此问题的影响，移动 HTTPArchive 数据集中 0.5%的网站受到影响。

通过下载脸书时间轴上所有视频的 25-30 %,大量数据被用于(移动用户)他们可能永远不会从网页上加载的视频。

### 怎么办？

每当向您的网站添加第三方小工具时，您必须执行性能检查，以确保新功能不会影响页面的加载时间。要明白，在你的社交媒体帖子中出现视频会降低你的网站对 Chrome 和 Android 用户的访问速度，要权衡这个插件在你的页面上对这些用户的重要性。

感谢科林·本德尔审阅一份草稿。

*原载于 2018 年 8 月 21 日*[*【dougsillars.com】*](https://dougsillars.com/2018/08/21/excess-video-download-on-embedded-facebook-pages/)*。*
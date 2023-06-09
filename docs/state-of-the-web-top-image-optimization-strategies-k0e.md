# 网络现状:顶级图像优化策略

> 原文：<https://dev.to/dougsillars/state-of-the-web-top-image-optimization-strategies-k0e>

图像是网络的核心部分。美丽的图像会吸引客户更深入地了解你的网页试图讲述的故事——无论是新闻、娱乐还是电子商务。和图片一样重要的是——大尺寸的图片可能会影响相同页面的加载时间，导致客户放弃。事实上，HTTP 档案显示，移动网站平均约 50%是图像(以 KB 为单位):

[![](img/534171e18e24eca62dcea4863d20ca2f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YK0mP66j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2AP3whafUO6o9lZmqF)

这一比例多年来一直保持相对稳定，但与此同时，网页越来越大，这意味着图像也越来越大。事实上，年复一年，我们看到手机上的图片大小增加了 8%，而页面上的图片数量实际上减少了 2.6%:

[![](img/2ea6c37c44ed8821474f45b674c2c5d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--65rv53FS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2AnbATnxhp_6r2R0Cf)

[![](img/5f7311617c890f0c09d7e5a148d21e5d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E9J832FF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2Al20m3ZZPyK_pRH56)

这意味着随着时间的推移，手机网页上的图像越来越大。

### 怎样才能缩小图像尺寸？

有许多图像优化策略可以在不影响页面图像质量的情况下帮助简化图像的交付(关于详细的回顾，艾迪·奥斯马尼的《基本图像优化指南》是一个很好的参考[https://images.guide/](https://images.guide/))。在这篇文章中，我将重点介绍 4 种优化策略:延迟加载(ll)、图像优化(IO)、响应图像(RI)和图像格式(IF)。这四个最佳实践包含在 Chrome Lighthouse 网站审计中并不奇怪，这些审计可以在 HTTP 档案中找到——这使我们能够详细了解这些优化。

### 灯塔定义

查看数据时，理解数据实际上在告诉我们什么是很重要的。测试着眼于:

*   **惰性加载(LL)** : Lighthouse 识别屏幕外的图像。如果这些图像在交互时间之前被加载，测试失败。如果在页面交互之前请求屏幕外的图像，则测试通过。通过推迟那些不会立即出现的图片的加载，页面的交互速度会更快。
*   **图像优化(IO)** :测试页面上的所有 JPEG(且仅 JPEG)图像，以查看它们的质量是否为 85%(或更低)。如果图像提供了 85%,测试通过。谷歌推荐 85%的质量设置作为图像质量和图像大小的平衡。
*   **响应图像(RI):** Lighthouse 计算渲染图像的大小，并将其与下载的图像进行比较。如果屏幕上的图像比下载的图像小 25KB 以上，则审核失败。这里的目标是向请求文件的设备提供适当尺寸的图像(小型智能手机上没有桌面视网膜大小的图像)。
*   **图像格式(IF)** : Lighthouse 检查下一代格式(JPEG2000，WebP)是否会在任何给定的图像上节省超过 8 KB。因为所有的移动 HTTP 存档测试都是在模拟的 Chrome 上运行的，所以选择的格式是 WebP。

### 数据采集

HTTP Archive 每两周收集一次前 500，000 个站点的 WebPageTest 跟踪，包括 Lighthouse 数据(在移动运行中)。使用 BigQuery，我们可以查询这些信息，并开始我们的分析:

> *选择*
> 
> *网址，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . off screen-images . score "))off screen score，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . off screen-images . extended info . value . waste DMS "))waste DMS，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . off screen-images . extended info . value . waste dkb "))waste dkb，*
> 
> *JSON_EXTRACT(report，" $ . audits . off screen-images . extended info . value . results ")wasted results，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . uses-optimized-images . score "))optimagesScore，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . uses-optimized-images . extended info . value . waste DMS "))optimgwastedms，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . uses-optimized-images . extended info . value . waste dkb "))optimgwastedkb，*
> 
> *JSON_EXTRACT(report，" $ . audits . uses-optimized-images . extended info . value . results ")optimgwastedresults，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . uses-responsive-images . score "))repimagesScore，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . uses-responsive-images . extended info . value . waste DMS "))respigwastdms，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . uses-responsive-images . extended info . value . waste dkb "))respigwastedkb，*
> 
> *JSON_EXTRACT(report，" $ . audits . uses-responsive-images . extended info . value . results ")respimgwastedresults，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . uses-webp-images . score "))webpimagesScore，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . uses-webp-images . extended info . value . waste DMS "))webpimgwastedms，*
> 
> *integer(JSON_EXTRACT(report，" $ . audits . uses-webp-images . extended info . value . waste dkb "))webpimgwastedkb，*
> 
> *JSON_EXTRACT(report，" $ . audits . uses-webp-images . extended info . value . results ")webpimgwastedresults，*
> 
> *来自*
> 
> *【httparchive:light house . 2018 _ 03 _ 15 _ mobile】*

让我们解开这些线在说什么:

> *integer(JSON_EXTRACT(report，" $ . audits . off screen-images . score "))off screen score*

由于' report '列中的数据是 JSON，所以结果都是字符串。因为我们希望将结果作为数字来处理，所以我包装了 integer()参数来将它们转换成值。JSON_EXTRACT 获取报告(Lighthouse JSON)并提取子属性‘audits-> off screen-images-> score’。

***注意:*** 运行该查询需要 131 GB(您每月 1TB 的免费查询)。保罗·卡尔瓦诺已经创建了一些[样本查询](https://bigquery.cloud.google.com/dataset/api-project-658924507062:httparchive_sampleset)，这些样本查询对于在大型数据集上运行之前优化您的查询来说要小得多。一旦你优化了你的查询——把它保存在一个表中——这样你就可以改进和运行更多的查询——而不会破坏你的大查询“银行”我的表只有 5.8 GB——对于运行查询来说，这是一个非常小的样本大小。

### 图像优化

四个优化都是打分的，可能的分数是 0(差)，65(一般)，90，100(好)。我们可以确定每个审核的分数:

[![](img/b77ff3d92115933229a7916b3fbfb856.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DLM8dQq0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2AvTRZwuCkzmC6CiF1)

有趣的是，在所有的审计中，超过 75%的网站得分为 0 或 100。同样清楚的是，优化图像(43%得分 100)和响应图像(57%得分 100)比延迟加载(22%)或图像格式(16%)实现得更频繁。

70%的网站(310k)至少有一个图像优化得分为 100。这(可悲地)意味着 30% (13 万)的网站没有 100 分。

### 一次失败的代价是什么？

灯塔报告用两个指标估计每个故障的成本:以 KB(通过网络发送的额外数据)和毫秒(与优化版本相比，加载资源花费的成本)为单位的成本。仅查看每次审核得分为 0(失败)的站点，我们可以找到改进得分的中间值:

[![](img/fb0ac1b7c893560256c747402fadb5d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vg96rL00--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2Ab6CL1CDQkkznkoO-)

这些真是令人印象深刻的改进！此图表显示，3G 网络的中值改善在 2.7-4.1 秒之间。当然，在较慢的网络上，速度改善会大得多(任何网络都可以节省 KB)。

### 优化分配

将图像优化应用到您的网站是附加的，这意味着实现一个以上的优化将导致更大的节省。我们可以确定有多少站点实现了多重优化(并且每次优化都得了 100 分)。

[![](img/7aa1e880333ebbc6f1b35c25b09cd23e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w9rn2lfG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2Aq8NjnZkOJF8srNoo)

30%的网页通过了四项图像测试中的零项。超过 70%的网站通不过两次或两次以上的测试(0、1 或 2 分满分为 100 分)。

我们可以看到这些测试的分类:

### 灯塔审计 100 分的百分比:

[![](img/8fbb47977fe27c9f740af034a828008f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fdPlRxjw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2AJwUJlAb6U4PCNu4Y)

在有优化的站点中，最常见的交集是优化图像和响应图像(有或没有延迟加载和 WebP。有趣的是，维恩图中人数最多的部分是 21%的网站，它们只通过了响应图像审核。也许这是由于“移动优先”网站的推动？

下一个最受欢迎的是响应和优化(11%)，其次是优化图像(仅)。这三个板块占据了移动互联网 40%以上的份额。下一个最常见的实现是所有四个审计都以 8%的比率通过。

这篇文章顶部的图表显示，响应性和优化是最常见的，惰性加载和图像格式没有得到充分利用。它还显示，延迟加载和图像格式大多出现在第三次或第四次优化中(在有很多重叠的部分)，这表明它们是随着时间的推移而进行的额外优化。

### 多次优化的站点

HTTP 档案中的数据允许我们根据通过测试的数量计算潜在的中值节省:

[![](img/399a9fb541fc4674006545ae24662f98.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BAgJdvzZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2Ay3YdCtJ-t3towg9z)

正如预期的那样，进一步的优化改善了加载时间，但是每一个额外的测试提供的好处稍微少一些。

结合以上两个部分的数据:

[![](img/073a03eaa16cccf566aeb9f231b6fa60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tnn5-sua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2A5-IojKarGoosOOi2)

31%的网站未能通过所有四项灯塔图片审核。解决这些故障有可能通过映像优化将这些站点的速度提高 7 秒(和 1.1 MB)。超过 70%的 web 没有通过 2 次或更多的测试，这表明至少有 1.3 秒的潜在加速(和超过 168KB 的节省)。

### 对比实际加载时间

因为这些数据都是从真实页面负载测试中获得的，所以我们也可以获得这些站点的实际加载时间。减去潜在的优化表明，图像优化网站将获得巨大的速度增长，为一个重要的网页横截面。

[![](img/d501bf1591cf0e72b4235cc92f14d95a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pV1ht_E2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2AuKu9DUfk6K7IXc5o)

该表给出了通过不同数量 Lighthouse 审核的网站在页面加载时间和 KB 方面的改进百分比。例如，一个未能通过所有 4 项审核的站点(31.23%的站点)将会发现加载时间的中位数上升了 26%，而图像 KB 下降了约 60%。已经通过 3 次审核的站点(只需修复一次)的加载时间平均提高了 2.5%，图像 KB 下降了 25%。

我们还可以比较通过所有 4 项审核的网站(8%的网站)和没有通过所有 4 项审核的网站(10%的网站)的加载时间:

[![](img/e68e1103aedb02a470ed9fd5900da3d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mKea0g4S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2AZGl83AIiAhrspLRA)

现在，这可能不是一个公平的比较，因为我们可以看到，通过所有四个审计的网站是非常轻量级的，而那些失败的网站是巨大的(移动设备上 4.3 MB 的页面！).但是，通过建议的优化的中间站点加载时间为 30 秒(！idspnonenote)。？！)比未通过所有四项审核的网站的中间值要快。

**结论**

图像占据了移动网络加载时间和吨位的很大一部分。Lighthouse 审核了 4 个映像最佳实践，只有 8%的人通过了所有这些实践(10.4%的人没有通过所有四个实践！).这些优化节省的中间值为 2.7-4.1 秒和 400-600 KB 数据。随着越来越多的优化，网站变得更快，使用更少的数据。

当每一秒都很重要的时候，很明显，图像优化是一种未被充分利用的，但是非常有效的提高网站性能的方法。

要用 Lighthouse 测试你的网站，使用 WebPageTest 或 Chrome DevTools，找出你的网站在这个光谱中的位置！

感谢 Rick Viscomi 阅读并提供精彩评论。在 HTTP 存档中交叉发布。

*原载于 2018 年 5 月 21 日*[*【dougsillars.com】*](https://dougsillars.com/2018/05/21/state-of-the-web-top-image-optimization-strategies/)*。*
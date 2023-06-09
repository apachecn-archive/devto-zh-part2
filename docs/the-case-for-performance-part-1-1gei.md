# 绩效案例(第一部分)

> 原文：<https://dev.to/joshua/the-case-for-performance-part-1-1gei>

注意:这是有意义的性能指标系列的第 1 部分。

一个网站在移动设备上的平均页面加载时间是 15.6 秒。

15.6 秒。

想想吧。

想象一下，如果你拿起一份报纸或一本书，这些字母看不见，或者跳来跳去，整整 15.6 秒才静止不动。

首先，你可能会感到震惊，书籍通常不会这样做。在那之后，你可能会在 10 秒左右放弃。

对于很多试图在移动设备上使用网络的人来说，这是一种完全正常的体验。这是一种从根本上破碎的体验，我们可以修复它。

* * *

缓慢加载网站的痛苦是我一生中最强烈的感受。我过去住在澳大利亚乡下的一个相当小的城镇里。

澳大利亚并不以网速极快而闻名。大约在 2009 年我住在那里的时候，他们做了一个测试。总理说

> 如果(反对派)如愿以偿，澳大利亚人今后将只能使用信鸽，而不是接入具有国际竞争力的宽带网络

所以，他们测试了它。他们将一只带有 700 兆字节 usb 驱动器的信鸽从中西部飞到悉尼，大约 100 公里或 60 英里。

[![Carrier pigeon](img/6d0f53f34b5da0bf434749aa32fef073.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FzsdDWH4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p3pav6q16ajzyk5ko7ou.jpg)

信鸽赢了，1 小时 5 分钟。他们派了一辆车，用了 2 小时 10 分钟。而互联网连它都没做成，退了两次。

这是网页每天加载的环境。但是，那是在 2009 年，所以从那以后情况肯定变好了，对吗？

幸运的是，他们做到了！随着时间的推移，互联网速度有所提高:

[![graph of internet speeds](img/2151b1340faed7e59e3d4319634d2a5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YHpoHAs8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hb9ven2oj2vch0viz5pn.png) 
[参考](https://www.statista.com/statistics/616210/average-internet-connection-speed-in-the-us/)

除此之外，CPU 速度也在逐年提高:

[![graph of CPU speeds](img/882624ea3b9675222f7c738078c97d36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UQ_7xBx2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3bgcl3uhe779rtojefrj.png)

但是唉！网络一点也没有变快:

[![graph of page load times](img/c6f93be1ef19bdc856b4d1d0b3a8a13c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E30fJ68f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8dpd6xedi6huufyq39j3.png) 
[参考](https://httparchive.org/reports/state-of-javascript?start=earliest&end=latest&view=list)

这是为什么呢？我们一直在改进我们的技术，但在过去几年里，网速基本保持不变。

当我们在高速公路上增加额外的车道时，类似的事情也会发生——这种现象被称为“诱导需求”效应。研究发现，在高速公路上增加额外的车道实际上根本不会改善交通状况——有时还会让情况变得更糟。公路通行能力增加 1%会导致需求增加 1.1%。公路越多，人们越想使用它。

类似的事情也发生在网络上。随着技术的进步，我们对媒体、新体验以及最终整个包大小的需求也在增长。

[![graph of increasing bundle size](img/806e73b2b8eb2183b8e87a170b59e15a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9FJ-Hw-E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6rc0wfgp4rqk5jz1h7se.png)

[参考](https://httparchive.org/reports/state-of-javascript?start=earliest&end=latest&view=list)

所以，这个问题并没有好转。但是为了我们的用户，它需要。

## 性能的影响

这有三个主要方面的影响:它影响业务目标、用户幸福，最终影响整个世界。

### 业务目标

[![A person on a laptop in a conference room](img/cfcf80bbc086f44d1032579efbc4e415.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BBxWW8qb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1hsvjqjhegmjmkez5var.png)

绩效直接等同于业务目标和金钱。

微软进行的研究发现，必应延迟一秒钟就会导致收入下降 2.8%。两秒钟的延迟导致 4.3%的下降。

沃尔玛发现，加载时间每缩短一秒，转化率就会提高 2%，每 100 毫秒转化率就会提高 1%。

谷歌发现，移动网站在 5 秒内加载的出版商从移动广告收入中获得的收入是 19 秒内加载的两倍。

各种网站都有很多类似的结果。

因此，在改进性能上投入的时间直接带来了金钱。单单基于这一点，就已经值得投资业绩了。

### 用户幸福感

[![A frustrated user on a laptop](img/a31dc3c119c3a4feb1469e3e6f71c620.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IqrQVntn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/muogukc13mpwtylbxvst.png)

[人机交互研究](https://www.nngroup.com/articles/response-times-3-important-limits/)显示，从用户的角度来看，有三大类任务:

*   <= 0.1s are **中的任务【即时】**任务。用户感觉系统对他们的输入立即做出反应
*   <= 1s are **流程中的任务**任务。用户能够在浏览任务时保持流畅感。他们会注意到延迟，但不会影响注意力
*   超过 10 秒的任务导致**任务切换**

然而，从 1 秒到 10 秒，用户越来越感到沮丧。当用户切换任务时，用户肯定会感到沮丧——影响他们对你的产品的感觉，让他们不高兴。

### 世界

[![A picture of earth](img/bcc681095b4d46384cfeac267c2d77ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--249pa36o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cfi2bt76t7sly2zt4gwl.png)

youtube 的一个版本被创建，叫做“ [Youtube 羽毛](https://wpostats.com/2015/11/11/youtube-feather.html)”。这个版本的 youtube 在页面重量方面轻了 90%,并且只包含了 youtube 加载的最低限度。创作者想看看这是否会改善该版本的平均加载时间，所以他们选择了一小部分流量。

奇怪的是，他们发现平均加载时间竟然上升了！

经过调查，他们发现来自东南亚、南美、非洲和 Sibera 的流量有所增加，这些地区的互联网连接一向很差。这些实际上是新用户——以前根本无法使用 Youtube 的用户，因为加载速度非常慢！

## 接下来呢？

因此，性能是一个问题，也是一个值得解决的问题。然而，如果你正在阅读这篇文章，很可能你已经认为性能是一个问题。

那我们为什么还没修好它？

我听到了一些常见的回答:

1.  “放弃”:“这是一个很难的问题，我们无法解决它”
2.  “否认”:“没关系，我们的用户有快速互联网和 cpu 的，对不对？”
3.  “循环”:“哦，上帝，你是对的，让我们修理它”->“我们修理它！”->“天哪，又可怕了”

所以，性能注定是差的，直到宇宙不可避免的热寂。不断膨胀的熵将占据一切。混乱至高无上。我们控制不了，成绩总会退步。

或者，我们试试。

有意义的绩效指标可以拯救你。

我们需要一种*有意义的*方式来衡量业绩，并做出我们将永远坚持的承诺。

* * *
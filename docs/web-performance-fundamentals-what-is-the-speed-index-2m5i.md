# Web 性能基础:什么是速度指数？

> 原文：<https://dev.to/borisschapira/web-performance-fundamentals-what-is-the-speed-index-2m5i>

在 Dareboost，我们有一个座右铭:你不能用一个单一的指标来评估你网站的整体表现！然而，有些比其他的更具综合性和相关性。我们认为速度指数是最重要的分析指标之一。让我们来发现为什么:速度指数表明什么？如何衡量？如何正确解读和使用？

你的网页对你的访问者来说有多快？这是在衡量你的网络性能时你试图回答的主要问题。然而，这绝对不是最容易做到的事情！
最著名的指标(例如:[满载时间](https://www.dareboost.com/en/doc/website-speed-test/metrics/fully-loaded))都不允许我们捕捉关于用户体验的相关和全面的信息。速度指数等新指标的出现带来了更好的结果。让我们来看一下使这个指标成为评估用户体验速度的一个好方法的主要特征。

## 速度指数不是(真正的)里程碑计时

这是你必须知道的第一件事，这使速度指数成为 webperf 领域中一个非常独特的指标:与你可能遇到的大多数其他指标不同，速度指数不是网页加载时间线上的另一个里程碑。虽然速度指数通常以毫秒表示，但它不是一个里程碑式的计时，而是一种计算结果。这个计算是为了什么？来转录网页的平均渲染速度及其视觉进度。

我们将在后面讨论视觉进步的评估，但是请记住第一个概念:速度指数与其说是时间的度量，不如说是速度的度量，它提供了网页内容呈现速度的综合得分。因此，速度指数是一个非常实用的指标，可以用来比较不同页面的体验，并跟踪用户对其性能的感知。

## 速度指数关注的是访问者所看到的

速度指数的另一个基本特征是:它关注访问者看到的内容。而不是网页浏览器加载了什么…这个特性实际上使得速度指数比完全加载时间更能揭示用户体验。

> 随着页面的增长和加载大量用户不可见或屏幕外(折叠下方)的内容，即使用户可见的内容早已呈现，到达加载事件的时间也会延长。 <cite>Patrick Meenan，“[介绍速度指数](https://sites.google.com/a/webpagetest.org/docs/using-webpagetest/metrics/speed-index)”</cite>

实际上，速度指数只考虑了页面的折叠部分(用户直接可见)。因此，该度量高度依赖于视口大小。正如下面的比较所表明的那样(相同的网页在相同的网络连接下测试，但是不同的屏幕分辨率)，你需要用你的访问者当前最常用的视窗尺寸来衡量你的网页的速度指数。

<figure>

[![Different viewport sizes = different Speed Index!](img/e4c2f7a3f6681d5e56060bf1c053f473.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xrtbJqEB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ocb5ak9m4yoppmmkc651.png)

<figcaption>Different viewport sizes = different Speed Index!</figcaption>

</figure>

当然，速度指数不仅仅取决于视口大小。像所有其他计时一样，它也依赖于与用户的网络连接相关的因素，例如(带宽、延迟)。

## 测量视觉进度

视觉进度测量基于网页加载的视频记录，从视觉进度测量执行速度指数的计算。该视频由您正在使用的测量工具捕获。

其他一些指标基于视频分析。让我们简单介绍一下它们，以便更好地理解速度指数是如何计算的，以及它提供了哪些额外信息:

*   [**开始渲染**](https://www.dareboost.com/en/doc/website-speed-test/metrics/start-render) 表示第一个元素在用户屏幕上可见的时刻(准确地说是用户的视口，因为视频分析只涉及被分析网页的可见部分)。事实上，对 UX 来说，这个有趣的时机标志着用户一个完整空白页的结束。但是要注意:第一个要呈现的可见元素不一定是重要的元素(因为它可能是背景色或任何次要的视觉信息)。
*   **视觉完成**表示视区以最终形式显示所需的时间。下面是来自[我们的用户手册](https://www.dareboost.com/en/doc/website-speed-test/metrics/visually-complete)::*的一些解释，当 Dareboost 检测到与页面加载相关的流量结束时，在我们的分析结束时捕获最终呈现状态。通过分析视频，Dareboost 然后识别这个最终渲染状态何时已经完成*。

对于用户体验，我们有两个有趣的里程碑(渲染开始的速度和完成的速度)。然而，我们仍然错过了一个重要的信息:在这两个计时之间发生了什么？让我们举一个极端的例子来搞清楚:2 个网页完全一样的开始渲染(500ms) et 视觉完成(2s)。

[![Filmstrip example](img/913e0561501b12685e9bc9431898529b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eNxMNzz---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dtcerrl4p0msuj3pt43e.png)

*   A 页逐渐出现。其 75%的内容在开始渲染后不到 300 毫秒内显示。然后一些细节以较慢的速度出现，
*   页面 B 几乎长时间保持空白，并在 2 秒钟内突然显示其所有元素。

毫无疑问:用户会发现页面 A 的显示速度比页面 B 快得多，而你——测试人员——从开始渲染到视觉上完成计时都无法发现两个页面之间的任何速度差距……这正是速度指数的巨大帮助！

## 速度指数是如何计算的？

首先，将页面加载的视频记录分解成帧。对这些帧中的每一帧进行检查，并指定渲染完成的百分比。为了确定一个帧的进度比率，将该帧与最终渲染状态进行比较。

该结果是被测试网页的视觉进度比率的逐帧演变。每个 Dareboost 测试结果都包括一个页面加载的幻灯片(见下图)。

[![Visual progress frame by frame](img/c55c898a820104159e7f09d93bf139ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kZ1_k5nI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ppcoqekmsnwa4een54zk.png)

随着时间的推移，视觉进度也可以用图表来表示，X 轴是时间，Y 轴是进度百分比。

<figure>

[![Speed Index from the visual progress graph](img/4bc87c6585ab1b2832dd15aa1326ca60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NiQ6SdUf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mayuheg4zf36eu04451m.png)

<figcaption></figcaption>

</figure>

速度指数实际上是线以上的区域，代表页面随时间推移的空白区域。该区域越小，页面呈现速度越快。所以你的速度指数越小，你的页面就越快！

这可以用下面的公式来翻译(来源:WebPageTest):

[![Speed Index formula](img/0283ce783c10e1e35a7c7adf69495630.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--goHzRHwr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yb2q054moik47tjnl062.png)

## 如何解读速度指数？

现在你知道速度指数是怎么计算的了。那么你可以猜猜怎么解读:速度指数越低，被测页面被感知的速度越快。让我们详细介绍一下这条捷径，以确保您不会错过任何东西:

*低速指数>视觉进度图上方的小区域>页面的未呈现部分随着时间的推移被快速最小化>页面折叠上方的大部分元素以最小的延迟显示>页面看起来呈现给用户的速度很快*

那价值呢？什么可以被认为是“快”的速度指数？谷歌 Chrome 开发者倡导者保罗·爱尔兰(Paul Irish)建议速度指数小于 1000(T1)，对于某些类型的页面和浏览环境来说，这个目标可能很难实现。例如，你应该预料到一个新闻网站的主页会有一个高速的索引，但浏览时网络连接却很差！

[![Speed Index benchmark DiG Lisbon 2017](img/bb5a2663fef466264c3680c17b728686.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kD0bedup--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cs24cepj5wubuzmmbp9z.png)

举几个具体的例子，下面是(上图)60 多家欧洲领先出版商的 Dareboost 基准测试结果。这些速度指数值是根据以下上下文测量的:一名法国游客(位于巴黎)，通过一个具有平均 web 连接(8Mbps 下行带宽，50 毫秒延迟)的桌面浏览网页。

作为一个结论，开始解释你的速度指数的最好方法之一可能是通过与你的竞争对手的网页进行比较和基准测试:如果你得到了明显更高的速度指数，那么考虑你的网站可能会因为比你的竞争对手更差的用户体验而受到惩罚...

## 尽管如此，速度指标还是有一定的局限性

我们已经知道了速度指数对于评估你的网页在用户屏幕上显示的速度有多大帮助，现在让我们来谈谈它的局限性。

*   首先，请记住，速度指数对视口大小很敏感。因此，您应该定义一系列与您的目标受众相匹配的测试配置。
*   我们刚刚看到，速度指数计算是基于正在显示的页面的最终状态(每一帧的视觉进度与最终状态进行比较)。在某些情况下，这可能会导致一些问题:一些动画(如旋转木马、自动幻灯片、视频)可能会篡改这种最终状态检测，因为当页面看起来完全加载到用户时，呈现可能会改变几次。幸运的是，Dareboost 已经开发了几个选项来[停用这些动画](https://www.dareboost.com/en/doc/website-speed-test/settings/animations-slider)，然后在大多数情况下稳定您的结果。

为了总结速度指数的局限性，让我们提醒自己这篇文章的第一句话:你的整体网络性能不能用一个单一的指标来衡量。例如，速度指数不会给你任何关于用户与你的界面交互能力的信息。根据你的内容是高度文本化的还是鼓励互动的(比如表单)，速度指数对用户的速度感知不会有同样的影响。因此，请记住，即使速度指数为您提供了独有的好处，您也应该将它与其他指标结合使用，以全面了解您的 web 性能。当然，还要确定最佳的优化方法！

*由 Philippe Guilbert 原创于 [Dareboost 的博客](https://blog.dareboost.com/en/)。*

感谢[查理斯·瓦扎克](https://twitter.com/vazac)、[德里克·约翰逊](https://twitter.com/derekjohnson)和[安迪·戴维斯](https://twitter.com/AndyDavies)的校对。
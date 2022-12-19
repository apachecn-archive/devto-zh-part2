# DSA、比特币和计算的未来

> 原文：<https://dev.to/bearguy/dsas-bitcoin-and-the-future-of-computing-4178>

* * *

这篇博客的灵感来自约翰·汉尼斯在 io18 上关于计算未来的演讲。你可以在这里得到他的演讲的更有力的解释:[这篇](https://www.eejournal.com/article/fifty-or-sixty-years-of-processor-developmentfor-this/)文章被证明是非常有帮助的

人们很快批评比特币挖矿正在全球范围内占用大量电力(30 TW/hr)。我不知道谁会说这是理想的，但是比特币网络凸显了工业计算在能源消耗方面的一个重要变化。随着工业比特币挖矿的兴起，许多人对这些计算架构的新实现采取了非常反动的立场；谴责它的包容性，并认为它是“集中的”,因此对开放接入、分散式网络不利。然而，这是一种倒退的心态，我认为它缺乏对现代计算环境的更广泛的分析和理解。这就是为什么工业比特币采矿展示了计算的未来，以及为什么它变得越来越必要。

## 计算公理的终结

[![END OF AN ERA](../Images/f36d0d2f43d42a570ff892b8a567fafb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1NpEULeZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.eejournal.com/wp-content/uploads/2018/03/Death-of-Dennard-Scaling-and-Moores-Law.png) 
我相信你们很多人都听说过[摩尔定律](https://en.wikipedia.org/wiki/Moore%27s_law)在过去的几十年里一直在放缓。具体而言，自 2015 年以来，处理器性能改善率已经下降到每年只有 3%。过去，我们可以预期处理器速度每 1.5 到 3 年翻一番，现在需要 20 年。然而，这只是计算创新在过去几年中遇到的最新障碍。在此之前，另一个已经不太适用的规则叫做[丹纳德缩放](https://en.wikipedia.org/wiki/Dennard_scaling)。该定律指出，随着晶体管变得越来越小，它们的功率密度保持不变，因此处理器的功率使用与所述处理器的面积成比例。这意味着在很长一段时间里，功耗不是芯片设计中的一个问题，功耗保持不变，因为它将平均分布在额外的晶体管上。

[![the end of dennard scaling](../Images/57038831d12df36b246755d06ecfb2de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ukE1ZV0X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.researchgate.net/profile/Luke_Shulenburger/publication/301650491/figure/fig24/AS:355250444750853%401461709714584/The-end-of-Dennard-Scaling-44.png)

这个定律在现代芯片设计中越来越不适用。随着对计算能力要求的提高，我们看到芯片所需的能量也在不断增加。今天，您的标准 i86 英特尔处理器实际上会降低其计算能力，以免烧毁！这在几年前是闻所未闻的，但现在是一个相当普遍的现象。通常，在当今的现代云计算系统中，能源使用已经成为一个 **巨大的** 问题。每个人都喜欢指责比特币的大量能源使用，但没有多少人知道，在你的标准英特尔芯片上并行进行的大约 **25%** 的计算被浪费了。

[![](../Images/6c89f608eee3f89de3db45e225b66aed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0FICrhiS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.slideplayer.com/26/8678317/slides/slide_31.jpg) 伴随这种代价高昂的错误而来的是巨大的能源浪费。即使不考虑这个等式中的[阿姆达尔定律](https://en.wikipedia.org/wiki/Amdahl%27s_law)，我们也应该清楚，我们不仅在能力上达到了极限，而且在使用并行性作为性能王牌方面也达到了极限。随着这些技术变得越来越低效，我们应该如何满足日益增长的计算能力需求？

### 特定领域架构的兴起

许多人提出的解决方案是断言，与只做好几件事的机器相比，我们的硬件不应该试图有效地做每件事。为了展示这一点，我们可以看两个对计算有巨大需求的现代网络的例子。

如前所述，在过去十年中，比特币的网络使用量大幅增加。为了竞争，矿工们需要制造优化的硬件来开采区块并获得区块奖励。矿工们创造了定制的 ASIC(专用集成电路)，能够以极高的速度计算比特币哈希算法(double SHA256)。批评者可能会冷嘲热讽地称这一创新完全是由矿工的自私所激发的，但鉴于当今现代计算的局限性，这是为数不多的允许比特币网络处理全球数百万人金融服务的解决方案之一。

ASIC 架构对于像哈希这样简单的事情很有效，但是对于更复杂的计算呢？谷歌的计算需求可能是世界上最复杂的，更不用说优化哈希算法了。他们的大多数产品已经变得越来越依赖人工智能，因此他们最大的挑战已经成为大规模处理机器学习。作为回应，谷歌创建了 TPU(张量处理单元)，它使用一块硅片上的 65，536 个乘/累加(MAC)单元的大规模阵列来加速 DNN(深度神经网络)的执行。这些小家伙每秒钟可以产生高达 92 万亿次运算，其运行速度比现代 CPU 快 30 倍，比 GPU 快 15 倍，而功耗仍然比它们都低。

[![Google's TPU](../Images/ea1c21f4133c6f96f65067293eb65c28.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xBhe9ktr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://regmedia.co.uk/2018/05/08/tpu.jpg)

这两种设计都是特定领域架构兴起的一个例子，这种架构是对计算机系统的一种改变，是推动计算机行业进一步发展所必需的。由于硬件进步的限制，剩下的唯一改进途径来自于限制硬件的操作范围，以便看到性能的提高。对于区块链网络来说，这是更大的激励来提高性能，不仅展示新兴分散网络的有效性，而且首先是为了那些参与网络的人的自身利益。

### 结论

对于分散空间中的许多人来说，有批评者会指出比特币采矿 ASICs 是对分散网络概念的攻击。从我们在过去几年中看到的计算机体系结构的变化来看，任何认真考虑提高计算机系统性能的人都必须面对这样一个现实，即特定领域的体系结构是必要的。这些性能提升是满足需求的必要条件，因为区块链网络将继续在提供最佳服务方面展开竞争，并进行扩展以满足超过 32 亿在线用户的需求。不允许创建定制硬件来满足需求的通用系统不可能满足这种需求。

* * *

感谢阅读到最后！请务必关注我的 Twitter @speterkins
原文发表于 bearguy.io
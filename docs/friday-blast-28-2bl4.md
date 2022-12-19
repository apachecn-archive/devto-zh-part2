# 星期五爆炸#28

> 原文：<https://dev.to/horia141/friday-blast-28-2bl4>

关注概率数据结构，本周投入了一些比特币。

[网络分析和数据挖掘的概率数据结构(2012)](https://highlyscalable.wordpress.com/2012/05/01/probabilistic-structures-web-analytics-data-mining/)——一篇我想看很久的文章。它涵盖了 Bloom Filteres，HyperLogLog 计数器，Count-Min sketch 和所有这些好东西，它也有一堆应用的例子和真实世界的数字。有一堆公式，但没有证据或直觉支持它们为什么会发生。总的来说，这是一项艰巨的任务，因为要涵盖许多非直观的材料。

[草图数据结构(2010)](http://lkozma.net/blog/sketching-data-structures/)——只是对 Bloom filters 和 count min 草图的一个小介绍，有一些类比。

[概率数据结构对决:布谷鸟滤波器 vs 布鲁姆滤波器(2016)](http://blog.fastforwardlabs.com/2016/11/23/probabilistic-data-structure-showdown-cuckoo.html) -两者对比及布谷鸟滤波器介绍。使用的布隆过滤器是计数过滤器，允许删除。这是一个比常规数据结构更大的数据结构，但是它可以和布谷鸟过滤器进行公平的比较。

[随叫随到(2017)](https://increment.com/on-call/on-call-at-any-size/) -为 10 人、11-100 人、101-1000 人、1001-10000 人和 10000+工程师的公司提供随叫随到的实践。非常有趣的是，随叫随到和保持系统正常运行的本质是如何从小变大的。在这个范围的一端，有大量的手动调整，而在另一端，基本上是对系统应该做什么的统计理解。失败，失败的负荷和负荷。

[为 StackOverflow、Enterprise 和 Stack Exchange 站点更新 navifation(2018)](https://stackoverflow.blog/2018/02/08/information-architecture-navigating-stack-overflow-enterprise-stack-exchange-sites/)-概述最近对 SO 的 UI 更改及其背后的流程。关于即将推出的[频道](https://stackoverflow.com/channels)功能的一些细节。

[比特币协议实际上是如何工作的(2013 年)](http://www.michaelnielsen.org/ddi/how-the-bitcoin-protocol-actually-works/)——尽管我很早就知道比特币，但我并没有花时间去正确理解它。这是一个遗憾，因为即使粗略地看了一眼，也表明这是非常酷的技术。除了围绕它的所有炒作和狂热。作者是迈克尔·尼尔森(Michael Nielsen)，他是一位博学的人，在量子计算、深度学习、网络理论和显然的分布式系统方面开展工作。这篇文章没有令人失望——它非常清晰地展示了人们可能希望从数字货币中得到什么，以及如何去做——最终以比特币告终。
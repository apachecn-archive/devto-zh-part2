# Golang 中的快速优先级队列:分层队列

> 原文：<https://dev.to/bgadrian/fast-priority-queues-in-golang-hierarchical-queue-2me8>

> 本文最初发表在我的网站[https://coder . today/fast-priority-queues-in-go-hierarchical-queue-86 daf 6a 7553 a](https://coder.today/fast-priority-queues-in-go-hierarchical-queue-86daf6a7553a)

编写 O(1)高性能数据结构是一个多篇系列文章:

1.  [分层队列](https://coder.today/fast-priority-queues-in-go-hierarchical-queue-86daf6a7553a)(本文)

2.  [分层堆](https://coder.today/fast-priority-queues-in-golang-hierarchical-heap-92960edd4aa9)

3.  阶梯队列(很快)

    > 一个[优先级队列](https://en.wikipedia.org/wiki/Priority_queue)是一个抽象数据结构，用来存储有优先级的值(任何数据)。可以随时插入任何优先级的数据，但只能取出优先级最高的值。

[![Magic](../Images/5c0cd327af2a6e2f0878df007de1b6bd.png) ](https://cdn-images-1.medium.com/max/2000/1*P5umJ1Wdc-qqRVtWOMyMIA.gif) *魔法*

## 何时使用优先队列⁉

我承认，我没有在我的应用程序中使用它们中的很多，但是有一些[问题为它提供了完美的](https://www.quora.com/Where-is-priority-queue-being-used):

*   类选——任何基于分数/重要性的处理

*   排序—当您的数据具有特定的处理/分析顺序时，例如:在 MAU 之前处理 DAU KPI

*   事件驱动模拟—优先级可以是时间戳

*   图形搜索

*   负载平衡—优先级与负载相反

## 婴儿步伐

我和 Go 开始了漫长的最后一段[关系。我不想让我的训练白费，所以我开始寻找高性能的数据结构来实现(现在还没有)。经过几次搜索，我最终得到了这张图片，](https://coder.today/go-go-go-flash-bang-d66f4c42eb7c)[摘自一本书](https://www.researchgate.net/publication/222685597_Revisiting_priority_queues_for_image_analysis)

[![](../Images/dad76157c17f8918440e4db14fd372fc.png)T2】](https://cdn-images-1.medium.com/max/2210/1*3AkHiTG8E9hpnN2r3J9NZg.png)

这两条底线引起了我的兴趣，它们是 O(1)多结构优先级队列。首先是分层堆，它是基于分层队列的，所以让我们深入研究一下。

[![](../Images/dbc0cbc5f0c10305573bb359400fabea.png)T2】](https://cdn-images-1.medium.com/max/2000/1*vmpxla2vG2b5oxd_jxTMOQ.png)

## 分层队列

我找不到这个结构的任何开源代码或伪代码，如果你发现任何问题，请让我知道。

> 当优先级值限于小整数时(例如，当数字图像离开成像传感器时，数字图像通常具有 8 位或 12 位整数作为像素值)，可以为每个可能的优先级值分配一个 FIFO 队列(桶)。一个数组包含一个指向每个桶的指针，当一个元素入队时，可以使用优先级值直接索引正确的桶。入队和出队都是 O(1)运算。

用于算法和片段的论文:

*   [重新审视图像分析的优先级队列，Cris L. Luengo Hendriks](http://www.cb.uu.se/~cris/Documents/Luengo2010a_preprint.pdf)

*   [分层队列:曼巴图像库中的一般描述和实现，Nicolas Beucher 和 Serge Beucher](http://cmm.ensmp.fr/~beucher/publi/HQ_algo_desc.pdf)

这是一个简单的结构，非常快，但它有一些限制。

1.  它只有有限数量的优先级值(在我的实现中是 uin 8(0-255))。每个值都有自己的队列，所以增加数量会导致内存开销。

2.  一旦最高优先级队列为空，它将被移除，下一个队列将开始清空，**，并且不能再次创建。**这意味着我们必须在开始出队之前填充队列，否则我们的性能会下降，例如:出队过程只剩下 1 个优先级(255)，我们将其他元素加入队列。所有新元素都将被推入 255 队列中(因为 0–254 是空的并被删除)。

[![](../Images/095d7412f9faef5935237e8c5e501a04.png)T2】](https://cdn-images-1.medium.com/max/2000/1*1R4iZ4wrVdWtONwHkBmgDA.jpeg)

## **围棋码围棋**

优先级可以是 uint8 和值接口{}。

**我决定去掉第二个限制，**我认为它使得这个结构对于大多数真实世界的场景来说限制太多了。我设法保持了非常相似的性能(每次操作≤50 纳秒)。我缓存了具有值的最高优先级，并从那里开始出列。在某些情况下，出队过程可以是 **O(1 + k)** ，其中 K 个空队列，但是如果优先级很好地平衡，则总体上是分摊的。

这意味着该结构的寿命延长了，它可以被重用，甚至在出队过程开始之后也可以添加更高的优先级值。

在第一次迭代中，我使用**链表**作为队列(使用 golang list。列表)，平均操作时间为 150-200 纳秒。我最终使用了更快的结构(感谢 Egon Elbre 的一个建议)。它把运算时间降低到了 50ns 以下*(这是一个相当快的该死的列表)*，见收藏/德奎:

## ![GitHub logo](../Images/375dfcc32199b4dedf2b526645c27ff7.png) [卡拉拉贝](https://github.com/karalabe) / [库奇亚尔](https://github.com/karalabe/cookiejar)

### 参赛者的算法工具箱

<article class="markdown-body entry-content p-5" itemprop="text">

# 选手的工具箱

CookieJar 是通用算法、数据结构和库扩展的一个小集合，在某种程度上被认为是计算竞赛的便利工具。

这个工具箱目前正在开发中。它可能是缺乏的，并且可能在两次提交之间发生剧烈变化(尽管尽了最大努力不这样做)。欢迎你使用它，但这是你的头在线路上:)

## 装置

要获取包，请执行:

```
go get gopkg.in/karalabe/cookiejar.v2 
```

要导入此包，请在代码中添加以下行:

```
import "gopkg.in/karalabe/cookiejar.v2" 
```

有关更多详细信息，请参见[包文档](http://godoc.org/gopkg.in/karalabe/cookiejar.v2)。

## 内容

算法:

*   图表
    *   [广度优先搜索](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/graph/bfs)
    *   [深度优先搜索](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/graph/dfs)

数据结构:

*   [袋子](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/collections/bag)
*   [德克](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/collections/deque)
*   [图形](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/graph)
*   [优先级队列](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/collections/prque)
*   [队列](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/collections/queue)
*   [设置](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/collections/set)
*   [堆栈](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/collections/stack)

扩展:

*   [fmt](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/exts/fmtext)
    *   `Scan`和`Fscan`为`int`、`float64`、`string`和线条
*   [数学](http://godoc.org/gopkg.in/karalabe/cookiejar.v2/exts/mathext)
    *   `Abs`为`int`
    *   `Min`和`Max`分别代表`int`、`big.Int`和`big.Rat`
    *   `Sign`为`int` …

</article>

[View on GitHub](https://github.com/karalabe/cookiejar).

因为并发性在 Go 中很重要，所以这个结构有一个**互斥**。它可以手动或自动使用(每个函数调用都会阻塞互斥)。

为了尽可能通用，队列可以存储任何数据类型**接口{}** 。

## 收拾行李

分层队列结构是“priorityqueue”包的一部分，它拥有 100%的测试覆盖率、示例、基准和[文档](https://godoc.org/github.com/bgadrian/data-structures/priorityqueue)。

## ![GitHub logo](../Images/375dfcc32199b4dedf2b526645c27ff7.png) [ bgadrian ](https://github.com/bgadrian) / [数据结构](https://github.com/bgadrian/data-structures)

### 抽象数据结构 Go 包，构建时考虑了性能和并发性以学习 Go。

<article class="markdown-body entry-content p-5" itemprop="text">

# Go 中的数据结构[![Build Status](../Images/a7c518a7d67cfefa357d58d738ba847d.png)](https://travis-ci.org/bgadrian/data-structures)[![codecov](../Images/75af653343e37fb490b550f0e5e14b3d.png)](https://codecov.io/gh/bgadrian/data-structures)[![Go Report Card](../Images/5e17c2c62090b9d59984e211110711ec.png)](https://goreportcard.com/report/github.com/bgadrian/data-structures)

我正在为 GO 中不同的数据结构编写一个包的集合。

为什么？学习围棋，练习基本结构，学习编写快速并发算法。

所有的包都有 100%以上的测试覆盖率，基准测试和 godocs。用 go 1.9 测试。

#### ！！警告该库尚未用于生产。！！

## [优先级队列](https://raw.githubusercontent.com/bgadrian/data-structures/master/priorityqueue/README.md) [![GoDoc](../Images/f01022857188c66836ee58aff8f2adfa.png)](https://godoc.org/github.com/bgadrian/data-structures/priorityqueue)

用于优先级队列的高性能、并发安全、复杂的抽象数据结构的集合。

优先级队列是一种抽象数据类型，类似于常规队列或堆栈数据结构，但其中每个元素都有与之相关联的“优先级”。在优先级队列中，具有高优先级的元素在具有低优先级的元素之前被服务。

### [分层队列](https://raw.githubusercontent.com/bgadrian/data-structures/master/priorityqueue/README.md) [描述](https://www.researchgate.net/figure/261191274_fig1_Figure-1-Simple-queue-a-and-hierarchical-queue-b)

一个针对小整数的 **O(1)/O(1+K)优先级队列(非常快)**实现，使用 N 个简单队列的集合。它针对大量数据进行了优化，但具有较小的价值优先级…

</article>

[View on GitHub](https://github.com/bgadrian/data-structures)

## 接下来

下一个数据结构是分层堆，它基于分层队列，以较小的性能代价消除了它的限制。
[Golang 中的快速优先级队列:分级堆](https://coder.today/fast-priority-queues-in-golang-hierarchical-heap-92960edd4aa9)。
对于大型数据集，层次堆是一种非常高效的优先级队列 O(log n/k)。

## 贡献

这是我用 Go 编写的第一个库，我从未在产品中使用过这些算法，所以如果我做错了，请纠正我。

## 谢谢！🤝

[![](../Images/600be3503dc9f86b44097a3753428321.png)T2】](https://cdn-images-1.medium.com/max/2560/1*gGB1I0XqPtpwGkaXm8L8kw.jpeg)
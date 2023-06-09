# 流分析中的窗口函数

> 原文：<https://dev.to/frosnerd/window-functions-in-stream-analytics-1m6c>

# 流分析简介

在过去的几十年里，数据分析被批处理所主导。当业务不运行时，通过常规的提取-转换-加载(ETL)作业将事务数据库中的记录复制到分析数据库中。报告是通过汇总大量数据在夜间生成的。

这些分析数据库在计算批处理查询时非常快，但是速度是有代价的:灵活性和延迟。数据库模式旨在为报表所需的查询提供最佳性能。如果企业需要一份新报告，可能需要几周甚至几个月的时间来修改整个流程。由于数据只在晚上出现，报告只能在第二天更新。

如今，许多企业不再朝九晚五。客户希望在世界任何地方都能获得全天候的服务。分析师需要最新的信息，随着机器学习的兴起，自动化的近实时操作已经成为最先进的技术。这就是为什么许多公司正在将其架构从基于 ETL 的批处理转向流处理。

在数据流驱动架构中，服务发出单独的记录，即所谓的*事件*。任何对特定服务的事件感兴趣的人都可以简单地订阅它们，并在它们可用时尽快接收它们。因为每个服务都可以访问原始事件，所以它不必等待 ETL 过程完成。下图说明了随着时间推移而发生的单个事件。

[![events in time](img/27a3bd0e43de2dbb7d26997d9a54d36d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BQq6Tz88--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hfq0sbm3tz5t844blzml.png)

但是我们如何对数据流进行分析呢？我们如何基于移动数据而不是固定批次生成报告？根据定义，流是无界的，因此如果我们要寻找一个聚合视图，例如，我们网站上来自某个特定国家的点击量，我们需要引入一些边界。在批处理世界中，这个界限受到 ETL 时间表的严重影响。在流分析中，我们可以随意离散化流，以便在上面执行聚合。

将流离散成事件组称为*窗口*。窗口化在技术上可以基于事件的任何属性来完成，只要它有一个顺序。然而，这通常是基于时间来完成的。然而，有一些微妙之处需要考虑。

一个问题是您使用哪个时间戳将事件分配给一个窗口？事件生成的时间还是事件到达处理器的时间？如果您正在使用创建时间，您需要注意事件生成器可能没有正确同步的时钟。有一些技术可以处理这类问题，但是它们超出了本文的范围。相反，在这篇博文中，我们想更深入地了解可以用来对数据流执行聚合的不同窗口函数。

该员额的其余部分结构如下。首先我们将介绍四种常见的窗口功能:滚动窗口、跳跃窗口、滑动窗口和会话窗口。之后，我们将了解市场上可用的不同工具和产品，以及它们在窗口功能方面提供的功能。我们通过总结和讨论主要发现来结束这篇文章。

# 窗口功能

## 定义

窗口函数将流中的事件分配给窗口。准确地说，它更多的是一个窗口关系，而不是一个函数，因为理论上它不必将所有事件分配给窗口，即它不是总的，并且它可以将一个事件分配给多个窗口。此外，它既不是满射的(不是所有的窗口都必须包含事件)，也不是内射的(一个窗口可以包含多个事件)。然而，我们将坚持数学上不正确的窗函数。

给定一个窗口函数和一个数据流，我们可以计算每个窗口内事件的集合。如前所述，一个事件可能被分配给多个窗口，或者一个窗口可能根本没有分配事件，这取决于所选择的窗口功能。当使用派生的聚合流作为时，记住这一点很重要。例如，基于重叠窗口的事件计数图与基于不同窗口计算的计数图看起来非常不同。

Martin Kleppmann 提到了四个常用的窗口函数[1]: *翻滚窗口*、*跳跃窗口*、*滑动窗口*和*会话窗口*。接下来的部分将详细解释它们。

## 翻滚窗口

翻转窗口具有固定的长度。下一个窗口位于时间轴上上一个窗口结束之后。滚动窗口不重叠并跨越整个时间域，即每个事件恰好被分配给一个窗口。您可以通过将事件时间向下舍入到最近的窗口开始位置来实现翻转窗口。下面的动画演示了长度为 1 的翻转窗口。

[![tumbling window animation](img/ef682b2f4722f91d80a86b876d660124.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yuo1M_Sc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c1n1b45h73yahej0cx5k.gif)

因为滚动窗口只通过一个属性配置，即窗口长度 *s* ，并且它们只包含一次每个事件，所以它们通常用于简单的报告。您可以使用滚动窗口将 1 分钟内所有传入的服务器请求相加，然后显示一个图表，其中每一分钟对应一个数据点。

下面的 Azure Stream Analytics 查询提供了一个基于访问者所在国家对网站点击量进行计数的示例，该示例在一个 10 秒的滚动窗口中进行分组。我们使用创建时间戳来计算窗口分配。

```
SELECT Country, Count(*) AS Count
FROM ClickStream TIMESTAMP BY CreatedAt
GROUP BY Country, TumblingWindow(second, 10) 
```

Enter fullscreen mode Exit fullscreen mode

## 跳跃窗

像翻滚窗口一样，跳跃窗口也有固定的长度。然而，它们引入了第二个配置参数:跳数 h。不是将长度为 *s* 的窗口在时间上向前移动 *s* ，而是将其移动 *h* 。

这意味着翻转窗口是跳跃窗口的特例，其中 *s = h* 。如果*的> h* 窗口重叠，并且如果*的< h* 一些事件可能没有被分配给任何窗口。下面的动画演示了一个长度为 1、跳跃大小为 0.25 的跳跃窗口。常见的是选择 *h* 为 *s* 的一个分数。

[![hopping window animation](img/b85620d0d0b8e613495970ee79601d79.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--96wcSgjP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/13sikpcme4mhsliibhe6.gif)

可以通过计算大小为 *h* 的滚动窗口并将它们聚集成一个更大的跳跃窗口来实现跳跃窗口，其中 *h* 是 *s* 的一部分。跳跃窗口的一个常见用例是移动平均计算。

下面的 Azure Stream Analytics 查询提供了一个基于访问者所在国家/地区的网站点击量移动平均值的示例，这些访问者在 10 秒的窗口中跳了 2 秒。我们再次使用创建时间戳来计算窗口分配。

```
SELECT Country, Avg(*) AS Average
FROM ClickStream TIMESTAMP BY CreatedAt
GROUP BY Country, HoppingWindow(second, 10, 2) 
```

Enter fullscreen mode Exit fullscreen mode

## 滑动窗口

滑动窗口可以看作是具有 *h → 0* 的跳跃窗口。当它们离散化输入流时，导出的聚集流不是离散的。滑动窗口沿着时间轴移动，将窗口长度 *s* 内发生的事件组合在一起。

然而，由于我们的数据点是离散的，我们可以通过基于实际事件而不是连续时间向前移动来实现滑动窗口。每当事件进入或退出向前移动的滑动窗口的长度时，就会创建一个新窗口。这在数学上相当于根据分配给所有可能窗口的事件集对这些窗口进行重复数据删除。下图说明了长度为 1 的滑动窗口。

[![sliding window animation](img/9833756a8d13be5fcbdc72e4e285080f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iQqSZzGW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i6gei09zof2p0vkmruje.gif)

例如，滑动窗口用于计算移动平均值。它们的独特之处在于，它们提供了基于流中事件时间模式的解决方案，而不是固定的解决方案。如果事件更密集，您将获得更高分辨率的移动集合。如果没有事件传入，聚合流保持不变，不会发出新的值。

请注意，滑动窗口并不总是以相同的方式实现。在一些工具中，聚合计算仅在新事件*进入窗口*时被触发，而不是在旧事件*退出窗口*时被触发。请务必查看您正在使用的工具的文档或源代码。

下面的 Azure Stream Analytics 查询提供了一个基于访问者所在国家的 10 秒滑动窗口的网站点击量移动平均值的示例。我们再次使用创建时间戳来计算窗口分配。

```
SELECT Country, Avg(*) AS Average
FROM ClickStream TIMESTAMP BY CreatedAt
GROUP BY Country, SlidingWindow(second, 10) 
```

Enter fullscreen mode Exit fullscreen mode

## 会话窗口

与之前的窗口功能相比，会话窗口具有可变的长度。使用会话窗口功能时，您需要指定连续事件之间不得超过的时间阈值。只要时间上足够接近的新事件到来，该窗口将继续扩大。下面的动画演示了阈值为 0.5 的会话窗口。

[![session window animation](img/6d1b6c8eec49fb5de8a6b46e7be4f8b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CZ3fQBFT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2s7fvhumgbarpmtj5i2g.gif)

您可以通过将当前事件保存在缓冲区中并添加新事件(只要它们在指定的会话间隔内)来实现会话窗口。由于流是无限的，会话理论上可以无限增长。因此，一些实现采用第二个参数，该参数表示最大会话时间或每个会话的最大事件量。

会话窗口有助于将连续发生的预期相关事件组合在一起。这个名字暗示了这个窗口功能的突出用例:在你的网站上对用户会话中的点击进行分组。只要用户在短时间内保持点击，你的窗口函数就会在一个会话中聚集所有的点击。

下面的 Azure Stream Analytics 查询提供了一个基于访问者所在国家/地区的网站点击计数示例，该示例在一个最多持续 10 秒的 5 秒间隔会话窗口中进行分组。我们再次使用创建时间戳来计算窗口分配。

```
SELECT Country, Count(*) AS Count
FROM ClickStream TIMESTAMP BY CreatedAt
GROUP BY Country, SessionWindow(second, 5, 10) 
```

Enter fullscreen mode Exit fullscreen mode

# [实践中的](#window-functions-in-practice)窗口功能

在上一节中，我们看了翻滚、跳跃、滑动和会话窗口背后的理论。现在我们想深入了解市场上不同的工具和产品都提供了哪些窗口功能。下表比较了以下工具和产品中不同窗口功能的可用性:

*   [Apache 小册子](https://flink.apache.org/)
*   [卡夫卡溪流](https://kafka.apache.org/documentation/streams/)
*   [Azure Stream Analytics](https://azure.microsoft.com/en-us/services/stream-analytics/)
*   [谷歌云数据流](https://cloud.google.com/dataflow/)
*   [亚马逊 Kinesis 数据分析](https://aws.amazon.com/kinesis/data-analytics/)

Flink 和 Kafka 流都是开源框架。Azure Stream Analytics、Google Cloud Dataflow 和 Amazon Kinesis Data Analytics 是公共云提供商的专有托管解决方案。仅考虑预配置的窗口功能。一些工具，例如 Flink，允许自定义窗口函数的定义，这提供了很大的灵活性。

[![available window functions in different tools and products](img/5ff8cd558f77df935e06bbabf4242f76.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bi-2UZKi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gm6h9abrro0spcfxdu63.png)

所有工具都支持滚动窗口，尽管在谷歌云数据流中它们被称为固定时间窗口。除了亚马逊 Kinesis 数据分析之外，所有工具都支持跳跃窗口。然而，Flink 和 Dataflow 都称它们为滑动窗口，这与上一节介绍的术语不一致。

Azure Stream Analytics 和 Amazon Kinesis Data Analytics 仅支持滑动窗口。Kafka streams 使用滑动窗口进行流连接，但您不能直接在其上聚合。会话窗口在除亚马逊 Kinesis 数据分析之外的所有工具中都可用。

Amazon 提供了一种称为交错窗口的滚动窗口的替代方法，交错窗口是与事件时间戳对齐的非重叠固定长度窗口。根据文档，交错窗口是使用基于时间的窗口聚集数据的推荐方式，因为与滚动窗口相比，它们减少了延迟或无序的数据。

Flink 和 Google Cloud Dataflow 都提供全局窗口。全局窗口是一种在触发窗口之前聚合流中所有可用数据的技巧。因为全局聚集的计算是昂贵的，所以它们只能手动触发。Google Cloud Dataflow 还提供了区间窗口、日历窗口等其他自定义窗口功能。

在比较不同工具和产品时，我花了很多时间阅读文档，可能会遗漏一些东西。如果您发现错误或对上表有意见，请留下您的评论！

# 结论

在这篇文章中，我们看到了窗口函数如何在流分析中发挥重要作用。使用滚动窗口、跳跃窗口、滑动窗口、会话窗口或其他窗口函数等概念，我们能够计算无界数据流上的聚合。

到今天为止，每一个好的流处理引擎都提供了不同的窗口功能。您应该选择哪一个取决于您的用例，因为它们产生不同的结果，并具有不同的计算复杂性。通过将批处理作业迁移到流式作业，您可以近乎实时地报告结果，并对业务中的重要事件做出快速反应。

哪个流处理引擎是你最喜欢的？你通常使用哪些窗口功能，为什么？期待在评论里和大家一起讨论:)

# 参考文献

*   [1]克莱普曼，马丁。设计数据密集型应用程序:可靠、可伸缩和可维护系统背后的重要思想。奥莱利媒体公司，2017。
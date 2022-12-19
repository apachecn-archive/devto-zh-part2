# 重访 PromCon 2018 Prometheus 长期存储专题小组

> 原文：<https://dev.to/mhausenblas/revisiting-promcon-2018-panel-on-prometheus-long-term-storage-5f1p>

虽然 Prometheus 本身没有提供集群存储解决方案来存储多台机器上的现成数据，但有许多所谓的长期存储(LTS)选项可用。在这篇文章中，我们对一些 LTS 选项进行了高层次的回顾，这些选项是我有幸在今年 8 月亲自见证的 2018 年 PromCon 专题讨论会的主题:

[https://www.youtube.com/embed/3pTG_N8yGSU](https://www.youtube.com/embed/3pTG_N8yGSU)

请注意，下面我不会建议您选择哪种 LTS 解决方案，因为这在很大程度上取决于您的具体要求和偏好。

Prometheus [将](https://prometheus.io/docs/prometheus/latest/storage/)数据存储在本地存储器上，这将您可以查询或处理的数据限制在最近几天或几周，这取决于您在本地有多少可用空间。但是，如果您需要将数据保留更长的时间，例如用于长期容量规划、分析使用趋势，或者出于某个垂直行业(比如金融领域或医疗保健)的监管原因，那么您可能会从 LTS 解决方案中受益。让我们看看 PromCon 面板中讨论的产品(按字母顺序排列):

## 皮质

[Cortex](https://github.com/weaveworks/cortex) 在用作远程写入目的地时，为 Prometheus metrics 提供可横向扩展的多租户长期存储，以及可横向扩展的 Prometheus 兼容查询 API。

另请参见:

*   [Cortex:开源、可横向扩展的分布式普罗米修斯](https://www.youtube.com/watch?v=Xi4jq2IUbLs)，2017 年 6 月
*   科学怪人项目:多租户、横向扩展普罗米修斯:[视频](https://youtu.be/3Tb4Wc0kfCM) | [幻灯片](http://www.slideshare.net/weaveworks/project-frankenstein-a-multitenant-horizontally-scalable-prometheus-as-a-service)，2016 年 9 月

## InfluxDB

[InfluxDB](https://www.influxdata.com/time-series-platform/influxdb/) 是一个时序数据库，旨在处理高写入和查询负载，旨在用作任何涉及大量时间戳数据的用例的后备存储，包括开发运维监控、应用指标、物联网传感器数据和实时分析。InfluxDB 支持[普罗米修斯远程读写 API](https://docs.influxdata.com/influxdb/v1.6/supported_protocols/prometheus) 。

另请参见:

*   InfluxDB 现在支持普罗米修斯，2017 年 9 月
*   [InfluxDB + Prometheus](https://speakerdeck.com/pauldix/influxdb-plus-prometheus) 展示，2017 年 8 月

## M3

[M3](http://m3db.github.io/m3/) 是一个度量平台，最初由优步开发，基于分布式时间序列数据库 M3DB，它提供 M3DB 与 Prometheus 的[集成。](http://m3db.github.io/m3/integrations/prometheus/)

另请参见:

*   [M3:优步为普罗米修斯开发的开源大规模度量平台](https://eng.uber.com/m3/)，2018 年 8 月
*   [黑客新闻线程](https://news.ycombinator.com/item?id=17707842)

## 灭霸

[灭霸](https://github.com/improbable-eng/thanos)是一组组件，可以组成一个具有无限存储容量的高可用性公制系统。它可以无缝地添加到现有 Prometheus 部署之上，并利用 Prometheus 2.0 存储格式在任何对象存储中经济高效地存储历史指标数据，同时保持快速查询延迟。此外，它还提供了跨所有 Prometheus 安装的全局查询视图，并可以动态合并来自 Prometheus HA 对的数据。

另请参见:

*   [全新普罗米修斯存储引擎](https://www.youtube.com/watch?v=6P-RmOWWA4U)，2018 年 6 月
*   [介绍灭霸:大规模的普罗米修斯](https://improbable.io/games/blog/thanos-prometheus-at-scale)，2018 年 5 月

* * *

综上所述:虽然 Prometheus 本身不支持长期保留感兴趣的时间序列数据，但有许多解决方案可供选择，可以根据需要保留这些指标。希望这篇快速回顾能让你对一些可用的选项有所了解，并能作为你自己研究的基础，如果你发现自己处于不得不选择一个的情况下。祝您监测成功，并请分享您的调查结果和/或与上述讨论或此处未涉及的其他 LTS 解决方案的实践经验。

> 通过 Unsplash 为[杰西·奥里科拍摄封面图片。](https://unsplash.com/photos/h6xNSDlgciU)
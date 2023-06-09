# HDP 和 HDF 监测改善情况

> 原文：<https://dev.to/risdenk/hdp-and-hdf-monitoring-improvements-4ip2>

【2018 年 3 月更新

以下内容创作于 2017 年中后期。由于我们进一步改进了监测，其中一些现在已经过时。感谢@quirogadf 和@westeras 帮助我们快速实现这些想法，并在此过程中解决了性能问题。

### 概述

[Hortonworks 数据平台(HDP)](https://hortonworks.com/products/data-platforms/hdp/) 和 [Hortonworks 数据流(HDF)](https://hortonworks.com/products/data-platforms/hdf/) 都是我的团队所支持的大数据基础设施的关键组件。大数据基础设施不仅支持近实时应用，还支持分析。由于这些用例是日常运营的核心，因此监控对于确保全天一切顺利运行非常重要。在 HDP 和 HDF，有一些组件历史上没有监控，但对确保正确的集群操作至关重要。我们解决了[弹性堆栈](https://www.elastic.co/products)的监控缺点，我们已经在其他用例中使用了它。通过将 HDP 和 HDF 与弹性堆栈相结合，我们能够构建必要的监控来解决我们遇到的性能问题。

### 监控组件

| 组件名称 | 描述 |
| --- | --- |
| [阿帕奇乔洛基亚](https://jolokia.org/) | 通过 HTTP 端点公开 Java JMX 度量。 |
| [弹性度量基准](https://www.elastic.co/products/beats/metricbeat) | 从多个不同的端点收集指标。 |
| [弹性对数存储](https://www.elastic.co/products/logstash) | 将指标发送到 Elasticsearch。 |
| [Elastic - Elasticsearch](https://www.elastic.co/products/elasticsearch) | 存储指标以供以后分析和发出警报。 |
| [格拉法纳](https://grafana.com/) | Grafana 允许构建收集的指标的图表。 |

### 收集指标

因为有多个 HDP 和 HDF 组件需要监控，所以我们需要一个能够在整个堆栈中良好运行的解决方案。由于 HDP 和 HDF 的大部分堆栈都是基于 Java 的，所以我们决定用 Apache Jolokia 来展示 JMX 指标。这提供了一个单一的抽象来公开指标，并允许以标准的方式收集指标。[弹性 Metricbeat](https://www.elastic.co/products/beats/metricbeat) 在 5.4.x 中增加了对 Jolokia JMX 集合的[测试版支持。我们测试了 Metricbeat，它在为各种服务提取 Jolokia 指标方面表现出色。然后，这些指标被发送到 Logstash，并被摄取到](https://www.elastic.co/guide/en/beats/metricbeat/current/metricbeat-module-jolokia.html) [Elasticsearch](https://www.elastic.co/products/elasticsearch) 中。HDP 和 HDF 已经安装了 Grafana，因此我们能够快速地根据为每个组件收集的数据创建图表。

我们增加了对以下服务的监控: [Ambari Metrics Collector](https://cwiki.apache.org/confluence/display/AMBARI/Metrics) ， [Ambari Server](https://ambari.apache.org/) ， [Knox](https://knox.apache.org/) ， [WebHBase](https://hbase.apache.org/) ，以及 [Zookeeper](https://zookeeper.apache.org) 。我们希望随着时间的推移跟踪简单的 JVM 指标，如堆和垃圾收集(GC)。HDP 2.5 或 HDF 3.0 的现成监控不包括对所列组件的监控。我们能够从这些服务中添加指标集合，而无需重启它们，这对我们的用例很重要。

### Grafana 控制板

我们将 [Grafana](https://grafana.com/) 配置为使用 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 作为数据源，然后为我们正在收集的指标构建了几个定制仪表板。 [Kibana](https://www.elastic.co/products/kibana) 曾被考虑用于指标的仪表板，但我们更熟悉 Grafana，它适合现有的 HDP/HDF 监控堆栈。我们为之构建仪表板的许多服务都是由于我们已经存在并需要跟踪的问题。下面提供了这些仪表板的示例以及构建它们的基本原理。

#### Ambari 指标收集器

[Ambari 指标收集器](https://cwiki.apache.org/confluence/display/AMBARI/Metrics) somtimes 通过 Ambari 自动重启。我们注意到，Ambari Metrics 收集器日志指出崩溃是由于内存不足造成的。我们希望随着时间的推移跟踪堆的使用情况，并确定是否应该使用更高的堆或不同的调优。我们并没有主动监控 Ambari Metrics Collector，而是在收集数据以备将来使用。

[![](img/28b6ff7db370b96c510938e64a149100.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YufqaBVi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.img/posts/2018-03-12/ambari_metrics_grafana.png)

#### 安巴里服务器

因为我们正在评估 Ambari 视图，所以我们想要跟踪 Ambari 服务器堆的使用情况，因为文档说应该为 Ambari 视图增加堆。

[![](img/e4cc90afc550569db5433a5efb340638.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WoFCYCXF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.img/posts/2018-03-12/ambari_server_grafana.png)

#### 诺克斯

[Apache Knox](https://knox.apache.org/) 对我们的许多用户来说很重要，因为他们通过 REST 访问大数据环境。以前，我们不了解 Knox 的性能，也不知道需要进行多少调整。默认情况下，HDP 没有为 Knox 提供任何 Java GC 调优。这很麻烦，因为我们通过监控发现，堆到处都是。在实现 Knox 堆监控之后，由于减少了垃圾收集，我们能够显著提高 Knox 的性能。

[![](img/e0b78b3944f28c1416989cc68c117e6f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dtcoKEGi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.img/posts/2018-03-12/knox_grafana.png)

#### WebHBase

我们的许多 Apache HBase 用户通过 Knox 在 WebHBase 上查询 HBase。类似于我们的 Knox 监控，我们想知道 WebHbase 的性能如何。我们跟踪标准的 Java 堆使用情况以及请求数量。我们使用这些指标来为我们的用户提高 WebHBase 的性能。

[![](img/9fc488540b57f37cfbb3c2c4f43f2535.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JkO1Y5L9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.img/posts/2018-03-12/webhbase_grafana.png)

#### 动物园管理员

Apache Zookeeper 对于 HDP 和 HDF 的高可用性至关重要。开箱即用，HDP 和 HDF 都没有动物园管理员监控。我们利用 Metricbeat 特定的 Zookeeper 监控和 Jolokia 监控来提供 Zookeeper 性能的综合视图。我们正在专门跟踪 HDF 动物园管理员的表现，以确保我们不会因为 HDF 使用动物园管理员进行州管理而给动物园管理员带来不合理的负担。

[![](img/3c5b24eaab9ddf5b2dd2a5e863a1c366.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6OsSN0b7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.img/posts/2018-03-12/zookeeper_grafana.png)

### 设置和配置

#### 乔洛基

我们按照[的](https://jolokia.org/reference/html/agents.html#agents-jvm) [Apache Jolokia 文档](https://jolokia.org/reference/html/index.html)使用 Java 代理连接到 Java 进程。我们为每个服务定义了一个众所周知的 Jolokia 代理运行端口列表。我们既可以连接到当前正在运行的 JVM，也可以连接到启动时的 JVM。

**附加到现有:**

```
java -jar jolokia-jvm-1.5.0-agent.jar --host 127.0.0.1 --port <port> start <pid> 
```

**启动时连接:**

```
... -javaagent:jolokia-jvm-1.5.0-agent.jar=port=<port>,host=127.0.0.1 ... 
```

#### 公制食品

使用 [Metricbeat](https://www.elastic.co/products/beats/metricbeat) ，我们使用动态配置重载来更改或添加要收集的新指标，而无需重启 Metricbeat。每个 yaml 文件都被签入到版本控制中，以便跨环境轻松部署。此外，我们使用标签将指标分组，以便稍后在 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 中识别它们。下面是一个关于[动物园管理员](https://zookeeper.apache.org) yaml 文件的例子:

```
- module: zookeeper
  metricsets: ["mntr"]
  enabled: true
  period: 10s
  hosts: ["ZOOKEEPER_HOST:ZOOKEEPER_PORT"]
- module: jolokia
  metricsets: ["jmx"]
  enabled: true
  period: 10s
  namespace: "jolokia_metrics"
  hosts: ["ZOOKEEPER_HOST:JOLOKIA_PORT"] path: "/jolokia/"
  jmx.mappings:
    - mbean: 'java.lang:name=PS MarkSweep,type=GarbageCollector'
      attributes:
        - attr: CollectionTime
          field: gc.psms_collection_time
        - attr: CollectionCount
          field: gc.psms_collection_count
    - mbean: 'java.lang:name=PS Scavenge,type=GarbageCollector'
      attributes:
        - attr: CollectionTime
          field: gc.pss_collection_time
        - attr: CollectionCount
          field: gc.pss_collection_count
    - mbean: 'java.lang:type=Memory'
      attributes:
        - attr: HeapMemoryUsage
          field: memory.heap_usage
        - attr: NonHeapMemoryUsage
          field: memory.non_heap_usage 
```

#### Logstash

[Logstash](https://www.elastic.co/products/logstash) 作为单点与 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 交互。节拍输入允许多个弹性节拍与单个端点通信。Logstash 然后将数据发送到 Elasticsearch 中正确的索引。

#### 弹性搜索

在 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 中，我们使用了[弹性节拍模板](https://www.elastic.co/guide/en/beats/metricbeat/current/metricbeat-template.html)，并在发送指标之前加载它们。这为优化性能创建了正确的映射和索引模板。

#### 格拉法纳

在 [Grafana](https://grafana.com/) 中， [Elasticsearch 数据源](http://docs.grafana.org/features/datasources/elasticsearch/)被配置为指向 Elasticsearch 前面的 HTTP 负载平衡器端点。然后，我们按照类似的模式构建了每个图表，如下所示:

```
Query: metricset.name:"jmx" AND metricset.host:"HOSTNAME:JOLOKIA_PORT"
Alias: HOSTNAME
Metric: Average jolokia.jolokia_metrics.gc.pss_collection_count
Group By: Date Histogram @timestamp 
```

对于一些图表，我们计算导数来显示随时间的变化，而不是累计平均值。

### 结论与未来工作

通过构建一个全面的监控框架，我们能够从最初的几个服务开始扩展，并根据需要轻松添加新的服务。由于这种监控，我们已经能够查明性能瓶颈并监控我们的改进。我们现在已经深入了解了 HDP 和 HDF 堆栈的核心组件。除了监控更多服务之外，我们还将针对收集的数据添加警报，并调整收集的指标数据的保留策略。此外，我们将为技术和领导管理构建仪表板。所有这些改进都是可能的，而不会使现有的大数据基础架构复杂化。
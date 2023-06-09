# 在 KafkaStreams 中，何时在 KTable 和 KStream 之间进行选择？

> 原文：<https://dev.to/danlebrero/in-kafkastreams-when-to-choose-between-a-ktable-or-a-kstream-3834>

如果您从 KafkaStreams 或一般的流应用程序开始，有时很难为您以前认为实现起来微不足道的应用程序找到合适的解决方案。但这只是一个习惯新的 API 和概念，并看到一堆例子的问题。

我最近收到了这封电子邮件询问(随意发送给我其他的！)关于如何使用 KafkaStreams:

我有一个来自设备的传感器数据，它包含纬度/经度以及其他信息。设备序列号是关键。

我的要求是计算设备的两个连续消息之间的距离。

我有一个 kafka 主题，主题中的每条消息都有纬度/经度和事件时间戳。比如:

| 设备 | GpsDateTime | 纬度 | 经度 |
| --- | --- | --- | --- |
| xyz | 2016-11-30 22:38:36 | 32.685757 | -96.735942 |
| xyz | 2016-11-30 22:39:07 | 32.687347 | -96.732841 |
| xyz | 2016-11-30 22:39:37 | 32.68805 | -96.729726 |

我想就上述主题创建一个新的 KStream，并用距离来丰富它。

| 设备 | GpsDateTime | 纬度 | 经度 | 距离 |
| --- | --- | --- | --- | --- |
| xyz | 2016-11-30 22:38:36 | 32.685757 | -96.735942 | Zero |
| xyz | 2016-11-30 22:39:07 | 32.687347 | -96.732841 | Zero point three four |
| xyz | 2016-11-30 22:39:37 | 32.68805 | -96.729726 | Zero point three zero two |

*为设备引用前一条消息的最佳方法是什么？*

当我们谈论保持某个状态时，我们脑海中浮现的第一件事是我们必须使用 KTable，因为我们已经在脑海中形成了状态需要 DB 的印象。正如我们经常读到的，KafkaStreams KTable 是等同于 DB 表的流，对于我们的流应用程序中需要维护某种状态的任何问题，使用 KTable 似乎是很自然的。

上述应用的一个可能的解决方案是:

```
(defn  create-kafka-stream-topology-ktable  []  (let  [^StreamsBuilder  builder  (StreamsBuilder.)  _  (->  builder  (.stream  device-data-topic)  (.groupByKey)  (.aggregate  (initializer  [])  (aggregator  [k  new-point  [previous  _]]  [new-point  previous]))  (.toStream)  (.mapValues  (value-mapper  [current  previous]  (if  previous  (assoc  current  :dist  (distance  current  previous))  (assoc  current  :dist  0))))  (.to  "points-with-distance"))]  builder)) 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们使用 KTable 来生成成对的，然后将这两个值转换为一个，将两个值之间的距离添加到当前值中。

运行这个流媒体应用程序似乎行得通:

```
device-gsp-coords  :  xyz  ,  {:lat  32.685757,  :lon  -96.735942,  :time  2016-11-30  22:38:36  }  points-with-distance  :  xyz  ,  {:lat  32.685757,  :lon  -96.735942,  :time  2016-11-30  22:38:36,  :dist  0}  device-gsp-coords  :  xyz  ,  {:lat  32.687347,  :lon  -96.732841,  :time  2016-11-30  22:39:07  }  points-with-distance  :  xyz  ,  {:lat  32.687347,  :lon  -96.732841,  :time  2016-11-30  22:39:07,  :dist  0.340}  device-gsp-coords  :  xyz  ,  {:lat  32.68805,  :lon  -96.729726,  :time  2016-11-30  22:39:37  }  points-with-distance  :  xyz  ,  {:lat  32.68805,  :lon  -96.729726,  :time  2016-11-30  22:39:37,  :dist  0.302} 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我们在短时间内收到给定设备的大量消息，会发生什么呢？

```
device-gsp-coords  :  xyz1  ,  {:lat  32.685757,  :lon  -96.735942,  :time  2016-11-30  22:38:36,  :ts  1538755525616}  device-gsp-coords  :  xyz1  ,  {:lat  32.687347,  :lon  -96.732841,  :time  2016-11-30  22:39:07,  :ts  1538755525618}  device-gsp-coords  :  xyz1  ,  {:lat  32.68805,  :lon  -96.729726,  :time  2016-11-30  22:39:37,  :ts  1538755525620}  points-with-distance  :  xyz1  ,  {:lat  32.685757,  :lon  -96.735942,  :time  2016-11-30  22:38:36,  :ts  1538755525616,  :dist  0}  points-with-distance  :  xyz1  ,  {:lat  32.68805,  :lon  -96.729726,  :time  2016-11-30  22:39:37,  :ts  1538755525620,  :dist  0.302} 
```

Enter fullscreen mode Exit fullscreen mode

看起来中间值(距离为 0.340 的值)已经消失，但是请注意，最后一条消息的距离计算与之前完全相同。

阅读 [KStream#aggregate 方法](https://docs.confluent.io/current/streams/javadocs/org/apache/kafka/streams/kstream/KGroupedStream.html#aggregate-org.apache.kafka.streams.kstream.Initializer-org.apache.kafka.streams.kstream.Aggregator-)的文档，就会清楚发生了什么:

> 并非所有更新都可能被发送到下游，因为内部缓存用于对同一密钥的连续更新进行重复数据删除。传播更新的速率取决于您的输入数据速率、不同键的数量、并行运行的 Kafka Streams 实例的数量以及缓存大小和提交间隔的配置参数。

请注意，这种情况不仅会在设备短时间内发送大量信息时发生，还会在您的应用程序有大量工作要做时发生，比如第一次启动时。

## 但是为什么呢？

如果您要在两个不同的时间查询传统数据库表中的一行，您会知道在这两个时间之间该行更改了多少次吗？你能检索所有这些中间值吗？号码

KTable 同样相当于 DB 表，在这些表中，使用 KTable 意味着您只关心行/实体的最新状态，这意味着可以安全地丢弃任何以前的状态。一张表格由一个紧凑的主题支持，这并不是徒劳的。

因此，这是一个很好的测试，可以知道使用 KTable 是否合适:

> 如果您删除了除最后一个州之外的所有州，您的应用程序是否仍然正确？

在上面的例子中，我们看到我们实际上关心每个位置。如果要求知道从时间开始到现在的总距离，那么 KTable 是合适的。

值得注意的是，能够丢弃中间状态也是一种优化，因为成千上万的输入消息最终可能只产生少量的输出消息，从而缩短处理时间，并避免大量的 IO 和压缩工作。

## 有状态的 KStream 解决方案

在 KafkaStreams 中，有状态转换并不排斥 ktable，我们也在 k stream 和处理器 API 中发现了它们(记住，ktable 和 k stream 是建立在处理器 API 之上的)。

使用 [KStream#transformValues 方法](https://docs.confluent.io/current/streams/javadocs/org/apache/kafka/streams/kstream/KStream.html#transformValues-org.apache.kafka.streams.kstream.ValueTransformerWithKeySupplier-java.lang.String...-)我们得到了:

```
(defn  create-kafka-stream-topology-kstream  []  (let  [^StreamsBuilder  builder  (StreamsBuilder.)  state-name  "last-device-state"  store  (Stores/keyValueStoreBuilder  (Stores/persistentKeyValueStore  state-name)  (EdnSerde.)  (EdnSerde.))  _  (->  builder  (.addStateStore  store)  (.stream  device-data-topic)  (.transformValues  (value-transformer-with-store  state-name  (fn  [store  key  current]  (let  [previous  (.get  store  key)]  (.put  store  key  current)  (if-not  previous  (assoc  current  :dist  0)  (assoc  current  :dist  (distance  current  previous))))))  (into-array  [state-name]))  (.to  "points-with-distance"))]  builder)) 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们手动创建一个状态存储，然后在进行计算时使用它来存储/检索以前的值。

非常简单和整洁。

所有的代码都可以在这里找到[，包括
一个 Docker Compose 文件，它将运行 Kafka、Zookeeper 以及该服务的三个实例，所以你可以随意使用它。如何构建和运行它的细节在资源库中。](https://github.com/dlebrero/kafka-consecutive-values)
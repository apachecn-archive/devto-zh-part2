# Go:使用 sarama-cluster 实现 kafka 消费者

> 原文：<https://dev.to/davidsbond/golang-implementing-kafka-consumers-using-sarama-cluster-4fko>

### 简介

如今，似乎越来越多的公司正在使用基于事件的架构来提供跨不同领域的服务之间的通信。[confuent](http://confluent.io/)维护着一个[的庞大名单](https://cwiki.apache.org/confluence/display/KAFKA/Powered+By)，上面列有积极使用 [Apache Kafka](https://kafka.apache.org/) 的公司，这是一个高性能的信息系统，也是本文的主题。

Kafka 之所以被广泛采用，部分原因是它的高性能和大量多种语言的客户端库。

这个概念相当简单，客户要么生产，要么消费按“主题”分类的活动。例如，像 LinkedIn 这样的公司可能会在成功注册后针对一个`user_created`主题生成一个事件，允许多个服务异步做出反应并执行与该用户相关的相应处理。一个服务可能会向我发送欢迎邮件，而另一个服务会尝试识别我可能想要联系的其他用户。

卡夫卡事件被划分为“分区”。这些是并行事件流，允许多个消费者处理来自同一主题的事件。每个事件都包含一个“偏移量”，这个数字代表一个事件在一个分区中所有事件序列中的位置。假设一个主题分区的所有事件都存储为一个数组，偏移量就是特定事件在时间上所处的索引。这允许使用者指定消费事件的起点，从而能够避免重复处理事件或消费较早产生的事件。

然后，消费者可以组成“组”，每个消费者读取一个或多个唯一的分区，以便在多个消费者之间传播对某个主题的消费。这在运行复制服务时特别有用，可以增加事件吞吐量。

### 实现一个卡夫卡式的消费者

当谈到在 Go 中实现 Kafka 消费者时，没有太多可行的选择。本教程重点关注 [sarama-cluster](https://github.com/bsm/sarama-cluster) ，这是一个平衡的消费者实现，构建在由 [Shopify](https://www.shopify.com) 开发的现有 [sarama](https://github.com/shopify/sarama) 客户端库之上。

该库有一个简洁的 API，使得入门相当简单。第一步是定义我们的消费者配置。我们可以使用`NewConfig`方法，用一些合理的初始值
创建默认配置

```
// Create a configuration with some sane default values
config := cluster.NewConfig() 
```

Enter fullscreen mode Exit fullscreen mode

#### 认证

如果你明智的话，你所连接的 Kafka 实例将会有某种形式的认证。`sarama-cluster`库支持 TLS 和 SASL 认证方法。

如果您正在使用 TLS 证书，您可以填充`config.TLS` struct 字段:

```
config := cluster.NewConfig()

// Load an X509 certificate pair like you would for any other TLS
// configuration
cert, err := tls.LoadX509KeyPair("cert.pem", "cert.key")

if err != nil {
  panic(err)
}

ca, err := ioutil.ReadFile("ca.pem")

if err != nil {
  panic(err)
}

pool := x509.NewCertPool()
pool.AppendCertsFromPEM(ca)

tls := &tls.Config{
  Certificates: []tls.Certificate{cert},
  RootCAs: pool,
}

kafkaConfig.Net.TLS.Config = tls 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是，如果您在 docker 映像中运行您的消费者，您需要安装`ca-certificates`来创建 x509 证书池。在基于 alpine 的 docker 文件中，这看起来像:

```
FROM alpine

RUN apk add --update ca-certificates 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您使用 SASL 进行身份验证，您可以像下面这样填充`config.SASL` struct 字段:

```
config := cluster.NewConfig()

// Set your SASL username and password
config.SASL.User = "username"
config.SASL.Password = "password"

// Enable SASL
config.SASL.Enable = true 
```

Enter fullscreen mode Exit fullscreen mode

#### 实现消费

现在，我们已经用我们选择的身份验证方法创建了一个配置，我们可以创建一个允许我们处理指定主题的事件的消费者。你需要知道你的卡夫卡经纪人的地址，你的消费群体的名字，以及你希望消费的每个话题

```
consumer, err := cluster.NewConsumer(
  []string{"broker-address-1", "broker-address-2"},
  "group-id",
  []string{"topic-1", "topic-2", "topic-3"},
  kafkaConfig)

if err != nil {
  panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

`sarama-cluster`库允许您在配置中指定消费者模式。理解其中的区别很重要，因为您的实现会根据您的选择而有所不同。这可以通过`config.Group.Mode` struct 字段修改，有两个选项。这些是:

*   `ConsumerModeMultiplex` -默认情况下，来自订阅主题和分区的消息和错误都被多路复用，并通过消费者的`Messages()`和`Errors()`通道提供。
*   `ConsumerModePartitions` -需要低级别访问的用户可以启用`ConsumerModePartitions`，其中各个分区暴露在`Partitions()`通道上。然后，消息和错误必须在分区本身上使用。

使用`ConsumerModeMultiplex`时，所有消息都来自通过`Messages()`方法公开的单一通道。阅读这些信息看起来像这样:

```
// The loop will iterate each time a message is written to the underlying channel
for msg := range consumer.Messages() {
  // Now we can access the individual fields of the message and react
  // based on msg.Topic
  switch msg.Topic {
    case "topic-1":
      handleTopic1(msg.Value)
      break;
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您想要一个更底层的实现，可以自己对分区变化做出反应，那么您将会想要使用`ConsumerModePartitions`。这通过`consumer.Partitions()`方法为您提供了单独的分区。这暴露了当使用者组重新平衡时分区被写入的底层通道。然后你可以使用每个分区来读取消息和错误:

```
// Every time the consumer is balanced, we'll get a new partition to read from
for partition := range consumer.Partitions() {
  // From here, we know exactly which topic we're consuming via partition.Topic(). So won't need any
  // branching logic based on the topic.
  for msg := range consumer.Messages() {
    // Now we can access the individual fields of the message
    handleTopic1(msg.Value)   
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

做事的方式需要你对你的消费者进行更多的监督。首先，您需要优雅地处理在重新平衡的情况下分区关闭的情况。当向组中添加新的消费者时，会发生这种情况。当您完成消费时，您还需要手动调用`partition.Close()`方法。

### 处理错误&重新平衡

如果你增加更多的消费者，现有的消费者将经历一个重新平衡。这是分配给每个用户的分区发生变化的地方，以便在用户之间实现最佳分布。我们创建的`consumer`实例已经公开了一个`Notifications()`通道，我们可以从这个通道记录/响应这些变化。

```
 for notification := range consumer.Notifications() {
    // The type of notification we've received, will be
    // rebalance start, rebalance ok or error
    fmt.Println(notification.Type)

    // The topic/partitions that are currently read by the consumer
    fmt.Println(notification.Current)

    // The topic/partitions that were claimed in the last rebalance
    fmt.Println(notification.Claimed)

    // The topic/partitions that were released in the last rebalance
    fmt.Println(notification.Released)
  } 
```

Enter fullscreen mode Exit fullscreen mode

错误很容易读取，并通过`consumer.Errors()`通道提供。他们返回一个标准的`error`实现。

```
 for err := range consumer.Errors() {
    // React to the error
  } 
```

Enter fullscreen mode Exit fullscreen mode

为了能够阅读通知和错误，我们需要对我们的配置做一些小的修改，比如:

```
config.Consumer.Return.Errors = true
config.Group.Return.Notifications = true 
```

Enter fullscreen mode Exit fullscreen mode

### 提交偏移量

实现消费者的最后一步是提交我们的偏移量。简而言之，我们在告诉卡夫卡，我们已经完成了对一个信息的处理，我们不想再去消费它。一旦不再需要消息数据进行任何处理，就应该这样做。如果您过早地提交偏移，您可能会失去在出错时轻松地重新消耗事件的能力。假设您将事件内容直接写入数据库，在将事件内容成功写入数据库之前，不要提交偏移量。这样，如果数据库操作失败，您可以重新消耗事件来重试。

```
// The loop will iterate each time a message is written to the underlying channel
for msg := range consumer.Messages() {
  // Now we can access the individual fields of the message and react
  // based on msg.Topic
  switch msg.Topic {
    case "topic-1":
      // Do everything we need for this topic
      handleTopic1(msg.Value)

      // Mark the message as processed. The sarama-cluster library will
      // automatically commit these.
      // You can manually commit the offsets using consumer.CommitOffsets()
      consumer.MarkOffset(msg)
      break;
      // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是实现一个简单的 Kafka 消费群所需要的一切。基于你如何维护你的 Kafka 代理，库提供了更多的配置选项来满足你的需求。我建议您自己浏览所有的配置值，以确定是否需要调整。

### 链接

*   [http://confluent.io/](http://confluent.io/)
*   [https://CWI ki . Apache . org/confluence/display/KAFKA/Powered+By](https://cwiki.apache.org/confluence/display/KAFKA/Powered+By)
*   [https://kafka.apache.org/](https://kafka.apache.org/)
*   [https://github.com/bsm/sarama-cluster](https://github.com/bsm/sarama-cluster)
*   [https://github . com/shopify/sarama](https://github.com/shopify/sarama)
*   [https://www.shopify.com](https://www.shopify.com)
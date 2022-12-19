# 使用 Kafka 和 Spark 处理流 Twitter 数据—第 1 部分:设置 Kafka 集群

> 原文：<https://dev.to/dbsheta/processing-streaming-twitter-data-using-kafka-and-spark--part-1-setting-up-kafka-cluster-32lh>

按照我在上一篇文章中列出的计划，我将从建立一个 Kafka 集群开始。在整个系列中，我将主要致力于 Google Cloud 实例，但是，我也将列出在您的本地机器上设置相同实例的步骤。

此外，在本系列中，主要焦点将是如何做而不是如何做。比起 Kafka/Spark/Zookeeper 如何实现，我们将花更多的时间来学习如何实现各种用例。然而，如果网上没有现成的资源，我们将进入理论模式。

## 阿帕奇动物园管理员

Kafka 使用 [Zookeeper](https://zookeeper.apache.org/) 来存储关于 Kafka 集群的元数据，以及消费者客户端的详细信息。网上有很多文章解释了卡夫卡为什么需要《动物园管理员》。[Data-Flair 的这篇文章做得很好。](https://data-flair.training/blogs/zookeeper-in-kafka/)

虽然您可以使用 Kafka 发行版中包含的脚本直接启动并运行快速而肮脏的单节点 Zookeeper 服务器，但从发行版安装完整版本的 Zookeeper 是微不足道的。

[![Source: Kafka- The Definitive Guide](../Images/128fb73b366147d76ec76986634382f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a_f7LDhZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A0vTDGwBRzFsexzhtiJ4OsQ.png)

我假设你已经安装了 JDK1.8。如果不是，Linux/macOS 用户可以使用包管理器下载 openJDK。Windows 用户可以去甲骨文的网站安装。

### **动物园管理员独立模式**

那些没有像 Google Cloud 或 Azure 或 AWS 这样的可用云资源的人，可以运行单节点独立的 Zookeeper 实例。旋转这样一个实例相当简单

下载最新版本的[动物园管理员](https://www.apache.org/dyn/closer.cgi/zookeeper/)。

```
 tar -xvf zookeeper-X.X.X.tar.gz -C /opt
    ln -s /opt/zookeeper-X.X.X /opt/zookeeper
    cd /opt/zookeeper
    cat conf/zoo_sample.cfg >> zookeeper.properties
    bin/zkServer.sh start conf/zookeeper.properties 
```

### **动物园管理员合奏**

动物园管理员集群被称为*集群*。由于所使用的算法，建议集合包含奇数个服务器(3，5，…),因为大多数集合成员(法定人数)必须工作，以便 Zookeeper 响应请求。此外，*而不是* *建议*运行七个以上的节点，因为一致性协议的性质会导致性能下降。

为了配置一个 Zookeeper 系综，所有的服务器必须有一个共同的配置，并且每个服务器需要在数据目录中有一个 ***myid*** 文件来指定服务器的 id 号

除了最后一个命令，在所有服务器上运行所有以前的命令。除此之外，还将在所有服务器上运行以下程序:

1.  将您的服务器列表(主机名/IP)添加到 zookeeper 配置文件的底部:

```
 server.1=X.X.X.X:2888:3888
    server.2=Y.Y.Y.Y:2888:3888
    server.3=Z.Z.Z.Z:2888:3888 
```

1.  在 dataDir 位置添加 myid 文件，在我的例子中是 */tmp/zookeeper* :

```
 touch /tmp/zookeeper/myid
    echo 1 >> /tmp/zookeeper/myid 
```

1.  完成上述更改后，在所有服务器上逐一启动 zookeeper。

```
 bin/zkServer.sh start conf/zookeeper.properties 
```

## 设定卡夫卡

在你所有的发球上下载最新版本的[卡夫卡](http://mirrors.wuchna.com/apachemirror/kafka/2.0.0/kafka_2.11-2.0.0.tgz)。

```
 tar -xvf kafka_2.11-0.11.0.0.tgz –C /opt
    ln -s /opt/kafka_2.XX /opt/kafka 
```

更新所有实例中的 kafka server.properties 文件(主机名/IP ),以包含以下行。这个文件位于*/opt/Kafka/config/server . properties*

```
 zookeeper.connect=X.X.X.X:2181,Y.Y.Y.Y:2181,Z.Z.Z.Z:2181 
```

### **单节点多代理(SNMB):**

对于手头没有云实例的人，您可以在本地设置一个集群。

1.  你必须复制 *server.properties* 文件，并用不同的名字如 server1.properties、server2.properties 等复制 3 次。

2.  每个 Kafka 代理都必须有一个整数标识符。打开配置文件，将第一台服务器的 *broker.id=1* 更改为第二台服务器的 *broker.id=2* ，依此类推。一个好的指导原则是将该值设置为主机固有的值，以便在执行维护时更容易将代理 id 映射到主机

3.  因为您将在同一台机器上运行多个实例，所以请更改端口配置，以便每个进程使用不同的端口号。打开配置文件，在 1 号上更改 *port=9092* ，在 2 号上更改为 9093，依此类推。

4.  此外，将来每当我发出一个 MNMB 设置命令时，您应该自动将其映射到您的配置中

### **多节点多代理(MNMB):**

1.  打开配置文件，为第一台服务器更改 *broker.id=1* ，为第二台服务器更改 *broker.id=2* ，依此类推。

2.  如果服务器的规范主机名不是公共的，请将其添加到 hosts 文件中。或者你需要覆盖每个服务器实例*的广告*

## 测试完整设置

1.  我们已经开始了动物园管理员合奏，现在让我们在所有的服务器上启动卡夫卡。

```
 cd /opt/kafka
    bin/kafka-server-start.sh config/server.properties 
```

1.  让我们创建一个包含 3 个分区和 2 个副本的示例主题:

```
 bin/kafka-topics.sh --create --zookeeper X.X.X.X:2181 --replication-factor 2 --partitions 3--topic sample_test 
```

1.  Kafka 有一个命令行消费者，它将消息转储到标准输出。

```
 bin/kafka-console-consumer.sh — zookeeper X.X.X.X:2181 — topic sample_test — from-beginning 
```

1.  运行生成器，然后在控制台中键入几条消息发送到服务器。

```
 bin/kafka-console-producer.sh --broker-list  X.X.X.X:9092 --topic sample_test
    > Hello, World!
    > Hello from the other side. 
```

如果您在控制台消费者窗口中看到输出，那么恭喜您！您成功地在本地/云上设置了 zookeeper 和 kafka 集群。如果由于某些原因，你得到了错误或不能得到想要的输出，请留下评论。

在接下来的几篇文章中，我们将使用相同的设置。在下一篇文章中，我们将看到如何实现一个 Kafka 客户端，它将从 Twitter 读取最新的 tweets，并将它们推送到 Kafka。

**直到那时，**

[![Goodbye](../Images/bd8e474940fb036418cc256fc092c89d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wJ5v75Q0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thumbs.gfycat.com/GenuineMenacingImperialeagle-size_restricted.gif)
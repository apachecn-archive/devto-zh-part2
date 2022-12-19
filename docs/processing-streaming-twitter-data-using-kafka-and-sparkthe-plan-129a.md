# 使用 Kafka 和 Spark 处理 Twitter 流数据——计划

> 原文：<https://dev.to/dbsheta/processing-streaming-twitter-data-using-kafka-and-sparkthe-plan-129a>

## 什么是阿帕奇卡夫卡？

> Apache Kafka 是一个发布/订阅消息系统。它通常被称为“分布式提交日志”，或者最近被称为“分布式流平台”自 2011 年由 LinkedIn 创建并开源以来，Kafka 已经从消息队列迅速发展成为一个成熟的流媒体平台

[![Source: [https://kafka.apache.org/images/kafka_diagram.png](https://kafka.apache.org/images/kafka_diagram.png)](../Images/6de57a1f7fddcf2bd88afd60a18703fd.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Fyx1I7BG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2706/0%2Af_7HXjtx0Nva3RQR.png) *来源:【https://kafka.apache.org/images/kafka_diagram.png】*T4

## 灵感

我最近读了《卡夫卡:权威指南》这本书，作者是卡夫卡的创造者。对于任何想开始使用 Kafka 开发应用程序的人，以及任何想了解大多数财富 500 强公司使用的这种独特平台的内部情况的人来说，这确实是一本精彩的书。

## 计划

在这个系列中，我将探索 Apache Kafka 的各个方面，所有这些都是通过实现很酷的数据管道实现的:

1.  我们将从在云中/本地设置 Kafka 集群开始

2.  之后，我们将编写一个 Producer 客户端，它将使用 Twitter API 不断获取最新的推文，并将它们推送到 Kafka。

3.  然后，我们将使用 Kafka Streams API 实现一个应用程序，该应用程序将实时消费来自 Kafka 的推文，并对它们进行基本处理，如查找每个用户的推文数量和最常用的词(即字数)。

4.  然后，我们将尝试更酷的东西，比如编写我们自己的 Kafka Connector，它将使用 twitter 作为数据源，并学习使用 Apache NiFi 来达到事半功倍的效果。

5.  我们将使用 Spark Streaming 对实时 twitter 数据进行情感分析

6.  最后，如果一切顺利，我们将尝试调整我们的架构，并使用 Firebase 和 Kafka 实现通知服务，如果用户的推文有负面情绪，Kafka 将向用户发送推送通知！

## 我们开始吧！

[![[By Amine Rock Hoovr](https://unsplash.com/@hoovr01?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)](../Images/7dd363ed01b3827d5be6c794821f0b3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1J1ms0LZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/10368/0%2AU_EwY9N-91IXddnk)
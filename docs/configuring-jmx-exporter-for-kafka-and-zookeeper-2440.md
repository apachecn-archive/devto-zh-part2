# 为 Kafka 和 Zookeeper 配置 JMX 导出器

> 原文：<https://dev.to/dzeban/configuring-jmx-exporter-for-kafka-and-zookeeper-2440>

我使用普罗米修斯已经有一段时间了，非常喜欢它。大多数事情都很简单——安装和配置 Prometheus 很容易，设置导出器只需启动并忘记，[检测您的代码](https://dev.to/blog/go-prometheus-service/)是一件乐事。但是有两件事我真的很纠结:

1.  搜索数据模型和 PromQL 以获得有意义的见解。
2.  正在配置 jmx 导出程序。

在这篇文章中，我将分享 JMX 的部分，因为我觉得我还没有完全理解数据模型和 PromQL。所以让我们深入到 jmx-exporter 的事情中。

## [T1】什么是 jmx-exporter](#what-is-jmxexporter)

jmx-exporter 是一个从基于 JVM 的应用程序(如 Java 和 Scala)中读取 jmx 数据的程序，并通过 HTTP 以 Prometheus 能够理解和抓取的简单文本格式公开它。

JMX 是 Java 世界中一种常见的技术，用于导出正在运行的应用程序的统计数据并对其进行控制(例如，可以用 JMX 触发 GC)。

jmx-exporter 是一个 Java 应用程序，它使用 JMX API 来收集应用程序和 JVM 指标。它是一个 Java 代理，这意味着它运行在同一个 JVM 中。这为您提供了一个好处，即不会远程公开 JMX——JMX-exporter 只会收集指标，并以只读模式通过 HTTP 公开它。

## 安装 jmx 导出器

因为它是用 Java 编写的，所以 jmx-exporter 是作为一个 jar 分发的，所以你只需要从 maven 下载它[并把它放在你的目标主机上的某个地方。](https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.3.0/jmx_prometheus_javaagent-0.3.0.jar)

对此我有一个负责的角色——https://github.com/alexdzyoba/ansible-jmx-exporter。除了下载 jar 之外，它还会放入 jmx-exporter 的配置文件。

该配置文件包含将 JMX mbean 重写为 Prometheus exposition 格式度量的规则。基本上，它是一个将 MBeans 字符串转换为 Prometheus 字符串的 regexps 集合。

jmx-exporter 源代码中的 [example_configs 目录](https://github.com/prometheus/jmx_exporter/tree/master/example_configs)包含许多流行的 Java 应用程序的示例，包括 Kafka 和 Zookeeper。

## 用 jmx-exporter 配置 Zookeeper

正如我说过的，jmx-exporter 作为 java 代理运行在其他 JVM 中，收集 jmx 指标。为了演示它是如何工作的，让我们在 Zookeeper 中运行它。

Zookeeper 是包括 Hadoop、Kafka 和 Clickhouse 在内的许多生产系统的关键部分，所以你真的想监控它。尽管事实上你可以用 [4lw 命令](https://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_zkCommands) ( `mntr`，`stat`)等来做这件事。)并且[有](https://github.com/dln/zookeeper_exporter) [专用的](https://github.com/lucianjon/zk-exporter) [导出器](https://github.com/dabealu/zookeeper-exporter)我更喜欢使用 JMX 来避免 Zookeeper 不断的查询(它们给度量增加了噪声，因为 4lw 命令被算作正常的 Zookeeper 请求)。

要用 jmx-exporter 收集 Zookeeper JMX 指标，您必须向 Zookeeper launch 传递以下参数:

```
-javaagent:/opt/jmx-exporter/jmx-exporter.jar=7070:/etc/jmx-exporter/zookeeper.yml 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用 Kafka 发布的 Zookeeper(你不应该)，那么通过`EXTRA_ARGS` :
传递它

```
$ export EXTRA_ARGS="-javaagent:/opt/jmx-exporter/jmx-exporter.jar=7070:/etc/jmx-exporter/zookeeper.yml"
$ /opt/kafka_2.11-0.10.1.0/bin/zookeeper-server-start.sh /opt/kafka_2.11-0.10.1.0/config/zookeeper.properties 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用独立的 Zookeeper 发行版，那么把它作为 SERVER_JVMFLAGS 添加到 zookeeper-env.sh:

```
# zookeeper-env.sh
SERVER_JVMFLAGS="-javaagent:/opt/jmx-exporter/jmx-exporter.jar=7070:/etc/jmx-exporter/zookeeper.yml" 
```

Enter fullscreen mode Exit fullscreen mode

无论如何，当您启动 Zookeeper 时，您应该看到进程监听指定的端口(在我的例子中是 7070)并响应`/metrics`查询:

```
$ netstat -tlnp | grep 7070
tcp 0 0 0.0.0.0:7070 0.0.0.0:* LISTEN 892/java

$ curl -s localhost:7070/metrics | head
# HELP jvm_threads_current Current thread count of a JVM
# TYPE jvm_threads_current gauge
jvm_threads_current 16.0
# HELP jvm_threads_daemon Daemon thread count of a JVM
# TYPE jvm_threads_daemon gauge
jvm_threads_daemon 12.0
# HELP jvm_threads_peak Peak thread count of a JVM
# TYPE jvm_threads_peak gauge
jvm_threads_peak 16.0
# HELP jvm_threads_started_total Started thread count of a JVM 
```

Enter fullscreen mode Exit fullscreen mode

## 用 jmx-exporter 配置 Kafka

Kafka 是一个用 Scala 编写的消息代理，所以它运行在 JVM 中，这反过来意味着我们可以使用 jmx-exporter 作为它的度量。

要在 Kafka 中运行 jmx-exporter，您应该这样设置`KAFKA_OPTS`环境变量:

```
$ export KAFKA_OPTS='-javaagent:/opt/jmx-exporter/jmx-exporter.jar=7071:/etc/jmx-exporter/kafka.yml' 
```

Enter fullscreen mode Exit fullscreen mode

然后启动卡夫卡(我假设动物园管理员已经按照卡夫卡的要求启动了):

```
$ /opt/kafka_2.11-0.10.1.0/bin/kafka-server-start.sh /opt/kafka_2.11-0.10.1.0/conf/server.properties 
```

Enter fullscreen mode Exit fullscreen mode

检查 jmx-exporter HTTP 服务器是否正在监听:

```
$ netstap -tlnp | grep 7071
tcp6 0 0 :::7071 :::* LISTEN 19288/java 
```

Enter fullscreen mode Exit fullscreen mode

并且刮度量！

```
$ curl -s localhost:7071 | grep -i kafka | head
# HELP kafka_server_replicafetchermanager_minfetchrate Attribute exposed for management (kafka.server<type=ReplicaFetcherManager, name=MinFetchRate, clientId=Replica><>Value)
# TYPE kafka_server_replicafetchermanager_minfetchrate untyped
kafka_server_replicafetchermanager_minfetchrate{clientId="Replica",} 0.0
# HELP kafka_network_requestmetrics_totaltimems Attribute exposed for management (kafka.network<type=RequestMetrics, name=TotalTimeMs, request=OffsetFetch><>Count)
# TYPE kafka_network_requestmetrics_totaltimems untyped
kafka_network_requestmetrics_totaltimems{request="OffsetFetch",} 0.0
kafka_network_requestmetrics_totaltimems{request="JoinGroup",} 0.0
kafka_network_requestmetrics_totaltimems{request="DescribeGroups",} 0.0
kafka_network_requestmetrics_totaltimems{request="LeaveGroup",} 0.0
kafka_network_requestmetrics_totaltimems{request="GroupCoordinator",} 0.0 
```

Enter fullscreen mode Exit fullscreen mode

如果您在 systemd:
下运行 Kafka，下面是如何运行 jmx-exporter java 代理

```
...
[Service]
Restart=on-failure
Environment=KAFKA_OPTS=-javaagent:/opt/jmx-exporter/jmx-exporter.jar=7071:/etc/jmx-exporter/kafka.yml
ExecStart=/opt/kafka/bin/kafka-server-start.sh /etc/kafka/server.properties
ExecStop=/opt/kafka/bin/kafka-server-stop.sh
TimeoutStopSec=600
User=kafka
... 
```

Enter fullscreen mode Exit fullscreen mode

## 重述

使用 jmx-exporter，您可以收集正在运行的 JVM 应用程序的指标。jmx-exporter 作为 Java 代理运行(在目标 JVM 内部),它抓取 jmx 指标，根据配置规则重写它，并以 Prometheus exposition 格式公开它。

快速设置检查我的 JMX-exporter[alexdzyoba . JMX-exporter](https://galaxy.ansible.com/alexdzyoba/jmx-exporter/)的责任[角色。](https://github.com/alexdzyoba/ansible-jmx-exporter)

目前就这些了，请继续关注[订阅 RSS](https://alex.dzyoba.com/feed) 或者在 [Twitter @AlexDzyoba](https://twitter.com/alexdzyoba) 上关注我。
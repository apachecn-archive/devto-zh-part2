# Apache Zookeeper -更改 Kerberos 主体- AuthFailedException

> 原文：<https://dev.to/risdenk/apache-zookeeper---changing-kerberos-principal---authfailedexception-3lnc>

### TL；速度三角形定位法(dead reckoning)

为 [Apache Zookeeper](https://zookeeper.apache.org/) 更改 Kerberos 主体会导致`AuthFailedException`必须在客户端用`-Dzookeeper.sasl.client.username=CUSTOM_ZK_PRINCIPAL`修复。

### 概述

2015 年 7 月，在 [Avalon Consulting，LLC](https://www.avalonconsult.com) 时，我花了两周时间与一家公司合作，以保护他们的 [Hortonworks 数据平台(HDP)](https://hortonworks.com/products/data-platforms/hdp/) 集群。这包括 SSL、LDAP 和 Kerberos。需要额外努力的一个因素是 Linux 用户名必须是 8 个字符的要求，这也影响了 Kerberos 主体长度。这一要求是由[中间派](https://www.centrify.com/)强制执行的，但也可以通过其他方式强制执行。前几天，我发现在我成功完成安全实现 3 年多后的今天，同样的问题依然存在。希望这篇博客有助于避免将来处理变化的委托人时的麻烦，尤其是 [Apache Zookeeper](https://zookeeper.apache.org/) 。

### 改变 Apache Zookeeper 主体的影响

在[“启用 Kerberos”向导](https://cwiki.apache.org/confluence/display/AMBARI/Automated+Kerberizaton)期间，Apache Ambari 允许更改生成的主体。这位客户想要 8 个字符的用户名，因此我们更改了相应的原则来满足这一要求。 [Kerberos](https://web.mit.edu/kerberos/) 向导成功完成，直到服务开始部分。许多服务都正确启动了，但是有一些没有启动。

我记得很少失败的是 [HDFS ZKFC](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html) 、[阿帕奇 HBase](https://hbase.apache.org/) 、[阿帕奇风暴](https://storm.apache.org/)。普遍的趋势是任何服务都必须连接到 Apache Zookeeper。由于 [`SASL`身份验证](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)，日志中出现无法通过 Zookeeper 身份验证的错误。

Zookeeper 客户端错误消息示例:

```
org.apache.zookeeper.KeeperException$AuthFailedException: KeeperErrorCode = AuthFailed for ... 
```

Enter fullscreen mode Exit fullscreen mode

### 为自定义 Zookeeper 主体修复 Zookeeper 客户端

经过一番挖掘，问题的根本原因是 Zookeeper 假设服务主体是`zookeeper`。 [ZOOKEEPER-1811](https://issues.apache.org/jira/browse/ZOOKEEPER-1811) 将硬编码的`zookeeper`字符串更改为可配置的属性。新增的 [`zookeeper.sasl.client.username`](http://zookeeper.apache.org/doc/r3.5.3-beta/zookeeperProgrammers.html#sc_java_client_configuration) 属性允许用户在客户端调整本金。由于这是客户端设置，Zookeeper 的所有客户端都需要配置这个属性。需要设置的系统属性如下所示。

```
-Dzookeeper.sasl.client.username=CUSTOM_ZK_PRINCIPAL_NAME 
```

Enter fullscreen mode Exit fullscreen mode

对于 [Apache Zookeeper](https://zookeeper.apache.org/) 来说，由于它本身是一个客户端，所以`JVMFLAGS`属性必须在`Zookeeper Env -> client_opts`中或者命令行`JVMFLAGS="-Dsun.security.krb5.debug=true -Dzookeeper.sasl.client.username=zk" /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server SERVERNAME`上设置

对于 [Apache Hadoop HDFS](https://hadoop.apache.org/) ，系统属性必须添加到`Hadoop Env -> HADOOP_OPTS`。对于 [Apache HBase](https://hbase.apache.org/) ，系统属性必须添加到`HBase Env -> HBASE_OPTS`中。对于[阿帕奇风暴](https://storm.apache.org/)，系统属性必须添加到`nimbus.childopts`、`supervisor.childopts`和`worker.childopts`。当时， [Ambari Infra Solr](https://ambari.apache.org/) 、 [Ambari Metrics](https://cwiki.apache.org/confluence/display/AMBARI/Metrics) 和 [Kafka](https://kafka.apache.org/) 并不存在，但是需要相同的系统属性，如这里的[和这里的](https://community.hortonworks.com/questions/21118/hive-storm-kafka-hbase-cannot-connect-to-zookeeper.html)所述。

### Apache Ambari 和自定义主体的当前状态

在过去一年左右的时间里，我还没有检查过 Apache Ambari 是如何处理定制主体的。我知道 Ambari 保护 Apache Hadoop 集群的方式有了很大的改进。Apache Ambari 2.5.0 为一系列服务添加了 Zookeeper ACLs 和认证。根据[这篇文章](https://community.hortonworks.com/articles/108144/hdfs-yarn-infrasolr-zk-client-when-using-custom-zo.html)所描述的问题仍然是 HDP 2.6.x 和 Ambari 2.5.x 的问题。Ambari 2.6.x 已经发布，但也可能有同样的问题。

不管 Apache Ambari 如何改进，我建议不要改变 Zookeeper 或其他服务的原则，因为这会导致很多问题。
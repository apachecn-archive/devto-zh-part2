# Apache Solr——在 Apache Hadoop HDFS 上运行

> 原文：<https://dev.to/risdenk/apache-solr---running-on-apache-hadoop-hdfs-17pc>

### 概述

Apache Solr 是一个基于 [Apache Lucene](https://lucene.apache.org/solr/) 的全文搜索引擎。在过去的六年里，我一直在与 Apache Solr 合作。其中一些是纯 Solr 安装，但许多是与 Apache Hadoop 集成的。这包括 Hortonworks HDP 搜索和 Cloudera 搜索。Solr 在 HDFS 的表现是一个常见的问题，所以写这篇文章来分享我的一些经验。

### Apache Hadoop HDFS

Apache Hadoop 包含一个名为 Hadoop 分布式文件系统(HDFS)的文件系统。HDFS 设计用于在商用硬件上扩展到数 Pb 的数据。这些年来，商品硬件的定义发生了变化，但前提是不需要最新最棒的硬件。从 Apache HBase 到 Apache Spark，各种工作负载都使用 HDFS。HDFS 的性能倾向于支持大文件的读写。HDFS 还将所有可用磁盘用于 I/O，这对于大型集群很有帮助。

### 阿帕奇索尔和 HDFS

从早期的 4.x 版本开始，Apache Solr 可以在 HDFS 上运行。Cloudera Search 添加了这一功能，以便能够使用现有的 HDFS 存储进行搜索。Hortonworks HDP 搜索，因为它是基于 Apache Solr 的，所以也支持 HDFS。由于 HDFS 不是本地文件系统，Apache Solr 实现了块缓存，旨在帮助缓存内存中的 HDFS 块。有了用于查询的 HDFS 块缓存，Apache Solr 可以有较慢但类似于本地索引的性能。HDFS 数据块缓存不用于合并、索引或一次性读取用例。这意味着在某些领域，Apache Solr 和 HDFS 的速度可能会慢一些。

### 阿帕奇 Solr 性能

如果你在寻找变化最少的最佳性能，那么固态硬盘和充足的内存是你应该寻找的地方。如果您预算有限，那么带内存的旋转磁盘也可以提供足够的性能。给定适当的内存量，HDFS 上的 Solr 可以像本地磁盘一样运行。常见的“视情况而定”的警告将归结为具体的用例。对于大规模分析，Solr 在 HDFS 表现良好。对于高速索引，那么你将需要固态硬盘，因为 Solr 在 HDFS 上的写性能是不匹配的。

在用 Solr 处理性能问题的大部分时间里，我发现底层硬件并不是问题所在。通常，数据的索引或查询方式会对性能产生巨大影响。这里的标准调试和改进有助于所有不同类型的硬件。

### HDFS Apache Solr-最佳实践

#### 干净利落地关闭 Apache Solr

确保给 Apache Solr 足够的时间来干净地关闭。旧版本的`solr`脚本在关闭前只等待了 5 秒钟。增加睡眠时间，以确保不会因为不干净的关机而将`write.lock`文件留在 HDFS 上。

#### 必须正确配置 Ulimits

确保您拥有运行 Solr 的用户的适当权限。当你由于 ulimits 太低而不能使用 Solr 时，这将导致巨大的问题。

#### 使用动物园管理员 chroot

有了 Apache Hadoop，许多不同的软件都使用 Zookeeper。如果你专门为 Solr 使用一个 chroot，这将有助于保持事情的条理性。

#### 为 Solr 做一个 HDFS 的目录

在 HDFS 为 Solr 制作一个目录，这个目录不能用于其他任何东西。这将确保您不会对从该位置读取/写入的其他进程造成问题。它还可以设置权限，以确保只有 Solr 用户可以访问。

#### HDFS 块缓存必须调优

确保 HDFS 数据块缓存已启用，并且已正确调整。默认情况下，块缓存没有足够的片来获得良好的性能。默认情况下，HDFS 数据块缓存的每个数据块为 128MB ( `solr.hdfs.blockcache.blocksperbank` :16834 * 8KB)。板的数量决定了将有多少内存用于缓存。由于 HDFS 块缓存存储在堆外，Java 也必须被允许使用`-XX:MaxDirectMemorySize`来分配多达这个数量的直接内存。

下面是一个方便的表格，显示了块数量、MaxDirectMemorySize 和 HDFS 块缓存大小之间的关系。

| `-Dsolr.hdfs.blockcache.slab.count` | `-XX:MaxDirectMemorySize` | HDFS 块缓存大小 |
| --- | --- | --- |
| one | 250MB | 128MB |
| eight | 2GB | 1GB |
| Twenty | 4GB | 2.5GB |
| Forty | 8GB | 5GB |
| One hundred | 15GB | 12.5GB |
| Two hundred | 30GB | 25GB |

正确配置后，Solr 将在日志中打印出所需内存的计算结果，如下所示:

```
Block cache target memory usage, slab size of [134217728] will allocate [40] slabs and use ~[5368709120] bytes 
```

#### 确保 HDFS 短路读取被启用

HDFS 短路读取允许 HDFS 图书馆从本地套接字读取，而不是进行网络调用。这可以显著提高读取性能。

#### 示例配置

```
# Solr HDFS - setup
## Use HDFS by default for its collection data and tlogs
SOLR_OPTS="$SOLR_OPTS -Dsolr.directoryFactory=HdfsDirectoryFactory \
    -Dsolr.lock.type=hdfs \
    -Dsolr.hdfs.home=$(hdfs getconf -confKey fs.defaultFS)/apps/solr \
    -Dsolr.hdfs.confdir=/etc/hadoop/conf"

## If HDFS Kerberos enabled, uncomment the following
#SOLR_OPTS="$SOLR_OPTS -Dsolr.hdfs.security.kerberos.enabled=true \
# -Dsolr.hdfs.security.kerberos.keytabfile=/etc/security/keytabs/solr.keytab \
# -Dsolr.hdfs.security.kerberos.principal=solr@REALM"

# Solr HDFS - performance
## Enable the HDFS Block Cache to take the place of memory mapping files
SOLR_OPTS="$SOLR_OPTS -Dsolr.hdfs.blockcache.enabled=true \
    -Dsolr.hdfs.blockcache.global=true \
    -Dsolr.hdfs.blockcache.read.enabled=true \
    -Dsolr.hdfs.blockcache.write.enabled=false"

## Size the HDFS Block Cache
SOLR_OPTS="$SOLR_OPTS -Dsolr.hdfs.blockcache.blocksperbank=16384 \
    -Dsolr.hdfs.blockcache.slab.count=200"

## Enable direct memory allocation to allocate HDFS Block Cache off heap
SOLR_OPTS="$SOLR_OPTS -Dsolr.hdfs.blockcache.direct.memory.allocation=true \
    -XX:MaxDirectMemorySize=30g -XX:+UseLargePages"

## Enable HDFS Short Circuit reads if possible
### Note: This path is different for Cloudera. It must be the path to the HDFS native libraries
SOLR_OPTS="$SOLR_OPTS -Djava.library.path=:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/usr/hdp/current/hadoop-client/lib/native"

## If Near Real Time (NRT) enable HDFS NRT caching directory, uncomment the following
#SOLR_OPTS="$SOLR_OPTS -Dsolr.hdfs.nrtcachingdirectory.enable=true \
# -Dsolr.hdfs.nrtcachingdirectory.maxmergesizemb=16 \
# -Dsolr.hdfs.nrtcachingdirectory.maxcachedmb=192" 
```

### 结论

在 Apache Hadoop HDFS 上运行 Apache Solr 可以获得合理的性能。如果预算允许，固态硬盘将为索引和查询提供更好的性能。最后，给定适当的内存量，即使旋转磁盘也能为 Apache Solr 提供足够的性能。

#### 参考文献

*   [https://wiki.apache.org/solr/SolrPerformanceProblems#SSD](https://wiki.apache.org/solr/SolrPerformanceProblems#SSD)
*   [https://sbdevel . WordPress . com/2013/06/06/内存被高估/](https://sbdevel.wordpress.com/2013/06/06/memory-is-overrated/)
*   [https://community . Horton works . com/questions/27567/write-performance-in-HDFS . html](https://community.hortonworks.com/questions/27567/write-performance-in-hdfs.html)
*   [https://blog . cloud era . com/blog/2014/03/the-truth-about-MapReduce-performance-on-SSDs/](https://blog.cloudera.com/blog/2014/03/the-truth-about-mapreduce-performance-on-ssds/)
*   [https://community . Horton works . com/questions/4858/Solr cloud-performance-HDFS-index data . html](https://community.hortonworks.com/questions/4858/solrcloud-performance-hdfs-indexdata.html)
*   [https://www . slide share . net/lucid works/Solr-on-HDFS-final-mark-miller](https://www.slideshare.net/lucidworks/solr-on-hdfs-final-mark-miller)
*   [https://issues.apache.org/jira/browse/SOLR-7393](https://issues.apache.org/jira/browse/SOLR-7393)
*   [http://blog . cloud era . com/blog/2017/06/Apache-Solr-memory-tuning-for-production/](http://blog.cloudera.com/blog/2017/06/apache-solr-memory-tuning-for-production/)
*   [http://blog . cloud era . com/blog/2017/06/Solr-memory-tuning-for-production-part-2/](http://blog.cloudera.com/blog/2017/06/solr-memory-tuning-for-production-part-2/)
*   [https://community . PLM . automation . Siemens . com/t5/Developer-Space/Running-Solr-on-S3/TD-p/449360](https://community.plm.automation.siemens.com/t5/Developer-Space/Running-Solr-on-S3/td-p/449360)
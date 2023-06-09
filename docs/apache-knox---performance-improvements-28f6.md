# Apache Knox -性能改进

> 原文：<https://dev.to/risdenk/apache-knox---performance-improvements-28f6>

### TL；速度三角形定位法(dead reckoning)

Apache Knox 1.2.0 应该会显著改进:

*   [Apache Hadoop WebHDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html) 写性能源于 [KNOX-1521](https://issues.apache.org/jira/browse/KNOX-1521)
*   [阿帕奇蜂巢](https://hive.apache.org/)和 GZip 性能归功于[诺克斯-1530](https://issues.apache.org/jira/browse/KNOX-1530)

如果您使用 Java 进行 TLS，那么您应该阅读这里。

### 概述

[Apache Knox](https://knox.apache.org/) 是一个反向代理，它简化了 Kerberos 安全 [Apache Hadoop](https://hadoop.apache.org/) 集群和其他相关组件前面的安全性。在 [knox 用户邮件列表](https://mail-archives.apache.org/mod_mbox/knox-user/201809.mbox/%3CCACEuXj475wey-AzxO%2Bqf162Qe7ChEB8oNj1Hd6O1E4VNd8cH7g%40mail.gmail.com%3E)和 [Knox 吉拉](https://issues.apache.org/jira/browse/KNOX-1221)上，有关于 Apache Knox 性能不如预期的报道。报告的两个案例特别关注 [Apache Hadoop WebHDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html) 的性能。我能够用 Apache Knox 再现速度变慢的情况，尽管结果令人惊讶。这篇博客详细介绍了 Apache Knox 1.2.0 的性能发现和改进。

### 再现性能问题

#### Apache Hadoop - WebHDFS

我开始调查报告的两个 WebHDFS 性能问题( [KNOX-1221](https://issues.apache.org/jira/browse/KNOX-1221) 和 [knox-user post](https://mail-archives.apache.org/mod_mbox/knox-user/201809.mbox/%3CCACEuXj475wey-AzxO%2Bqf162Qe7ChEB8oNj1Hd6O1E4VNd8cH7g%40mail.gmail.com%3E) )。我发现这个问题在我笔记本电脑上的虚拟机上很容易重现。我使用 curl 测试了 WebHDFS 的本地读写性能，并通过 Apache Knox 进行了测试。张贴到 [KNOX-1221](https://issues.apache.org/jira/browse/KNOX-1221) 的结果如下:

**WebHDFS 读取性能- 1GB 文件**

| 判例案件 | 传送速度 | 时间 |
| --- | --- | --- |
| 本机 WebHDFS | 252 兆字节/秒 | 3.8s |
| 诺克斯不带 TLS | 264 MB/秒 | 3.6s |
| 诺克斯 w/ TLS | 54mb/秒 | 20s |
| 并行 Knox w/ TLS | 2 大约 48MB/秒 | 22s |

**WebHDFS 写入性能- 1GB 文件**

| 判例案件 | 时间 |
| --- | --- |
| 本机 WebHDFS | 2.6s |
| 诺克斯不带 TLS | 29 岁 |
| 诺克斯 w/ TLS | 50 年代 |

这些结果非常令人吃惊，因为这些数字无处不在。一致的是，Knox 在用 TLS 读和不管 TLS 写时性能很差。另一个有趣的发现是，来自 Knox 的并行读取并没有变慢，而是每个连接被独立地限制。分析的详细内容见下文。

#### Apache HBase - HBase Rest

在分析了 WebHDFS 的性能之后，我决定研究其他服务，看看是否存在同样的性能下降。我查看了作为 KNOX-1524 一部分的 Apache HBase Rest。我决定在没有 TLS 的情况下测试 Knox，因为 WebHDFS 已经出现了速度变慢的情况。

**10 万行的扫描性能**

| 判例案件 | 时间 |
| --- | --- |
| HBase 外壳 | 13.9 秒 |
| HBase Rest -本机 | 3.4s |
| HBase Rest - Knox | 3.7s |

结果并不太令人惊讶。分析的更多细节可以在下面找到。

#### Apache 蜂巢-蜂巢 2

作为 [KNOX-1524](https://issues.apache.org/jira/browse/KNOX-1524) 的一部分，我还研究了使用和不使用 Apache Knox 时的 HiveServer2 性能。下面的测试也没有 TLS。

**选择* 20 万行的性能**

| 判例案件 | 时间 |
| --- | --- |
| HDFS DFS-文本 | 2.4s |
| 直线二进制 fetchSize=1000 | 6.2 秒 |
| 直线 http fetchSize=1000 | 7.5s |
| 直线 http 诺克斯 fetchSize=1000 | 9.9s |
| 直线二进制 fetchSize=10000 | 7.3 秒 |
| 直线 http fetchSize=10000 | 7.9s |
| 直线 http 诺克斯 fetchSize=10000 | 8.5 秒 |

这表明，使用 Knox 的 Hive 也有改进的空间。分析的详细内容见下文。

### 性能分析

#### Apache Hadoop - WebHDFS

在查看 WebHDFS 结果时，我发现禁用 TLS 会带来很大的性能提升。由于更改`gateway-site.xml`中的`ssl.enabled`是唯一的更改，这意味着 TLS 是读取性能差异的唯一因素。我研究了 TLS 的突堤性能，发现 JDK 存在已知的性能问题。有关更多详细信息，请参见下文。

WebHDFS 写性能差异不能归因于 TLS 性能，因为非 TLS Knox 也慢了大约 20 秒。在找到根本原因之前，我尝试了不同的 buffersizes 和升级 httpclient。性能差异可以归因于 Apache Knox 中的`UrlRewriteRequestStream`的问题。在一个`InputStream`上有多个读取方法，并且这些方法没有被实现。有关修复的详细信息，请参见下面的内容。

#### Apache HBase - HBase Rest

由于是用 [JRuby](https://www.jruby.org/) 编写的，并且不是使用 HBase 的最佳工具，因此 [HBase shell](https://hbase.apache.org/book.html#shell) 的缓慢是意料之中的。通常使用 [HBase Java API](https://hbase.apache.org/book.html#hbase_apis) 。在查看结果时，性能测试中没有出现大的瓶颈。Apache Knox 带来了一些开销，但大部分是由于额外的跳数。

#### Apache 蜂巢-蜂巢 2

我花了几次尝试来创建一个测试框架，让 be 能够轻松地测试这些变化。一个重要的发现是，对于相同的文件，Hive 明显比`hdfs dfs -text`慢。HiveServer2 本身可以有一些性能改进。另一个发现是，HiveServer2 二进制模式与 http 模式有很大的不同，默认的`fetchSize`为 1000。我的猜测是，当 HTTP 压缩被添加到 [HIVE-17194](https://issues.apache.org/jira/browse/HIVE-17194) 中时，`fetchSize`参数应该被增加以提高网络效率。当忽略二进制模式性能时，使用和不使用 Apache Knox 的 HiveServer2 http 模式之间仍然存在差异。关于性能改进的详细信息可以在这里找到。

### 性能提升

#### Java - TLS/SSL 性能

使用默认的 JDK TLS 实现时会有一些性能问题。我找到了一些关于 JDK 和码头的参考资料。

*   [https://nbsoftsolutions . com/blog/the-cost-of-TLS-in-Java-and-solutions](https://nbsoftsolutions.com/blog/the-cost-of-tls-in-java-and-solutions)
*   [https://nbsoftsolutions . com/blog/drop wizard-1-3-即将到来-TLS-改进](https://nbsoftsolutions.com/blog/dropwizard-1-3-upcoming-tls-improvements)
*   [https://webtide.com/conscrypting-native-ssl-for-jetty/](https://webtide.com/conscrypting-native-ssl-for-jetty/)

我可以用[consecrypt](https://github.com/google/conscrypt/)进行测试，发现 TLS 读写的性能下降现象消失了。我还测试了禁用 GCM，因为有资料表明 GCM 会导致 JDK 8 的性能问题。

*   [https://www . wowza . com/docs/how-to-improve-SSL-performance-with-Java-8](https://www.wowza.com/docs/how-to-improve-ssl-performance-with-java-8)
*   [https://stack overflow . com/questions/25992131/slow-AES-GCM-encryption-and-decryption-with-Java-8u 20](https://stackoverflow.com/questions/25992131/slow-aes-gcm-encryption-and-decryption-with-java-8u20)

测试不同 TLS 实现的结果如下:

| 判例案件 | 传送速度 | 时间 |
| --- | --- | --- |
| 本机 WebHDFS | 252 兆字节/秒 | 3.8s |
| 诺克斯不带 TLS | 264 MB/秒 | 3.6s |
| Knox w/consecrypt TLS | 245 MB/秒 | 4.2 秒 |
| 诺克斯 w/ TLS no GCM | 125 兆字节/秒 | 8.7s |
| 诺克斯 w/ TLS | 54.3 兆字节/秒 | 20s |

为 JDK 切换到不同的 TLS 实施提供商可以显著提高性能。这适用于任何使用 Java 的 TLS 处理。另一个方法是用非基于 Java 的负载平衡器终止 TLS 连接。最后，对于特定于性能的孤立用例，关闭 TLS 可能是可以的。在 Java 中使用 TLS 时，应该考虑这些选项。

#### Knox - WebHDFS 写性能

我创建了 [KNOX-1521](https://issues.apache.org/jira/browse/KNOX-1521) 来添加`UrlRewriteRequestStream`类中缺少的`read`方法。这允许基础流一次读取一个字节以上的数据。随着来自 [KNOX=1521](https://issues.apache.org/jira/browse/KNOX-1521) 的变化，WebHDFS 的写性能现在更接近本机 WebHDFS。 [KNOX-1521](https://issues.apache.org/jira/browse/KNOX-1521) 结果后更新的写入性能如下:

**WebHDFS 写入性能- 1GB 文件- KNOX-1521**

| 判例案件 | 时间 |
| --- | --- |
| 本机 WebHDFS | 3.3 秒 |
| 诺克斯不带 TLS | 29 岁 |
| 诺克斯不带 TLS，带诺克斯-1521 | 4.2 秒 |

#### Knox - GZip 处理

我发现 Apache Knox 在处理 GZip 压缩数据时有一些问题。我打开了 [KNOX-1530](https://issues.apache.org/jira/browse/KNOX-1530) 来解决潜在的问题。最大的改进是在 [KNOX-1530](https://issues.apache.org/jira/browse/KNOX-1530) 之后的 Knox 不会解压缩不需要重写的数据。这消除了大量的处理过程，应该可以提高 Knox 在其他用例中的性能，比如从 WebHDFS 中读取压缩文件，以及为 ui 处理 JS/CSS 压缩文件。在解决了 [KNOX-1530](https://issues.apache.org/jira/browse/KNOX-1530) 问题后，在有和没有 Apache Knox 的情况下，http 模式下 Apache Hive HiveServer2 的[性能大致相同。](https://issues.apache.org/jira/browse/KNOX-1524?focusedCommentId=16673639&page=com.atlassian.jira.plugin.system.issuetabpanels%3Acomment-tabpanel#comment-16673639)

**用[KNOX-1530](https://issues.apache.org/jira/browse/KNOX-1530)T3 选择*性能为 20 万行**

| 判例案件 | 时间 |
| --- | --- |
| HDFS DFS-文本 | 2.1s |
| 直线二进制 fetchSize=1000 | 5.4s |
| 直线 http fetchSize=1000 | 6.8s |
| 直线 http 诺克斯 fetchSize=1000 | 7.7s |
| 直线二进制 fetchSize=10000 | 6.8s |
| 直线 http fetchSize=10000 | 7.7s |
| 直线 http 诺克斯 fetchSize=10000 | 7.8s |

默认的 1000 会降低 HTTP 模式的速度，因为需要重复请求才能得到更多的结果。

### 结论

通过重现 WebHDFS 的性能瓶颈，表明我们可以提高 Knox 的性能。由于 [KNOX-1521](https://issues.apache.org/jira/browse/KNOX-1521) 的变化，Apache Knox 1.2.0 的 WebHDFS 写性能应该会显著提高。由于 [KNOX-1530](https://issues.apache.org/jira/browse/KNOX-1530) 具有更好的 GZip 处理，Hive 性能在 Apache Knox 1.2.0 中应该会更好。Apache Knox 1.2.0 应该很快就会发布，带来这些性能改进和更多改进。

我将我使用的性能测试[贴在这里](https://github.com/risdenk/knox-performance-tests)，这样它们就可以被用来寻找其他性能瓶。性能基准测试应该是可重复的，我将很快使用它们进行更多的性能测试。

到目前为止所做的性能测试是为了与本机端点进行比较，并不是为了显示绝对最佳的性能数据。这种类型的测试发现了一些已经为 Apache Knox 1.2.0 解决的瓶颈。到目前为止，所有的测试都没有对后端进行 Kerberos 身份验证。当使用 Kerberos 身份验证时，可能会有额外的性能瓶颈，这将是我要研究的另一个领域。
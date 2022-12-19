# Apache HBase - Thrift 1 服务器 SPNEGO 改进

> 原文：<https://dev.to/risdenk/apache-hbase---thrift-1-server-spnego-improvements-4d9d>

### 概述

[Apache HBase](https://hbase.apache.org/) 提供对大型数据集执行实时随机读/写访问的能力。HBase 构建在 [Apache Hadoop](https://hadoop.apache.org/) 之上，可以扩展到数十亿行和数百万列。Apache HBase 的功能之一是一个[节俭服务器](https://hbase.apache.org/book.html#thrift)，它提供了从任何支持[节俭的语言与 HBase 交互的能力。有两个不同版本的 HBase 节俭服务器 v1 和 v2。这篇博文关注的是 v1，因为这是集成了](https://thrift.apache.org/) [Hue](https://gethue.com/) 的版本。

### 阿帕奇 HBase 和 Hue

Hue 通过 v1 节俭服务器支持 Apache HBase。Hue UI 允许轻松地与 HBase 进行交互，以进行查询和插入。这是一种快速而简单的 HBase 入门方式。缺点是当使用 HBase thrift v1 服务器时，对 Kerberos 的支持有限。

### HBase 节俭 V1 和 Kerberos

已经有一些关于让 HBase Thrift V1 服务器与 Kerberos 正常工作的帖子。在许多情况下，解决方案是合并 HTTP 主体和 HBase 服务器主体的 keytabs。另一个解决方案是添加 HTTP 主体作为代理用户。这两种解决方案都需要不必要的额外工作。HTTP 主体应该只用于对 SPNEGO 进行身份验证。HBase 服务器主体应该用于与 HBase 的其余部分进行身份验证。我在比较了 Apache Hive HiveServer2 thrift 实现和 HBase thrift 服务器实现之后发现了这一点。

### 提高 HBase 节俭 V1 实现

我给 hbase 用户邮件列表发了电子邮件，看看我的发现是否可信，或者我是否遗漏了什么。Josh Elser 对其进行了审核，并表示这一更改会很有用。我打开了 [HBASE-19852](https://issues.apache.org/jira/browse/HBASE-19852) ，并在接下来的几个月里拼凑了一个工作补丁。事实证明，我们环境的快速补丁花费了一些努力来对 Apache HBase 本身做出贡献。该修补程序实现了以下功能:

*   在检查 Kerberos 凭证之前，通过预先检查授权头来避免现有的 401 try/catch 块
*   添加`hbase.thrift.spnego.principal`和`hbase.thrift.spnego.keytab.file`以允许专门为节俭服务器配置 SPNEGO 主体

第一个变化是，当授权头为空时，这可以防止日志中充满关于 Kerberos 身份验证失败的消息。第二项更改允许在 hbase-site.xml 文件中配置 SPNEGO 主体。然后，节俭服务器将被配置为使用 SPNEGO 主体和 keytab 进行 HTTP 身份验证。这避免了合并 keytab 的需要，并允许管理员使用主机上现有的 SPNEGO 主体和 keytab(类似于 Ambari 的设置)。

### 结论

[HBASE-19852](https://issues.apache.org/jira/browse/HBASE-19852)2018 年 6 月审核合并。它是 HBase 2.1.0 及更高版本的一部分。Apache HBase 社区是一个很好的合作伙伴，因为我花了几个月的时间开发补丁，他们很有耐心。新的配置选项允许 HBase Thrift V1 服务器无缝地与 Kerberos 和 Hue 一起工作。不再需要合并键标签或执行其他解决方法。这一改变已经使用了一年多，成功地将 Hue HBase 浏览器与 HBase 和 Kerberos 结合使用。
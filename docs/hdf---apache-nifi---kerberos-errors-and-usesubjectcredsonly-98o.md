# HDF -阿帕奇 NiFi - Kerberos 错误和使用 SubjectCredsOnly

> 原文：<https://dev.to/risdenk/hdf---apache-nifi---kerberos-errors-and-usesubjectcredsonly-98o>

### 概述

我的团队支持 [Hortonworks 数据流(HDF)](https://hortonworks.com/products/data-platforms/hdf/) 用于各种流用例。HDF 的核心组件之一是[阿帕奇 NiFi](https://nifi.apache.org/) 。HDF 和我们的 [Hortonworks 数据平台(HDP)](https://hortonworks.com/products/data-platforms/hdp/) 都由 [Kerberos](https://web.mit.edu/kerberos/) 保护。Kerberos 安全性确保用户在访问资源之前经过身份验证。我们有几个问题无法确定根本原因是什么。一个问题是，当我们试图[停止一个 NiFi 处理器时，它会得到一个阻塞的线程](https://community.hortonworks.com/questions/155101/nifi-puthdfs-processor-stuck-and-not-able-to-relea.html)，直到我们重新启动那个 NiFi 节点才释放。另一个问题是，我们会间歇性地遇到错误，这些错误会随着时间的推移自行解决。这两者都指向 Kerberos，因为它们只会发生在像 HDFS、Hive 和 HBase 这样的处理器上。

### 调试卡住线程和`GSSException`错误

我们在 2017 年 9 月向 [Hortonworks](https://hortonworks.com/) 开了一张支持票，试图找到这些错误的根本原因。我们试图消除我们这边的所有外部变量，但是不能排除 Active Directory Kerberos 的变化。当问题第一次出现时，我们在 HDF 2.x 上，我们被告知 HDF 3.0 将修复这些与 Kerberos 相关的错误。升级到 HDF 3.0 后，同样的错误发生了，这让我们很担心。

我们注意到 HDF 的其他用户也遇到了同样的问题，比如[线程停滞](https://community.hortonworks.com/questions/155101/nifi-puthdfs-processor-stuck-and-not-able-to-relea.html)。我推动 Hortonworks 试图找到这些问题的根源。2018 年 1 月，我们联系到了@joewitt， [Apache NiFi](https://nifi.apache.org/) 的创作者之一，他告诉我们他们正在非常努力地重现这个问题。到目前为止，我们已经向 Hortonworks 提供了大量的日志和各种调试设置，看看我们是否能找到问题。

### 修复 Kerberos 相关错误

2018 年 1 月，Hortonworks 在@joshelser 的帮助下取得了突破，确定了 Kerberos 问题的根本原因。卡住线程的问题原来是 [NIFI-4318](https://issues.apache.org/jira/browse/NIFI-4318) ，它描述了`javax.security.auth.useSubjectCredsOnly`必须总是被设置为真。如果`javax.security.auth.useSubjectCredsOnly`没有设置为 true，那么使用 Kerberos 的处理器可能会由于等待认证提示的线程阻塞而挂起。我们将`javax.security.auth.useSubjectCredsOnly`设置在我们的 NiFi `bootstrap.conf`中。您可以使用以下命令检查`javax.security.auth.useSubjectCredsOnly`是否被设置为假:

```
sudo -u nifi /usr/jdk64/jdk1.8.0_112/bin/jinfo NIFI_PID | grep javax.security.auth.useSubjectCredsOnly 
```

Enter fullscreen mode Exit fullscreen mode

原来这是问题的一部分，因为`GSSExceptions`继续发生。如 [NIFI-4350](https://issues.apache.org/jira/browse/NIFI-4350) 中所述，我们可以通过调整 Kerberos 处理器的重新登录周期来减少一些`GSSExceptions`。默认重新登录时间太长，导致 Kerberos 票证过期，从而导致`GSSExceptions`。

其余的`GSSExceptions`是由 Apache NiFi 以非线程安全的方式使用 [`UserGroupInformation` (UGI)](http://hadoop.apache.org/docs/stable/api/org/apache/hadoop/security/UserGroupInformation.html) 造成的。 [NIFI-4323](https://issues.apache.org/jira/browse/NIFI-4323) 有所有血淋淋的细节，上面提到的许多问题都被永久修复了。PR 2360 展示了实现这一点的所有代码更改。 [NIFI-4323](https://issues.apache.org/jira/browse/NIFI-4323) 被并入[阿帕奇 NiFi](https://nifi.apache.org/) 1.5.0 被 HDF 3.1.0 捡走。

### 结论

如果您正在使用带有 Kerberos 安全 Hadoop 集群的 [Hortonworks 数据流(HDF)](https://hortonworks.com/products/data-platforms/hdf/) 或 [Apache Nifi](https://nifi.apache.org/) ，那么您应该升级到 HDF 3.1.0 或 Apache NiFi 1.5.0。[HDF 3.1 的 Apache Ambari](https://ambari.apache.org) 管理也将为`bootstrap.conf`添加所需的属性。我们还没有完全验证我们在 HDF 3.1 上的所有用例，但是到目前为止，结果是令人鼓舞的。
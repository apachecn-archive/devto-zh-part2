# Kerberos 调试

> 原文：<https://dev.to/risdenk/kerberos-debugging-29ne>

### 概述

从 2013 年到 2017 年，我在 [Avalon Consulting，LLC](https://www.avalonconsult.com) 工作，担任 [Hadoop](https://hadoop.apache.org) 顾问。在此期间，我与许多客户合作，并为 [Hortonworks](https://hortonworks.com/) 和 [Cloudera](https://www.cloudera.com/) 保护了( [TLS/SSL](https://en.wikipedia.org/wiki/Transport_Layer_Security) 、 [LDAP](https://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol) 、 [Kerberos](https://web.mit.edu/kerberos/) 等)相当多的 Hadoop 集群。已经有一些关于调试 Kerberos 问题的帖子，如[@ steveloughran](https://dev.to/steveloughran)[“Hadoop 和 Kerberos:门外的疯狂”](https://steveloughran.gitbooks.io/kerberos_and_hadoop/content/)。这篇文章涵盖了我多年来收集的一些技巧，这些技巧一般适用于 Kerberos，也适用于 Apache Hadoop。

### 增加`kinit`详细度

默认情况下，`kinit`不显示任何调试信息，失败时通常会返回一个模糊的错误。以下命令将启用标准输出的详细日志记录，这有助于调试。

```
KRB5_TRACE=/dev/stdout kinit -V 
```

Enter fullscreen mode Exit fullscreen mode

### Java Kerberos/KRB5 和 SPNEGO 调试系统属性

处理 Kerberos 的 Java 内部类具有打开调试日志记录的系统属性。这些属性启用了大量的调试，因此应该只在试图诊断问题时打开，然后关闭。如果需要，它们也可以组合使用。

第一个属性处理 [Kerberos](https://web.mit.edu/kerberos/) 错误，并可以帮助解决错误配置的 KDC 服务器、`krb5.conf`问题和其他问题。

```
-Dsun.security.krb5.debug=true 
```

Enter fullscreen mode Exit fullscreen mode

第二个属性专门用于 Kerberos 安全 web 端点的 [SPNEGO](https://en.wikipedia.org/wiki/SPNEGO) 调试。SPNEGO 可能很难调试，但是这个标志可以帮助启用额外的调试日志记录。

```
-Dsun.security.spnego.debug=true 
```

Enter fullscreen mode Exit fullscreen mode

这些属性可以用 [Apache Hadoop](https://hadoop.apache.org/) 和相关组件的`*_OPTS`变量来设置，如下例:

```
HADOOP_OPTS="-Dsun.security.krb5.debug=true" #-Dsun.security.spnego.debug=true" 
```

Enter fullscreen mode Exit fullscreen mode

### Hadoop 命令行调试日志

大多数 [Apache Hadoop](https://hadoop.apache.org/) 命令行工具(即:`hdfs`、`hadoop`、`yarn`等)使用相同的底层机制进行日志记录`Log4j`。`Log4j`不允许动态调整日志级别，但是允许在使用命令之前调整日志记录器。Hadoop 将 root logger 作为环境变量`HADOOP_ROOT_LOGGER`公开。这可用于在不改变`log4j.properties`的情况下改变特定命令的记录。

```
HADOOP_ROOT_LOGGER=DEBUG,console hdfs ... 
```

Enter fullscreen mode Exit fullscreen mode

### 调试 Hadoop 用户和组

使用 [Apache Hadoop](https://hadoop.apache.org/) 的用户通常通过 [Kerberos](https://web.mit.edu/kerberos/) 进行身份验证，如这里的[所解释的](https://hadoop.apache.org/docs/stable/hadoop-auth/index.html)。通过身份验证后，用户的用户名将用于确定组。使用 [Apache Hadoop](https://hadoop.apache.org/) 的组可以使用 [Hadoop 组映射](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/GroupsMapping.html)以多种方式进行配置。调试 Apache Hadoop 认为您的用户和组是什么对于正确设置安全性至关重要。

第一个命令接受一个用户主体，并根据配置的 [`hadoop.security.auth_to_local`规则](https://community.hortonworks.com/articles/14463/auth-to-local-rules-syntax.html)返回用户名。

```
hadoop org.apache.hadoop.security.HadoopKerberosName USER_PRINCIPAL 
```

Enter fullscreen mode Exit fullscreen mode

第二个命令获取用户名，并确定与它相关联的组。这使用配置的 Hadoop 组映射来确定组是什么。

```
hdfs groups USERNAME 
```

Enter fullscreen mode Exit fullscreen mode

第三个命令使用当前经过身份验证的用户，并打印出当前用户的 UGI。它还可以使用 principal 和 keytab 来打印关于 UGI 信息。

```
hadoop org.apache.hadoop.security.UserGroupInformation
hadoop org.apache.hadoop.security.UserGroupInformation "PRINCIPAL" "KEYTAB" 
```

Enter fullscreen mode Exit fullscreen mode

第四个命令 [`KDiag`](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SecureMode.html#Troubleshooting_with_KDiag) 相对较新，因为它是在 [HADOOP-12426](https://issues.apache.org/jira/browse/HADOOP-12426) 中引入的，并在 [Apache Hadoop](https://hadoop.apache.org/) 2.8.0 中首次发布。这个命令将一些额外的调试工具打包在一起，并检查常见的与 Kerberos 相关的错误配置。

```
# Might also set HADOOP_JAAS_DEBUG=true and set the log level 'org.apache.hadoop.security=DEBUG'
hadoop org.apache.hadoop.security.KDiag 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

在处理 Kerberos 和分布式系统的战斗中，有一半以上是知道在哪里查找和生成什么日志。有了正确的日志，就有可能调试问题并快速解决问题。
# 采用日立内容平台的 Apache Hadoop S3A(HCP)

> 原文：<https://dev.to/risdenk/apache-hadoop-s3a-with-hitachi-content-platform-hcp-2aeb>

感谢@quirogadf 对此进行深入研究，感谢 Hitachi 迅速找到 405 错误的根本原因。

### 概述

[日立内容平台(HCP)](https://www.hitachivantara.com/en-us/products/cloud-object-platform/content-platform.html) 是一个具有多种 API(兼容 NFS、CIFS、REST、WebDAV 和 S3)的存储设备。由于其中一个 API 是一个 [S3 兼容端点](https://knowledge.hds.com/Documents/Storage/Content_Platform/7.1.2/Manage_an_HCP_system/Using_Hitachi_API_for_Amazon_S3)，我们想要测试我们是否可以将我们现有的 [Apache Hadoop](https://hadoop.apache.org/) 副本与 HCP 集成。我们直接和间接地利用 [`distcp`](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 作为我们的拷贝。随着 Hadoop [支持 S3 兼容的终端](https://hadoop.apache.org/docs/stable/hadoop-aws/tools/hadoop-aws/index.html)，我们开始观察它如何与 HCP 一起工作。

### Apache Hadoop S3 支持的历史

#### `s3://`文件系统

Apache Hadoop 最初支持 S3 的 [`s3://`文件系统](https://hadoop.apache.org/docs/stable/hadoop-aws/tools/hadoop-aws/index.html#S3)。它使用 blob 存储，并且只与支持它的应用程序一起工作。该文件系统将在 Hadoop 3 中删除，不再推荐使用。

#### `s3n://`文件系统

继`s3://`文件系统之后，Apache Hadoop 开发了 [`s3n://`文件系统](https://hadoop.apache.org/docs/stable/hadoop-aws/tools/hadoop-aws/index.html#S3N)。`s3n://`文件系统支持原生 S3 对象，并且支持整个 Hadoop 2.x 系列。即使对最初的`s3://`文件系统进行了许多改进，仍然有许多问题使它在许多情况下不可用。不推荐使用`s3n://`文件系统，而是转移到`s3a://`文件系统。

#### `s3a://`文件系统

[`s3a://`文件系统](https://hadoop.apache.org/docs/stable/hadoop-aws/tools/hadoop-aws/index.html#S3A)正在积极开发中，并试图消除`s3n://`文件系统的许多现有限制。它最初是在 Hadoop 2.6 中引入的，在最初的 2.6 版本和最新的 2.9.x 版本之间经历了大量的开发。最大的变化是`s3a://`不再依赖 [JetS3t 库](http://www.jets3t.org/)，而是使用原生 [AWS S3 Java 库](https://aws.amazon.com/sdk-for-java/)。另一个很大的好处是，对于 S3 兼容的端点，无需更改需要重启的集群配置就可以设置配置。目前推荐使用 Apache Hadoop 时使用`s3a://`与 S3 交互。

### 使用 HCP 7.x 测试 S3A(不支持多部分)

基于当前 Apache Hadoop S3 的建议和对现有实现的`s3a://`的改进，我们希望将`s3a://`用于 HCP。当我们第一次开始测试时，安装的是 HCP 7.x 版本。此版本不支持 S3 多部分协议，该协议限制了可以发送的数据大小。我们能够通过一些简单的配置项目将 HCP 与`s3a://`连接起来:

*   `fs.s3a.access.key`
*   `fs.s3a.secret.key`
*   `fs.s3a.endpoint`
    *   这是 HCP 租户的网址(即:`tenant.HCP_HOSTNAME`)
*   `hdfs://NAMESPACE/path`
    *   需要在 S3 支持下在 HCP 设置命名空间。

虽然我们能够用`s3a://`连接和存储数据，但我们渴望 HCP 8.x 能增加对 S3 多部分的支持。

### 使用 HCP 8.x 测试 S3A(支持多部分)

今年早些时候，安装了 HCP 8.x，其中包括对 S3 多部分的支持。我们渴望尝试多部分，因为这将支持大文件，并提高大型上传的性能。我们最初在 Apache Hadoop 2.7.3 和 aws-sdk-java 版本 1.10.6 中遇到了 multipart 的问题。对于超过多部分大小的文件，导致以下错误:

```
18/02/12 09:31:12 DEBUG amazonaws.request: Received error response: com.amazonaws.services.s3.model.AmazonS3Exception: HTTP method PUT is not supported by this URL (Service: null;
Status Code: 405; Error Code: 405 HTTP method PUT is not supported by this URL; Request ID: null), S3 Extended Request ID: null 
```

Enter fullscreen mode Exit fullscreen mode

我们遵循了请求结构，它符合 HCP 文档解释的内容。我们与日立合作，确定问题出在 AWS SDK 版本上。根据日立公司的说法，在版本 1.10.38 之前的 aws-java-sdk-s3 中，`Content-Type`标头设置不正确。版本 1.10.38 将`Content-Type`标头更正为“应用程序/八位字节-strem”。

我们更新了 AWS SDK 版本 1.10.77，并再次用 HCP 测试了`s3a://`。我们成功地上传了超过 700GB 的文件，支持多部分，这在以前是失败的。请注意，在某些情况下，更新 AWS SDK 版本[可能会导致错误](https://hadoop.apache.org/docs/stable/hadoop-aws/tools/hadoop-aws/index.html#Missing_method_in_com.amazonaws_class)。

### 接下来是什么？

由于 HCP 8.x 和`s3a://`与`distcp`一起工作用于简单拷贝，我们想探索将 HCP 用于其他用例。在某些情况下，我们可以从 HCP 中提取数据，以便与其他数据集一起处理。检查 HCP、`s3a://`和类似[阿帕奇蜂巢](https://hive.apache.org/)的整合是我们将来要关注的事情。
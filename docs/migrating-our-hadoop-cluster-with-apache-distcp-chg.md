# 使用 Apache DistCp 迁移我们的 Hadoop 集群

> 原文：<https://dev.to/eranelbaz/migrating-our-hadoop-cluster-with-apache-distcp-chg>

#### 从不安全集群到远程安全集群

### 我们的使命是什么？

我们需要将我们的**整个 Hadoop 集群**从不安全的 CDH 5.7.1 集群迁移到远程数据中心的安全的 CDH 5.13.1 集群。

我们想为我们的用例找到一个完整的产品。

[![](img/4e7bd16a8af21b339e7770832ecdfe24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uupJSoY---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/686/1%2AyPv_3AmCyKOyVZ2AZeYflA.png)

### 产品需求

*   密封地迁移我们的数据
*   迁移我们的 Apahce Hive Metastore。
*   需要在转移过程中显示 BI

### 那又有什么大不了的？

有几个框架可以处理这类问题:

*   [Cloudera BDR](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/cm_bdr_tutorials.html)
*   [阿帕奇猎鹰](https://falcon.apache.org)
*   甲骨文金门用 [HDFS 适配器](http://www.oracle.com/technetwork/middleware/goldengate/documentation/gg-java-adp-bigdata-hdfs-adp-2601138.pdf)和[蜂巢适配器](http://www.oracle.com/technetwork/middleware/goldengate/documentation/gg-java-adp-bigdata-hive-adp-2600938.pdf)
*   [甲骨文数据集成器](https://www.linkedin.com/pulse/moving-data-across-hadoop-clusters-using-oracle-issam-hijazi/)
*   还有更多我们没有提到的

然而，对于每个框架，我们都会遇到另一个退路..

*   Cloudera BDR 不支持不安全集群到安全集群
*   Apache Falcon 不支持 Cloudera 使用的当前 Hadoop 版本
*   Oracle 告诉我们这不是 Oracle Golden Gate 的使用案例，并建议我们使用 Oracle Data Integrator
*   我们对 Oracle Data Integrator 的体验并不是很好…

因此，我们发现自己在这个任务面前没有任何对我们有用的框架，很明显，我们必须从头开始。

### 用一些 Python 的 Apache DistCp 怎么样？

我们一直知道我们可以使用 Apache DistCp 从不安全的集群到安全的集群，但是我们想要一个经过测试和审查的完整产品，我们不想开发我们自己的“BDR”，尽管很明显我们必须开发我们自己的产品。

### 我们的流量

#### 数据复制

[![](img/d43defdf5c1f8fb6da69dbe91e2f4b3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a0mApoa1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/198/1%2A-ZlDA_I4wwsfV3WH7kT7bQ.png)

1.  扫描不安全的 HDFS——递归地，我们还需要知道要同步的子路径。我们有多少文件？路径大小？
2.  将 HDFS 扫描保存到数据库表。
3.  从安全集群运行 [Hadoop DistCp 命令](https://www.cloudera.com/documentation/enterprise/5-5-x/topics/cdh_admin_distcp_secure_insecure.html)。
4.  我们希望整个过程与我们的团队用户一起运行，因此我们需要更改目录权限。
5.  验证每个字节都已在群集间同步

我们决定每次分批运行 10 个最深子目录的 DistCp 命令，这个决定是由于资源限制而做出的…

#### Apahce Hive Metastore 同步

[![](img/588e3405ab2fceaa1b330b52817d7171.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_NI9HaTf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/151/1%2AZkszg-AJQgVuYvyPWLu8OQ.png)

1.  列出我们不安全集群中的所有配置单元数据库。
2.  列出我们所有的桌子
3.  为每个表查询“显示创建表”。
4.  使用相同的查询来查询安全集群，以便创建表。
5.  运行无效元数据，以便将数据“加载”到 impala，并对 Hive 进行 msck 修复

### 总结

您不需要一个花哨的产品来迁移或复制您的数据，您总是可以使用相同的旧 Hadoop distcp 命令和一些 hive 查询。

尽情享受吧！

> 喜欢这个帖子？你想访问我的私人 github 项目？
> 通过[Patreon](https://www.patreon.com/eranelbaz)T3】支持我订阅我的 [YouTube 频道](https://www.youtube.com/channel/UCVUNeBGM5wZJKcOx0QwAaTA?sub_confirmation=1)
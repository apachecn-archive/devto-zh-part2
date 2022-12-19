# Apache Solr -参与其中-两周年纪念

> 原文：<https://dev.to/risdenk/apache-solr---getting-involved---two-year-anniversary-321j>

#### 2018 年 3 月更新

我最初是在 2016 年 5 月把这个内容发布到 LinkedIn [这里](https://www.linkedin.com/pulse/apache-solr-jdbc-how-get-involved-kevin-risden/)。在[宣布](http://mail-archives.apache.org/mod_mbox/lucene-dev/201603.mbox/%3CCAE4tqLPcNwyJpsD8UBUJ67-52TVWDq-GY7H1Bk8_C1RO_7KFgA@mail.gmail.com%3E)我成为 [Apache Lucene/Solr](https://lucene.apache.org/solr/) 提交者大约两周年之际，交叉发布到我的博客上，并做了一些小的编辑。

### 概述

我在 Apache Solr JDBC 博客系列中的前一篇文章展示了如何将 JDBC 工具如 [Apache Zeppelin](https://zeppelin.apache.org/) 连接到 [Apache Solr](https://lucene.apache.org/solr/) 。这篇文章描述了如何使用 Apache Solr 和 JDBC 驱动程序。这篇文章的灵感来自于伟大的 [Lucidworks](https://lucidworks.com/) 博客，作者是 [Hoss](https://lucidworks.com/blog/?a_name=hossman) 关于[对 Apache Solr](https://lucidworks.com/2012/03/26/14-ways-to-contribute-to-solr/) 做出贡献的 14 种方式。

### 完善文档

[Apache Solr 参考指南](https://lucene.apache.org/solr/guide/)包含了关于如何使用并行 SQL 和 JDBC 驱动程序连接 Apache Solr 的信息。文档总是可以改进的，以减少开始的时间。如果[参考指南](https://lucene.apache.org/solr/guide/)中有任何需要改进的地方，请在具体页面上留下评论，我们会对其进行审核。

除了参考指南之外，以下是一些可用于开始使用并行 SQL 和 JDBC 驱动程序的参考资料:

*   [阿帕奇索尔 JDBC -简介-凯文里斯登](///2018/03/17/apache-solr-jdbc-introduction-two-year-anniversary.html)
*   [阿帕奇 Solr JDBC - Java - Sematext](https://sematext.com/blog/solr-6-as-jdbc-data-source/)
*   阿帕奇索尔 JDBC - JDBC 工具公司-凯文里斯登

### 提问&报告 Bugs 改进

如果你有问题或者想要报告你遇到的任何错误，Apache Solr 有一个关于[社区](https://lucene.apache.org/solr/community.html)和如何使用 Solr 用户邮件列表的部分。solr-user 邮件列表由提交者和用户监控，所以你可以很快得到答案。在询问之前提供细节和搜索可以[改善你对邮件列表的体验](http://www.catb.org/esr/faqs/smart-questions.html)。一旦一个 bug 或改进被审查，它通常会被输入到 [Apache JIRA](https://issues.apache.org/jira/) 中。

### 帮助发展

除了文档之外，向 Apache Solr 贡献代码和补丁也是一种回报的方式。Apache Solr 有一个[如何贡献](https://wiki.apache.org/solr/HowToContribute)的指南，概述了从哪里开始。Apache Solr 是用 Java 写的，是开源的。开发任务存储在[阿帕奇 JIRA](https://issues.apache.org/jira/) 上，允许开发者看到需要改进的地方。浏览 JIRA 并找到一个你感兴趣的问题是一个很好的开始方式。下一步是确保问题可以重现，或者提供一个测试补丁来解决问题。提交者然后可以检查补丁并提交它。Apache Solr [社区](https://lucene.apache.org/solr/community.html)页面有关于 solr-dev 列表的信息，该列表专门用于 Apache Solr 代码库的贡献者。

一些与并行 SQL 和 JDBC 相关的 Apache Solr JIRAs 有:

*   [SOLR-8125 -伞票流& SQL 问题](https://issues.apache.org/jira/browse/SOLR-8125)
*   [SOLR-8659 -为更多 SQL 客户端改进 JDBC 驱动程序](https://issues.apache.org/jira/browse/SOLR-8659)
*   [SOLR-8593 -阿帕奇方解石优化器](https://issues.apache.org/jira/browse/SOLR-8593)

### 结论

我简要介绍了我是如何成为 Apache Solr 提交者的，介绍了 Apache Solr JDBC 驱动程序，以及一些如何使用 Apache Solr JDBC 驱动程序的分步指南。如果你感兴趣的话，请按照上面描述的一些技巧来参与进来。
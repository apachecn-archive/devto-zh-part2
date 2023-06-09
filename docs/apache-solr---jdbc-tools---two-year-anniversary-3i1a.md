# Apache Solr - JDBC 工具-两周年纪念

> 原文：<https://dev.to/risdenk/apache-solr---jdbc-tools---two-year-anniversary-3i1a>

#### 2018 年 3 月更新

我最初是在 2016 年 4-5 月把这个内容发布到 LinkedIn [这里](https://www.linkedin.com/pulse/apache-solr-jdbc-dbvisualizer-kevin-risden/)、[这里](https://www.linkedin.com/pulse/apache-solr-jdbc-squirrel-sql-kevin-risden/)、[这里](https://www.linkedin.com/pulse/apache-solr-jdbc-zeppelin-incubating-kevin-risden/)。在我成为 Apache Lucene/Solr 提交者的[公告](http://mail-archives.apache.org/mod_mbox/lucene-dev/201603.mbox/%3CCAE4tqLPcNwyJpsD8UBUJ67-52TVWDq-GY7H1Bk8_C1RO_7KFgA@mail.gmail.com%3E)2 周年之际，交叉发布到我的博客上。由于内容已经迁移到了 [Apache Solr 参考指南](https://lucene.apache.org/solr/guide/)中，这篇文章合并了三篇原始文章。

### 概述

我在 Apache Solr JDBC 上的前一篇文章介绍了这个特性，并介绍了一些可以连接到 Apache Solr 的 SQL 客户端和数据库可视化工具。这篇文章强调了你可以使用 Solr JDBC 驱动程序将 [DbVisualizer](https://www.dbvis.com/) 、[松鼠 SQL](https://squirrel-sql.sourceforge.net/) 和[阿帕奇齐柏林飞船](https://zeppelin.apache.org/)连接到阿帕奇 Solr。

### DbVisualizer

DbVisualizer 是一个基于 Java 的数据库查询和管理工具。该分步指南包含一些截图，展示了如何添加 Apache Solr JDBC 驱动程序，连接到 Solr，然后在 Solr 上执行一些 SQL 查询。这对应于 DbVisualizer 上的阿帕奇 Solr JIRA [SOLR-8521](https://issues.apache.org/jira/browse/SOLR-8521) 和[阿帕奇 Solr 参考指南页面。](https://lucene.apache.org/solr/guide/7_2/solr-jdbc-dbvisualizer.html)

### 松鼠 SQL

[松鼠 SQL](https://squirrel-sql.sourceforge.net/) 是一个基于 Java 的数据库查询和管理工具。分步指南包含一些截图，展示了如何添加 Solr JDBC 驱动程序，添加 Solr 的别名，连接到 Solr，然后在 Solr 上执行一些 SQL 查询。这对应于松鼠 SQL 上的阿帕奇 Solr JIRA [SOLR-8825](https://issues.apache.org/jira/browse/SOLR-8825) 和[阿帕奇 Solr 参考指南页面。](https://lucene.apache.org/solr/guide/7_2/solr-jdbc-squirrel-sql.html)

### 阿帕奇齐柏林飞艇

[Apache Zeppelin](https://zeppelin.apache.org) 是一款基于网络的笔记本电脑，支持交互式数据分析。该分步指南包含一些截图，展示了如何添加 Apache Solr JDBC 驱动程序作为解释器，在笔记本中使用解释器，然后使用笔记本执行一些查询。这对应于阿帕奇飞艇上的阿帕奇 Solr JIRA [SOLR-8824](https://issues.apache.org/jira/browse/SOLR-8824) 和[阿帕奇 Solr 参考指南页面。](https://lucene.apache.org/solr/guide/7_2/solr-jdbc-apache-zeppelin.html)
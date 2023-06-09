# 我的 Apache Lucene/Solr 提交者之旅——两周年纪念

> 原文：<https://dev.to/risdenk/my-journey-to-apache-lucenesolr-committer---two-year-anniversary-40ni>

#### 2018 年 3 月更新

我最初是在 2016 年 4 月把这个内容发布到 LinkedIn [这里](https://www.linkedin.com/pulse/my-journey-apache-lucenesolr-committer-kevin-risden/)。在[宣布](http://mail-archives.apache.org/mod_mbox/lucene-dev/201603.mbox/%3CCAE4tqLPcNwyJpsD8UBUJ67-52TVWDq-GY7H1Bk8_C1RO_7KFgA@mail.gmail.com%3E)我成为 [Apache Lucene/Solr](https://lucene.apache.org/solr/) 提交者两周年之际，交叉发布到我的博客上，并做了一些小的编辑。

### 简介

2013 年至 2017 年，我在 [Avalon Consulting，LLC](https://www.avalonconsult.com) 工作。我在加入 Avalon 大约 3 年后写了这篇文章，回顾我在那里的时光以及成为 Apache Lucene/Solr 提交者的经历。我在 Avalon 的时间是技术和项目的旋风，重点是搜索和 Hadoop 搜索。在这篇文章中，我想概述一下我的一些经历，以及是什么让我对[Apache Solr](https://lucene.apache.org/solr/)——特别是 Solr JDBC 驱动程序做出了贡献。

### 2013-Solr 和 Hadoop 简介

#### 2013 年春——加入阿瓦隆咨询有限责任公司

当我于 2013 年 1 月加入 Avalon Consulting，LLC 时，我很幸运地立即拥有了两位高级技术导师:Sam 和 Ryan。Sam 指导我搜索，Ryan 指导我大数据和 Hadoop。自从我专注于搜索和 Hadoop 以来，这种早期的导师关系塑造了我在 Avalon 的大部分职业生涯。

#### 2013 年春季——南太平洋大学/USAP &山姆

山姆开发了 [USP(统一搜索平台)](http://www.avalonconsult.com/unified-search-platform)和 [USAP(统一搜索和分析平台)](http://www.avalonconsult.com/usap)。这些 web 应用程序旨在使用搜索引擎进行分析，并找到关于您的数据的见解。Sam 解释了这两个项目背后的基本原理，以及他认为搜索将如何实现分析。USP 和 USAP 用于阿瓦隆的一些项目和许多演示。山姆当时并不知道，但 USAP 早于现在流行的弹性基巴纳应用程序。

#### 2013 年春季——安然电子邮件演示&瑞安

在我加入 Avalon 之前，Ryan 完成了一个大型保险公司的项目，处理了十亿封电子邮件。大约在同一时间，Avalon 需要一种方法来证明我们可以有效地处理大型数据集。Ryan 使用 Amazon Elastic MapReduce 和 Enron 电子邮件数据集构建了一个简单的例子。在我加入 Avalon 后，我改进了这个例子，并在安然电子邮件的顶部添加了 USP/USAP 接口。这提供了一个很好的演示界面，表明我们可以快速处理大量数据，同时还能获得有价值的见解。

早在 2013 年春天，Lucidworks 就对如何展示其基于 Apache Solr 的搜索平台 Lucidworks Search 的多种选择感兴趣。为此，Avalon 提供了我们的安然电子邮件演示，并在圣何塞举行的 2013 年 Strata 会议上为 Lucidworks 展台配备了工作人员。会议期间，我向各种与会者展示了安然电子邮件演示。

### 2013-2016 - Solr 和 Hadoop

#### 2013 年秋季- Solr Cloud & HBase Indexer

2013 年秋天，整合搜索和 Hadoop 搜索处于起步阶段。在一家大型电信公司，我负责三个用例，其中一个处理大量的全文。现有的解决方案使用了一个基于 Lucene 的定制搜索应用程序，但是这个应用程序不能满足他们的需求。客户希望在文本中搜索能够帮助他们更有效地服务客户的趋势。Hortonworks HDP 1.3 在发行版中没有包含搜索引擎。我评估了 Solr 和 Elasticsearch，由于 Solr 与 Hadoop 的集成更加成熟，所以我决定选择 Solr。当时，Cloudera 已经开始通过 Cloudera Search 将 Solr 放入他们的发行版中。在这个项目中，我集成了 NGData HBase 索引器和 Solr，以实现接近实时的全文摄取和搜索。

#### 2014 年秋季——2015 年春季——卡夫卡、斯托姆、HBase、索尔

在大约 3 年的时间里，Avalon 第二次帮助同一家保险公司将现有的遗留系统迁移到 Hadoop。我和我的同事 Trey 致力于实现一个基于 HDP 的近实时平台，用于接收通信信息。该项目集成了 Hadoop 生态系统的许多部分，为法律查询提供了规模和速度。该项目集成了 Solr 和 Hadoop，因此该系统可以支持电子通信的未来增长。这个项目所需的规模也需要回答关于如何重新索引和查询的问题，这些问题很难解决。该解决方案提供了近乎实时的文档接收，大大改善了旧式系统 8 小时的滞后。

#### 2015 年秋季——搜索与 Hadoop

事实证明，2015 年秋天，我做的项目变化很大，尤其是围绕搜索和 Hadoop 的项目。我和一家初创公司合作了一个纯搜索项目，他们需要帮助解决 Solr 性能问题。我能够在几天内将接收和查询的性能提高 10 倍以上。一家支付处理公司想要一个关于搜索和 Hadoop 的架构研讨会。这一概述表明，Hadoop 在短期内不是最佳选择，但搜索引擎可能有助于解决他们的分析问题。客户决定整合 Kafka 和 ELK stack，我可以帮助他们开始。这包括扩展建议以及如何最好地使用这些技术的指导。

#### 2016 年春季- Cloudera 搜索(Solr) & CDH

2016 年春天，我回到了一家制造公司，之前我在那里建立了 Hortonworks HDP 集群。这一次，Avalon 将 Cloudera CDH 与他们现有的 EDW 集成在一起，为搜索提供分析。该客户有一个从 Teradata 和 Microsoft SQL Server 向 Hadoop 和 Cloudera Search 拉入关系数据的现有流程，但该流程速度缓慢且不自动化。Avalon 简化了流程，并就如何最好地使用 Cloudera Search 进行分析提供了指导。

### 2015 年秋季-2016 年春季 Solr 开发

经过几年与搜索相关的多个项目和跟踪 Apache Solr 社区(JIRAs 和 Solr 邮件列表)后，我想贡献自己的一份力量。2015 年 10 月发生的几件事让我走上了贡献的道路。

[Erick Erickson](https://www.linkedin.com/in/erick-erickson-129a341) 在 2015 年 10 月的阿瓦隆周会上介绍了流式聚合。我读过关于流式聚合的文章，但不知道它们有多大的威力。在接下来的 2-3 周的活动中，Erick Erickson 的演示是第一个活动，包括在 Lucene/Solr Revolution 上会见提交者，以及改进现有的 SolrJ JDBC 驱动程序以与 DbVisualizer 配合使用。到了 11 月，我有了一个概念验证，展示了 Solr JDBC 驱动程序可以改进以支持 DbVisualizer，但我还没有时间把它提交回来。

2016 年 1 月，我联系了开发过并行 SQL 的[乔尔·伯恩斯坦](https://www.linkedin.com/in/bernsteinjoel)([SOLR-7560](https://issues.apache.org/jira/browse/SOLR-7560))和最初的 JDBC 驱动( [SOLR-7986](https://issues.apache.org/jira/browse/SOLR-7986) )协调改进 JDBC 驱动。我创建了 [SOLR-8502](https://issues.apache.org/jira/browse/SOLR-8502) 来跟踪这个进程。接下来的一个月，我将现有的 POC 分解成可管理的部分，并将这些部分提交审查。Joel 审阅了所有的 JIRA 子任务并提交了它们。几乎每一个新的每夜构建都证明了我在改进 Solr JDBC 驱动程序的正确道路上。SOLR-8502 于二月初完成，使 DbVisualizer 和其他一些 JDBC 客户的工作。然后我创建了 [SOLR-8659](https://issues.apache.org/jira/browse/SOLR-8659) 来为更多的 SQL 客户端继续测试和改进 SolrJ JDBC 驱动程序。

2016 年 3 月 13 日周日，我惊喜地收到了 Joel 的邮件，邀请我成为 Apache Lucene/Solr 提交者。短短几天后，我正式成为 Apache Lucene/Solr 提交者，并在社区邮件列表上被宣布。

### 接下来是什么？

我将在 Solr JDBC 驱动程序上交叉发布 LinkedIn 的原始文章，并逐步指导如何配置一些 SQL 客户端和数据库可视化工具，以通过 JDBC 驱动程序连接到 Solr。如果你有任何关于 Solr JDBC 驱动的问题或者想要帮忙，Solr 网站有一个关于[社区](https://lucene.apache.org/solr/resources.html#community)和如何使用 Solr 用户邮件列表的部分。
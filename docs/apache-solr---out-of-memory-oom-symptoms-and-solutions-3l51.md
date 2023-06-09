# Apache Solr -内存不足(OOM)症状和解决方案

> 原文：<https://dev.to/risdenk/apache-solr---out-of-memory-oom-symptoms-and-solutions-3l51>

### 概述

Apache Solr 是一个基于 [Apache Lucene](https://lucene.apache.org/solr/) 的全文搜索引擎。在过去的六年里，我一直在与 Apache Solr 合作。在这段时间里，Apache Solr 发布了多个主要版本，从 4.x、5.x、6.x、7.x 到即将到来的 8.x。基于 Java 的应用程序的一个常见问题是内存不足。已经有几篇关于这个话题的帖子，想重申一些症状和解决方法。

### 症状及解决方法

#### 大量的行

Apache Solr 是一个隐藏的得分引擎。这意味着它旨在为最终用户返回排名最好的文档。一个常见的用例是电子商务，其中前 5-10 名的结果需要匹配用户正在寻找的东西，否则他们会去别处。Solr 还可以用于分析领域，如果匹配一组搜索标准，需要返回大量结果集。用 Solr 可以返回大量的行，但是用`rows=1000000`不能。

Solr 的 [`rows`参数](https://lucene.apache.org/solr/guide/7_5/common-query-parameters.html#CommonQueryParameters-TherowsParameter)可用于返回 10 行以上的默认值。我见过用户成功地将`rows`参数设置为 100-200，没有看到任何问题。然而，将`rows`参数设置得更高会产生很大的内存问题，无论如何都要避免。Solr wiki 有关于请求大量行时内存消耗的详细信息[这里是](https://wiki.apache.org/solr/SolrPerformanceProblems#Asking_for_too_many_rows)。此外，较高的`rows`参数意味着 Solr Cloud 设置中的每个分片都需要向 leader 返回那么多行，以便进行最终排序和处理。即使没有遇到任何内存问题，这也会显著降低查询速度。

内存不足(OOM)错误通常发生在带有大的`rows`参数的查询之后。Solr 通常会工作得很好，直到查询到来。有时这很难追踪，但是查看 Solr 日志会有所帮助。增加堆来解决这个错误可能很诱人，但是要知道这并不能解决问题。下面是解决这个问题的一些想法。

**寻呼结果大启动**

与上面的大量行一样，Solr 可以用于返回大量行。我见过的一种模式不是使用大的`rows`参数，而是使用大的 [`start`参数](https://lucene.apache.org/solr/guide/7_5/common-query-parameters.html#CommonQueryParameters-TherowsParameter)，比如`start=1000000`。通过使用`rows`和`start`参数，许多用户认为他们将能够从 Solr 获得数百万个结果。这在测试环境中可以工作，但是对于实际的数百万行，这种模式将会失败。这很可能不会导致 OOM 内存问题，但结果不会很快返回。同样的解决方案在这种情况下也有帮助。更多详情请见此处。

#### 分面/排序/分组 OOM

Apache Solr 有一个很棒的特性，叫做 faceting，允许对索引中的术语进行计数。这可以以各种不同的方式使用。排序也是 Solr 的核心，因为我们希望能够根据用户的排序标准返回相关的结果。分面和排序的问题在于它们是对存储在索引中的未转换值进行的。对于不熟悉 Lucene 的用户来说，这意味着原始令牌需要可用，而不仅仅是文档中的位置。

当谈到内存使用时，如果 Solr 中的一个字段需要分面或排序，并且未转换的表示不可用，那么它将构建在堆上。该结果随后被缓存在[字段缓存](http://lucene.apache.org/solr/7_5_0/solr-core/org/apache/solr/uninverting/FieldCache.html)中。每次索引改变时，该字段都需要再次取消转换并存储在堆中。这可能会占用大量堆并导致内存不足(OOM)。这可能不会立即发生，但随着时间的推移，会有更多未转换的字段放入 FieldCache。

在分析过程中，当有人想用像 [Banana](https://github.com/lucidworks/banana) 这样的工具探索一个指数时，这种症状经常出现。Banana 是建立在分面和排序的基础上的，所以它要求索引中的大多数字段都不被转换。这会导致先前工作的 Solr 设置由于 OOM 错误而崩溃。

有好消息，从 Apache Lucene/Solr 4.0 开始，有一个新特性叫做 [DocValues](https://lucene.apache.org/solr/guide/7_5/docvalues.html) 。这里的解决方案部分提供了更多信息。

### 方案

#### 翻阅大量结果

与其使用`rows`或`start`参数来请求更多的行，不如使用 [`/export`处理程序](https://lucene.apache.org/solr/guide/7_5/exporting-result-sets.html#ExportingResultSets-The_exportRequestHandler)或 [`cursorMark`](https://lucene.apache.org/solr/guide/7_5/pagination-of-results.html) 更合适。`/export`处理程序确保结果以可扩展到数百万行的方式流回。`cursorMark`允许返回大量页面，而不会重复产生排序成本，使用大的`start`参数会出现这种情况。这也确保了不会一次返回大量的行。通过使用`/export`处理程序和`cursorMark`，您将能够检索大型结果集，而不会导致任何内存不足错误。

#### 对大型索引进行分面/排序/分组

Apache Lucene/Solr 4.0 引入了一个名为 [DocValues](https://lucene.apache.org/solr/guide/7_5/docvalues.html) 的新特性。DocValues 存储索引时分面/排序/分组所必需的值，避免了将它们存储在堆上的需要。这大大减少了运行 Solr 所需的堆的数量。为您可能需要进行 factet/sort/group 的所有字段启用 DocValues 的代价很小。Solr 6.x 默认为大多数字段启用了 DocValues。如果您在 Solr 5.x 上，您应该能够启用 DocValues，因为该功能已经存在。

### 调试

如果上面的解决方案都不起作用，那么知道如何调试相关问题是很有用的。我在以前的一篇文章[这里](///2017/12/18/ambari-infra-solr-ranger.html)中讲述了如何诊断 Solr 的内存不足问题。它包含了关于何处查找以及如何分析堆转储的详细信息。

### 结论

使用 Apache Solr 功能，您可以轻松地将搜索和检索扩展到更大的结果集。DocValues 和`/export`处理程序都启用 Solr 的[流表达式](https://lucene.apache.org/solr/guide/7_5/streaming-expressions.html)和[并行 SQL](https://lucene.apache.org/solr/guide/7_5/parallel-sql-interface.html) 特性。这意味着这些特性得到了很好的支持，并会随着时间的推移不断得到改进。

**参考文献**

一些相关的博客可能对以后的阅读有用:

*   [https://blog . trifork . com/2011/10/27/introducing-Lucene-index-doc-values/](https://blog.trifork.com/2011/10/27/introducing-lucene-index-doc-values/)
*   [http://blog . Florian-Hopf . de/2014/05/Solr-cache-sizes-eclipse-memory-analyzer . html](http://blog.florian-hopf.de/2014/05/solr-cache-sizes-eclipse-memory-analyzer.html)
*   [https://medium . com/@ sarkaramrit 2/frequent-out-of-memory-errors-in-Apache-Solr-36499 f84c 98 a](https://medium.com/@sarkaramrit2/frequent-out-of-memory-errors-in-apache-solr-36499f84c98a)
*   [https://wiki.apache.org/solr/SolrPerformanceProblems](https://wiki.apache.org/solr/SolrPerformanceProblems)
*   [https://www . elastic . co/guide/en/elastic search/guide/current/_ deep _ dive _ on _ doc _ values . html](https://www.elastic.co/guide/en/elasticsearch/guide/current/_deep_dive_on_doc_values.html)
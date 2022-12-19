# 了解 Elasticsearch 过滤器

> 原文：<https://dev.to/awj/understanding-the-elasticsearch-percolator-2j5l>

Elasticsearch 是一款功能强大的工具。他们的[文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)很棒，但是有些部分有点……出格。除此之外，这些年来一些功能已经发生了显著的变化，因此第三方的解释可能不再准确。

一个既不寻常又变化很大的奇妙特征是渗滤。我将尝试在当前实现的上下文中解释这个特性(撰写本文时的版本是 6.2.4)。你需要对 Elasticsearch 有一个基本的了解，特别是[映射](https://www.elastic.co/guide/en/elasticsearch/reference/6.2/mapping.html)和[搜索](https://www.elastic.co/guide/en/elasticsearch/reference/6.2/search-request-body.html)。

# 概念

Elasticsearch 的正常工作流程是将文档(作为 JSON 数据)存储在一个索引中，并执行搜索(也是 JSON 数据)来查询关于这些文档的索引。

简而言之，渗透逆转了这一点。您存储搜索并使用文档来查询关于这些搜索的索引。没错，但不是特别有行动价值的信息。这些年来，滤壶的结构已经发生了变化，到了我们可以给出更有用的解释的时候了。

现在，渗透围绕着[过滤器](https://www.elastic.co/guide/en/elasticsearch/reference/6.2/percolator.html)映射字段类型。这类似于任何其他字段类型，只是它希望您将搜索文档指定为值。当您存储数据时，索引会将这个搜索文档处理成可执行的形式，并保存起来以备后用。

[过滤查询](https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-percolate-query.html)获取一个或多个文档，并将结果限制为存储的搜索匹配至少一个文档的结果。在搜索时，过滤查询像任何其他查询元素一样工作。

# 深入

在幕后，这是按照您所期望的方式实现的:带有过滤字段的索引保持一个隐藏的(内存中的)索引。您的渗滤液查询中列出的文档首先被放入该索引中，然后对该索引执行一个普通的查询，以查看原始渗滤液字段文档是否匹配。

需要记住的重要一点是，这个隐藏的索引是从原始的 percolator 索引中获取映射的。所以用于过滤查询的索引需要有适合原始数据和查询文档数据的映射。

这引入了一点管理问题，因为索引数据和过滤查询文档可能以不同的方式使用同一个字段。一个简单的答案是使用[对象类型](https://www.elastic.co/guide/en/elasticsearch/reference/6.2/object.html)将与过滤相关的映射从普通文档映射中分离出来。

假设您使用的查询最初是为实际文档的另一个索引编写的，那么最有意义的做法是将数据直接隔离到渗滤液索引中，并将根级别交给渗滤液查询文档的映射定义。

此外，因为渗滤液字段被解析为搜索和保存在索引时间，您可能需要重新索引渗滤液文件后升级，以利用任何优化系统。

# 举个例子

在我看来，percolator 示例是使该工具难以理解的主要原因之一。它们往往过于简单，以至于很难区分各个部分。

在这个例子中，我们将为玩具建立一个保存期限和价格搜索的索引。其背后的想法是，用户应该能够输入一个搜索词和一个最高价格，然后一旦匹配该词的东西低于这个价格，就会收到通知。用户还应该能够打开和关闭这些通知。

下面的映射实现了一个过滤索引来支持这个特性。与保存的搜索本身相关的字段位于`search`对象中，而与原始玩具相关的字段位于映射的根级别。

```
{  "mappings":  {  "_doc":  {  "properties":  {  "search":  {  "properties":  {  "query":  {  "type":  "percolator"  },  "user_id":  {  "type":  "integer"  },  "enabled":  {  "type":  "boolean"  }  },  },  "price":  {  "type":  "float"  },  "description":  {  "type":  "text"  }  }  }  }  } 
```

下面是代表存储搜索的文档的样子:

```
{  "_id":  1,  "search":  {  "user_id":  5,  "enabled":  true,  "query":  {  "bool":  {  "filter":  [  {  "match":  {  "description":  {  "query":  "nintendo switch"  }  }  },  {  "range":  {  "price":  {  "lte":  300.00  }  }  }  ]  }  }  }  } 
```

注意，我们只在`search`对象字段中存储数据。`price`和`description`的映射只是为了支持过滤查询。

在查询时，我们希望同时使用普通对象字段和“特殊”percolator 字段。该查询将在用户的搜索中进行检查，以查看哪些当前启用的搜索与该文档匹配。

```
{  "query":  {  "bool":  {  "filter":  [  {  "percolate"  :  {  "field"  :  "search.query",  "document"  :  {  "description"  :  "Nintendo Switch",  "price":  250.00  }  }  },  {  "term":  {  "search.enabled":  true  }  },  {  "term":  {  "search.user_id":  5  }  }  ]  }  }  } 
```

请注意，它将文档与字段中存储的查询的过滤匹配与常规术语查询相结合，以限制我们根据文档的启用状态和用户 id 测试哪些文档。

# 一些附加的想法

由于运行查询作为解析渗滤液过滤器的一部分所涉及的工作，您可能需要额外注意渗滤液索引的碎片/副本。每个碎片通过减少要评估的带有搜索功能的文档数量，减少了任何一台机器可能必须运行的查询数量。

过滤查询可以选择从集群中的另一个索引获取文档。这采用了字面量 GET 请求的形式，所以试图将两个索引的碎片保存在相同的节点上没有太大的好处。
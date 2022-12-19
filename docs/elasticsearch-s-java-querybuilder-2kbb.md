# Elasticsearch 的 Java QueryBuilder

> 原文：<https://dev.to/solacedevs/elasticsearch-s-java-querybuilder-2kbb>

[![](img/7eac47fb5b513d45efa2de16d75bf995.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hua1PEst--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWEQxGI2m3O-taz4FaXmGnA.png)

在 [Solace PubSub+ Cloud](https://cloud.solace.com/) 中，我们很早就开始存储指标，以备记账和计费之用。问题是，我们不太确定将使用哪些指标，也不确定需要什么样的查询来支持我们的会计和计费需求。

我们选择 Elasticsearch 进行存储搜索，因为我们相信它强大的搜索功能和可扩展性。然而，我们严重低估的一个方面是它奇妙的 Java API。虽然它通常是 Elasticsearch 的 REST API 的一个门面，但一个特别聪明的功能一直在帮助我们以巨大的速度和灵活性构建我们的 metrics 微服务，而不牺牲健壮性。

当我们第一次开始使用 Elasticsearch 时，我们以一种非常简单的方式构建查询:

```
BoolQueryBuilder()
 .must(QueryBuilders.termQuery("metricName", "Host"))
 .must(QueryBuilders.termQuery("metricType", "DiskSpace"))
 .must(QueryBuilders.termQuery("organizationId", organizationId))
 .must(QueryBuilders.rangeQuery("startTime").gte(startTime))
 .must(QueryBuilders.rangeQuery("endTime").lte(endTime)); 
```

我们最终意识到，我们通常必须在查询中附加一个组织的 ID 和一些时间范围，所以我们将其抽象出来，只需要给出查询中特定于指标的部分。

这在一开始是可行的，但是我们不希望每次我们需要计算一个新的度量或者稍微改变一个现有的度量时都必须编辑代码。这时我们发现了 [Elasticsearch 的包装查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-wrapper-query.html)。

从表面上看，这是一个简单的功能，您可以向 QueryBuilder 对象提供一个 JSON 字符串。大概是这样:

```
{
 "bool" : {
   "must" : [
     { "terms" : { "metricName" : ["Host"] } },
     { "terms" : { "metricType" : ["DiskSpace"] } }
   ]
 }
} 
```

像这样输入到 QueryBuilder 中:

```
QueryBuilders.wrapperQuery(json); 
```

下一个问题是如何开始扩展查询以跨组织 id 和时间段进行搜索。直觉反应可能是在 JSON 字符串中的某处添加一个标记来替换，但这正是 Elasticsearch API 的亮点。

您可能已经注意到，包装器查询只是另一个 QueryBuilder，这意味着您获得了一个构建器，您可以在其上简单地添加更多的参数。这让我们可以重用大部分的抽象概念，将组织 ID 和时间段添加到我们的指标查询中:

```
BoolQueryBuilder()
 .must(QueryBuilders.wrapperQuery(json))
 .must(QueryBuilders.termQuery("organizationId", organizationId))
 .must(QueryBuilders.rangeQuery("startTime").gte(startTime))
 .must(QueryBuilders.rangeQuery("endTime").lte(endTime)); 
```

有了这个，我们就有了解决方案。我们能够轻松地生成新的 Elasticsearch 查询或更新现有查询，而无需任何代码更改，同时重用我们经过充分测试的抽象来指定众所周知的搜索参数。另一个令人敬畏的好处是能够直接使用我们的 JSON 文件作为对 Elasticsearch 的 REST API 的查询，以便于测试和验证。

你认为这个解决方案怎么样？有没有其他我们应该使用的 Elasticsearch API 特性？我们仍在学习并乐于听到新的特性和使用案例。

* * *
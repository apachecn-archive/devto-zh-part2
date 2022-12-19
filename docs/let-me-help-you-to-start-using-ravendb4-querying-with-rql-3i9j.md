# 让我来帮助你开始使用 RavenDB4:用 RQL 查询

> 原文：<https://dev.to/elemarjr/let-me-help-you-to-start-using-ravendb4-querying-with-rql-3i9j>

在[之前的文章](https://dev.to/elemarjr/let-me-help-you-to-start-using-ravendb4-24c4-temp-slug-7887628)中，您学习了如何在您的计算机上安装 RavenDB、创建数据库和加载样本数据。此外，您还学习了一些关于文档数据库的基本概念。

现在，您将学习如何使用 RQL 编写查询。

## RQL？这是什么？

RQL 是 RavenDB 4 最令人兴奋的特性之一。它是一种功能强大、易于使用和学习的语言，我们旨在让您的生活更加简单。

从文档中:

> Raven 查询语言 RQL 是一种类似 SQL 的语言，用于在执行查询时从服务器检索数据。它旨在以一种易于理解、易于使用且不会给用户造成负担的方式公开 RavenDB 查询管道。

这个 [RQL 文档](https://ravendb.net/docs/article-page/4.0/csharp/indexes/querying/what-is-rql)确实不错。你应该考虑读一读。

## 准备编写查询

是时候停止讨论并写一些代码了。所以，让我们开始吧。

1.  打开`RavenDB Management Studio`([http://localhost:8080](http://localhost:8080)默认。记得吗？)
2.  在左侧面板中，点击`Databases`
3.  打开我们在上一篇文章中创建的数据库(Northwind，如果你遵循了我的建议)
4.  在左侧面板中，选择`Documents`部分。
5.  点击`Query`。

还有其他的途径。我会让你发现它。

## 你先查询

让我们从简单的开始。

1.  假设您已经在查询编辑器中(在 RavenDB Management Studio 中)。键入以下查询。

```
from Employees 
```

Enter fullscreen mode Exit fullscreen mode

1.  点击`Run`按钮。

是的。你是对的。这个查询返回了`Employees`集合中的所有文档。我想你明白了。

现在，继续尝试像这样的其他查询。从`Products`集合中获取所有文档。

## 过滤

从一个集合中获取所有文档是一件好事，但却毫无用处。让我们来点更刺激的。

```
from Employees
where FirstName=="Nancy" 
```

Enter fullscreen mode Exit fullscreen mode

是啊！我想你明白了。FirstName 是 Employees 集合的文档中出现的

属性之一的名称。

```
{
    "LastName": "Davolio",
    "FirstName": "Nancy",
    "Title": "Sales Representative",
    "Address": {
        "Line1": "507 - 20th Ave. E.\r\nApt. 2A",
        "Line2": null,
        "City": "Seattle",
        "Region": "WA",
        "PostalCode": "98122",
        "Country": "USA",
        "Location": {
            "Latitude": 47.623473,
            "Longitude": -122.306009
        }
    },
    "HiredAt": "1992-05-01T00:00:00.0000000",
    "Birthday": "1948-12-08T00:00:00.0000000",
    "HomePhone": "(206) 555-9857",
    "Extension": "5467",
    "ReportsTo": "employees/2-A",
    "Notes": [
        "Education includes a BA in psychology from Colorado State University in 1970\. She also completed \"The Art of the Cold Call.\" Nancy is a member of Toastmasters International."
    ],
    "Territories": [
        "06897",
        "19713"
    ],
    "@metadata": {
        "@collection": "Employees",
        "@flags": "HasAttachments"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 塑造查询结果

直到现在，我们只是得到文件。假设我们想要塑造我们得到的东西。考虑下面的查询。

```
from Orders
where Lines.Count > 4
select Lines[].ProductName as ProductNames, OrderedAt, ShipTo.City 
```

Enter fullscreen mode Exit fullscreen mode

再说一遍，我相信你明白发生了什么。我们对订单文件中的所有数据都不感兴趣。所以，我们指定一个形状。

其中一个结果会是这样的:

```
{
    "ProductNames": [
        "Ikura",
        "Gorgonzola Telino",
        "Geitost",
        "Boston Crab Meat",
        "Lakkalikööri"
    ],
    "OrderedAt": "1996-08-05T00:00:00.0000000",
    "ShipTo.City": "Cunewalde",
    "@metadata": {
        "@flags": "HasRevisions",
        "@id": "orders/26-A",
        "@last-modified": "2018-02-28T11:21:24.1689975Z",
        "@change-vector": "A:275-ZzT6GeIVUkewYXBKQ6vJ9g",
        "@projection": true,
        "@index-score": 1
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们以后再谈`metadata`。

## 在查询投影中使用 Javascript

最后一个查询很好。但是，假设你想做更多的定制。

```
from Orders as o
load o.Company as c
select {
    Name: c.Name.toLowerCase(),
    Country: c.Address.Country,
    LinesCount: o.Lines.length
} 
```

Enter fullscreen mode Exit fullscreen mode

RavenDB 允许你使用 Javascript(大家都知道 Javascript 的基础知识吧？)定义查询结果的投影时。

您可能已经注意到，这个查询中还有一件有趣的事情。订单文档的公司字段包含数据库中存储的另一个文档的 ID。load 指令足够智能，可以为您获取文档，因此您也可以使用它来投影数据。

## 贴图并缩小(哦耶！)

考虑下面的查询:

```
from Orders
group by Company
where count() > 5
order by count() desc
select count() as Count, key() as Company 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做什么？我们使用公司字段作为分组关键字对订单进行分组。因此，我们添加了一个过滤器，只获取至少包含五个文档的组，然后按照元素数量降序排列这些组。最后，我们预测每个组的文档数量和组密钥。

在“business words”中，该查询会产生一个顶级买家公司列表。

## 工作原理

有一段时间，你不应该关心我们的实现细节。但是，很重要的一点是，我们关注性能，我们使用一系列技术来尽快交付结果(甚至更多！).

RavenDB 中的所有查询都由一个复杂高效的索引机制支持。简而言之，我们对所有的查询都使用索引。但是，我会在以后解释更多的细节。

## 遗言…

厉害！现在您已经了解了使用 RavenDB 进行查询的基本知识。很简单吧？

帖子[让我帮助你开始使用 RavenDB4:与 RQL 一起查询](http://www.elemarjr.com/en/2018/03/it-is-time-to-you-get-familiar-with-rql/)最早出现在 [Elemar JR](http://www.elemarjr.com/en) 上。
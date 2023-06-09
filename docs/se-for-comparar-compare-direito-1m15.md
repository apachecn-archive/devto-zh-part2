# 如果是比较，就直截了当地比较吧！

> 原文：<https://dev.to/delbussoweb/se-for-comparar-compare-direito-1m15>

大家好，今天我直接从路上写信(打算在蒙戈布多训练一支球队)来谈谈蒙戈布和阿朗戈布最近的比较。事实上，这并不是两个银行之间的具体比较，而是几个银行之间的个案研究，但我确实找到了 mongodb 被引用的具体点。在继续之前，我先把 piotr srczkowski 写的原始文章的链接留在这里，以非常详细的信号表示。

想法是阐述我对比较项目的看法，展开讨论，欢迎大家发表意见！

## 无连接

好吧，这一点在接触时总是引起很大的争议。重要的是，当我们使用 NoSQL(尤其是文档模板)时，我们希望通过将尽可能多的信息聚合到一个文档中来构建我们的实体模型，以便我们能够更好地实现我们的愿望。因此，我们必须在建模时关注“连接”，而不是关系银行，在那里，它们将由好的旧关系代数来保证。

但是，说蒙戈布没有加入是完全错误的。在 3.2 版中，$lookup 运算符已引入聚合框架中，从而允许在集合之间“关联”数据。正确组合使用此运算符与常规连接的效果基本相同。很明显，由于 mongodb 不使用约束条件，此运算符不能保证另一边的集合会找到任何结果。在这种情况下，通过与关系银行并行跟踪，$lookup 类似于 LEFT JOIN。

此比较中与此问题相关的另一点是聚合框架的复杂性和局限性。此点与$lookup 直接相关，因为此运算符仅通过 mongodb 聚合函数起作用。聚合框架是用来取代 mapReduce 的(而且很好)，这是肯定的，我认为它很复杂，而且很有限，难以调试。我同意，聚合框架并不是那么容易理解，尤其是在开始时，但我将这一点与对操作人员不熟悉以及我们非常熟悉 SQL 这一事实联系起来，SQL 已经伴随我们至少 40 年了。

在我看来，当我们尝试学习新技术时，我们必须牢记我们会有一个学习曲线，而我们是否应该减缓它。对于聚合框架，mongodb 在此处提供了一个 SQL 愿望映射“[”](http://docs.mongodb.org/manual/reference/sql-aggregation-comparison/)。它没有全部，但我认为它是一个指南针，我们可以从聚合框架开始。

以下是一个简单的例子，其中聚合发生在集合 *orders* 中，并与集合 *inventory* 中的文档相关:

```
db.orders.aggregate([
   {
     $lookup:
       {
         from: "inventory",
         localField: "item",
         foreignField: "sku",
         as: "inventory_docs"
       }
  }
]) 
```

Enter fullscreen mode Exit fullscreen mode

这个查询的结果会是这样的:

```
{
   "_id" : 1,
   "item" : "almonds",
   "price" : 12,
   "quantity" : 2,
   "inventory_docs" : [
      { "_id" : 1, "sku" : "almonds", "description" : "product 1", "instock" : 120 }
   ]
}
{
   "_id" : 2,
   "item" : "pecans",
   "price" : 20,
   "quantity" : 1,
   "inventory_docs" : [
      { "_id" : 4, "sku" : "pecans", "description" : "product 4", "instock" : 70 }
   ]
}
{
   "_id" : 3,
   "inventory_docs" : [
      { "_id" : 5, "sku" : null, "description" : "Incomplete" },
      { "_id" : 6 }
   ]
} 
```

Enter fullscreen mode Exit fullscreen mode

没有 SQL seria algo do tipo:

```
SELECT *, inventory_docs
FROM orders
WHERE inventory_docs IN (SELECT *
FROM inventory
WHERE sku= orders.item); 
```

Enter fullscreen mode Exit fullscreen mode

[您可以在此找到完整的$lookup](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/index.html) 文件

所以蒙戈布没有加入，所以我选择另一家面向文件的银行:神话！

## 缺乏专用表达语言

还有一点我不同意。按照蒙戈布创始人的理念，他们认为编码应该是自然的事情，为什么还要创造一种功能齐全的语言来学习，而我可以把 JavaScript 带到银行那边呢？mongodb 通过使用接收 JSON 的函数作为参数，使用 JavaScript 命名来实现这一点。

JSON，在我看来，也是来解决各种问题的。谁从来不需要使用 web 服务和那些巨大的 XML 文件，这些文件都是以前从未使用过的数据。这种模式是为了简化很多事情，我只列举一个，我引用网络流量！使用 JSON 来传输信息的数据包要小得多。

蒙戈布原油的几个例子:

### 插入

```
db.inventory.insertOne(
   { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" } }
) 
```

Enter fullscreen mode Exit fullscreen mode

### 找到

```
db.inventory.find( { status: "D" } ) 
```

Enter fullscreen mode Exit fullscreen mode

SQL 中的等效项:

```
SELECT * FROM inventory WHERE status = "D" 
```

Enter fullscreen mode Exit fullscreen mode

### 更新

```
db.inventory.updateOne(
   { item: "paper" },
   {
     $set: { "size.uom": "cm", status: "P" },
     $currentDate: { lastModified: true }
   }
) 
```

Enter fullscreen mode Exit fullscreen mode

### 删除

```
db.inventory.deleteMany({ status : "A" }) 
```

Enter fullscreen mode Exit fullscreen mode

请参阅此处的“T0”完整文档。

所以，让我们充分利用 JavaScript 和 JSON 可以提供的所有好处，不要为学习新语言而哭泣！

## 无交易

好吧，我不会因为 MongoDB 只支持一份文件中的交易而沾沾自喜，因为正如我的朋友 jhonathan soares 所说:这是不符合逻辑的。但请记住，mongodb 自 WiredTiger 作为存储引擎推出以来就支持这一点，但这是一个不同的概念，它并不像我们习惯的那样反映交易的概念。

现在，在 4.0 版中，mongodb 将支持多文档事务，而且最酷的是，它将支持 replicaset 中的事务，也就是说，我们将在复制环境中拥有事务。

我同意在 arangodb 的案子里这已经存在了一段时间但对我们来说这是一个非常激动人心的新生意。但是，MongoDB 自己的研究表明，采用 MongoDB 作为数据库可以满足 80%到 90%的应用程序使用案例。

让我们等待交易的到来！

## 只在支尔格出现问题的票

好吧，Github 比 JIRA 友好得多，但把它作为不收养 MongoDB 的点是太多了！我会说那是咪咪 kkk！

我从来没有遇到过与 MongoDB 代码本身有关的问题，但我已经需要在 JIRA 开一张票来谈论阿特拉斯系统中的一个环境。在这种情况下，效果很好，直到他用另一个工程师代替了原来的工程师。

再说一遍，在我看来，我不会把它作为一个点来不采用蒙戈布！

## 结论

正如我所说，我想把辩论的开始留在这里。并不是每个人都会有相同的看法，但我认为讨论很有意义，也考虑到了这里提出的所有观点。

拥抱所有人！
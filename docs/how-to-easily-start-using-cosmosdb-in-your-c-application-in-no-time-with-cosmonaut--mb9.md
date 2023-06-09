# 如何使用 Cosmonaut 在 C#应用程序中轻松使用 CosmosDB

> 原文：<https://dev.to/elfocrash/how-to-easily-start-using-cosmosdb-in-your-c-application-in-no-time-with-cosmonaut--mb9>

[![](img/1ac550b94a70f924581d866a96ec865b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ff1FA-pQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4ZZdJuT.png)

我用过很多基于 NoSQL 数据库的文档。CosmosDB 是我最喜欢的，主要是因为它远不止这些。像它的可扩展性，不同的 API 选项，变化反馈，甚至微软提供的模拟器都是令人惊叹的。

然而，除非您愿意花大量时间阅读相关内容，否则集成体验对某些人来说可能是一场噩梦。比如价格、性能、分区键和索引等等，可能太多了。最重要的是，你必须使用微软提供的 SDK，它们一点也不差，但是除非你知道何时使用哪种方法，否则你最终会有糟糕的性能或者付出比你应该付出的更多的代价。

这就是[宇航员](https://github.com/Elfocrash/Cosmonaut)发挥作用的地方。
它是围绕 CosmosDB 的 SQL API 的包装器库，允许基于对象的灵活 CRUD(以及更多)。有些人可能会说 SQL API SDK 已经做到了这一点，但请继续准备，你会明白我的意思。

##### 安装

宇航员发表于 [Nuget](https://www.nuget.org/packages/Cosmonaut) 。
你可以从 Nuget 浏览器或者命令行安装

```
Install-Package Cosmonaut
or
dotnet add package Cosmonaut 
```

Enter fullscreen mode Exit fullscreen mode

一旦添加了包，集成就可以像在 DI 服务集合中添加下面一行代码一样简单。
`serviceCollection.AddCosmosStore<YourObject>(cosmosSettings);`

一旦你这样做了，你就可以从 DI 那里得到`ICosmosStore<YourObject>`，你就可以开始了。
或者，您可以手动创建一个 CosmosStore 对象。

CosmosStoreSettings 中只有 3 个强制设置。

*   数据库名
*   AuthKey
*   端点 Url

还有更多东西可以像`ConnectionPolicy`或`IndexingPolicy`那样配置，但如果没有设置，它们将默认为 CosmosDB 默认值。

##### 如何使用

默认情况下，宇航员将为每个对象创建/需要一个集合。然而，它也有在不同对象之间共享集合的逻辑。这个我们以后再说。

现在你需要知道的是有一个主要的限制。

您的对象需要勾选以下复选框之一

*   有一个名为`Id`的`string`类型的属性
*   实现`ICosmosEntity`接口
*   扩展`CosmosEntity`类
*   有一个属性为`[JsonProperty("id")]`的`string`类型的属性

这是为了确保您的对象可以在 CosmosDB 中存储、检索和更新。

如果你计划做任何`Select(x => x.Id)`查询，那么你必须有`[JsonProperty("id")]`属性或者扩展`CosmosEntity`类。

Cosmonaut 创建的集合的名称(当集合丢失时)是通过以下方式生成的。如果对象有`CosmosCollection`属性，那么您可以在那里指定集合的名称。如果不是，那么将使用对象名称的复数形式。如果您想将 Cosmonaut 添加到现有的 CosmosDB 集合中，该属性也很棒。

**cosmos store 有以下对象操作方法:**

*   `AddAsync(TEntity entity)`在 CosmosDB 集合中添加一个对象。
*   `UpdateAsync(TEntity entity)`更新 CosmosDB 集合中的现有对象。
*   `UpsertAsync(TEntity entity)`更新 CosmosDB 集合中的现有对象，如果它不在集合中，则添加它。
*   `RemoveAsync(TEntity entity)`从 CosmosDB 集合中删除了一个对象。

所有这些都有一个`Range`方法，它允许对一组条目进行操作。RemoveAsync 还支持基于过滤器的表达式删除。

操作响应还包含文档本身的 ResourceResponse，以便允许检索低级信息。

**说到查询...**

...您可以简单地调用`.Query()`方法并准备好一个 IQueryable。请记住，在查询级别，CosmosDB 只支持`Where`、`Select`和`SelectMany`。
当你想把你刚刚建立的查询返回到一个列表中，或者只是得到第一个对象，你有两个选择。

您可以使用 LINQ 方法`ToList()`，但是这是一个不推荐的同步调用。你应该做的是使用 Cosmonaut 自带的一个扩展方法，比如:

*   托利斯塔 sync
*   计数异步
*   FirstOrDefaultAsync
*   FirstAsync
*   SingleOrDefaultAsync
*   单一异步
*   MaxAsync
*   迷你同步

这些方法将使用内置的 int 分页逻辑来确保当 Cosmonaut 为您检索文档时，您的应用程序不会被锁定。

正如你可以告诉你的，这给了你几乎一切你需要开始。

##### 分区键

在 CosmosDB 中，分区键是您需要了解的最重要的事情之一。这个博客不会解释它到底是什么以及它是如何工作的，但是它会让你知道宇航员是如何使用它的。更多关于分区键[的信息请点击这里](https://docs.microsoft.com/en-us/azure/cosmos-db/partition-data)。

关于分区键，您需要了解一些事情。

*   一旦创建了没有分区键的集合，就不能添加分区键。
*   一旦使用分区键创建了集合，就不能更改它。

在非共享集合中，默认情况下 Cosmonaut 不会添加分区键。然而，通过使用`[CosmosPartitionKey]`属性，您可以指定哪个属性是您的分区键属性。如果集合尚未创建，这将用于创建带有键的集合。

##### 索引

在查询文档属性时，索引起着很大的作用。
默认情况下，cosmosdb 集合是根据以下集合规则创建的。

```
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                },
                {
                    "kind": "Hash",
                    "dataType": "String",
                    "precision": 3
                }
            ]
        }
    ],
    "excludedPaths": []
} 
```

Enter fullscreen mode Exit fullscreen mode

这也不是一个解释索引的博客，所以我不会深入，但你需要知道的是，如果字符串的类型是 Hash，你就不能部分查询字符串。你只能精确匹配它们。宇航员允许你在设置级别覆盖它。将 Hash 改为 Range 将允许像`StartsWith`这样的东西匹配您想要的数据。

示例:如果字符串数据类型是 Hash，那么如下所示的精确匹配，`cosmoStore.Query().FirstOrDefaultAsync(x => x.SomeProperty.Equals($"Nick Chapsas")`将返回 CosmosDB 中存在的项目，但是`cosmoStore.Query().FirstOrDefaultAsync(x => x.SomeProperty.StartsWith($"Nick Ch")`将抛出一个错误。将 Hash 改为 Range 将使后者工作。

然而，您也可以在查询级别覆盖它，只需将`FeedOptions`中的`EnableScanInQuery`改为 true。

更多关于索引[的信息，请点击](https://docs.microsoft.com/en-us/azure/cosmos-db/indexing-policies)。

##### 存钱

我明白了。RU/s 很吓人，但是别担心，Cosmonaut 的设计就是为了消除这种恐惧。

你看，CosmosDB 的收费方式是每次收集按小时收费。但是，如果您在一个小时内更改了您的 RU/s，哪怕是一秒钟，您都将被收取一个小时的费用，无论该小时的最高 RU/s 是多少。

这可能会失去控制，并不是每个集合都需要与其他集合分开。请记住，这是一个无模式的数据库，为什么不共享集合呢？

嗯，宇航员已经内置了对收藏共享的支持。为了可靠地共享收藏而不打乱您的操作，您只需要做两件事。

*   用`SharedCosmosCollection`属性装饰你的对象。
*   实现`ISharedCosmosEntity`接口。

您还需要指定这个对象将要使用的共享集合的名称，就像`[SharedCosmosCollection("shared")]`一样。

唯一的妥协是您的索引不能非常具体，因为您正在共享集合。
我还启用了一个功能，如果您正在共享集合，那么`id`属性将自动成为您的分区键。有两个原因支持这一选择。

*   在集合创建之后，您不能添加分区键，并且没有至少随机的分区分布是很遗憾的。
*   不保证您的文档之间有任何其他公共属性。

##### 代码

宇航员在麻省理工学院的许可下在 Github 上开源。

请考虑尝试一下，并报告任何问题。
也是 GitHub 回购上的一颗星让我内心感到温暖。

反馈也非常受欢迎。
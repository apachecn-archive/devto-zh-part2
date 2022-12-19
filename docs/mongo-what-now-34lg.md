# 蒙戈什么现在？

> 原文：<https://dev.to/turnersoftware/mongo-what-now-34lg>

你有没有让你的主要副业项目衍生出自己的副业项目？我不知道这种情况在其他人身上发生的频率有多高，但这似乎是我的项目中反复出现的主题。

在我解释这个 *side* side 项目是什么之前，让我稍微解释一下我是如何来到这里的。

# 原方项目

在过去的几年里，我一直致力于建立自己的分析工具，以帮助管理和分析品牌。当我最终发布它的时候，它本身应该有自己的帖子，但是现在，重要的部分是了解一些关于它的开发进展。

我最初的想法是围绕一个鲜为人知的叫做 [SilverStripe](https://www.silverstripe.org/) 的框架，做一个 PHP/MySQL 网站。应该很容易建立，因为它是我用来为客户建立网站的主要技术之一。然而，我越深入开发，就越觉得这不是适合这项工作的技术。

那么 PHP/MySQL 变成 C#/MySQL 是怎么回事呢——不完全是最自然的组合。之后没多久，出于和之前类似的原因，我又改学了 C#/SQL。

因为我必须享受重写代码的乐趣，所以我又改变了——这一次，现在是 C#/MongoDB。

这些改变不仅仅是为了好玩——是一路上一点点小事的积累让我想要做这些改变。边项目现在使用 C#和 MongoDB，这就把我们带到了今天。

# C# + MongoDB = MongoDB C#驱动？

我在 C#和数据库方面的经验主要是围绕着良好的 ol' [Linq2SQL](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/linq/) 接口，它在很大程度上完成了我需要的功能，没有任何问题。也就是说，当我使用 C#/SQL 进行最初的附带项目时，我开始喜欢上了[实体框架](https://docs.microsoft.com/en-us/ef/ef6/)——尤其是“代码优先”的体验以及你如何与集合交互。

EF“代码优先”方法的一个例子:

```
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class MyContext : DbContext 
{
    public MyContext() : base() { }
    public DbSet<Person> People { get; set; }
}

...

using (var ctx = new MyContext())
{
    var entity = new Person() { Name = "James" };
    ctx.People.Add(entity);
    ctx.SaveChanges();           
} 
```

Enter fullscreen mode Exit fullscreen mode

MongoDB 有一个全功能的 C#驱动程序，可以对数据库做任何你想做的事情。对我来说，它的缺点是它的 API 接口。与上面的相比，与驱动程序的接口看起来更像这样:

```
public class Person
{
    public ObjectId Id { get; set; }
    public string Name { get; set; }
}

...

var client = new MongoClient("mongodb://localhost:27017");
var database = client.GetDatabase("foo");
var collection = database.GetCollection<Person>("bar");

collection.InsertOne(new Person { Name = "James" }); 
```

Enter fullscreen mode Exit fullscreen mode

这并不是一个本质上糟糕的 API 或任何东西，只是我不喜欢这样处理数据库。我真正想要的是带有 MongoDB 后端的实体框架接口。

如果你是一个实体框架的核心爱好者，似乎确实有人在为它开发合适的 MongoDB 提供者，但是当我开始做这个的时候，还没有。除此之外，我的*最初的*边项目还在进行中。NET 框架，不是核心。

那我做了什么？我为我的 MongoDB“实体框架”类库想出了一个原始名称，并开始工作。

# 蒙哥框架

*导数是新的原始值*

这可能不是我发布的第一段代码，但可能是目前对我来说最重要的一段。

我想要构建的是一个官方 MongoDB C#驱动程序的基本包装器，让我感觉像是在使用实体框架。一旦我做好了基础工作，那就是我想要变聪明的时候。

MongoDB C#驱动程序很好，但是有些东西开箱即用很麻烦。例如，执行文档(又名。实体)更新要求您要么使用这个构建器/过滤器系统并指定您更新的键和值，要么替换整个文档。我不喜欢这两个选项，我决定在 MongoFramework 中构建变更跟踪并支持差异更新，这样开发人员就不用担心了。

另一个例子是官方驱动程序中没有“变更集”支持——当你调用`InsertOne`时，它已经把它发送到数据库。在 MongoFramework 中对变更集支持的几次迭代之后，它现在可以对特定的`MongoDbSet`中的所有变更进行单个`BulkWrite`调用。

# 仍在进行中

像许多其他项目一样，MongoFramework 仍然是一项正在进行的工作。我还想添加更多的功能，比如 GridFS、异步读取和数据库事务支持。

即使现在 EF Core 中提供了 MongoDB 提供程序，我认为 MongoFramework 仍然有它的位置——如果不在你的下一个项目中，它在我心中也有一席之地。❤️

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png)[Turner software](https://github.com/TurnerSoftware)/[MongoFramework](https://github.com/TurnerSoftware/MongoFramework)

### MongoDB 的类似“实体框架”的接口

<article class="markdown-body entry-content container-lg" itemprop="text">

# MongoFramework

MongoDB 的类似“实体框架”的接口

[![AppVeyor](../Images/683472c9150e7926c0eca290f339e995.png)](https://ci.appveyor.com/project/Turnerj/mongoframework)[![Codecov](../Images/7260eb4f7ea36d39dc0e9b0bb2547c2c.png)](https://codecov.io/gh/TurnerSoftware/MongoFramework)[![NuGet](../Images/5e15c035c5c71c698e71e06ab03250e4.png)](https://www.nuget.org/packages/MongoFramework/)[![Codacy Badge](../Images/4f2f4212de102acd5247f678912de96d.png)T11】](https://www.codacy.com/app/Turnerj/MongoFramework)

## 概观

MongoFramework 试图将实体框架的一些优秀特性引入 MongoDB 的世界。

一些主要功能包括:

*   通过属性为集合、id 和属性进行实体映射
*   通过属性(包括文本和地理空间)进行索引
*   实体变更跟踪
*   变更集支持(允许同时运行多个数据库更新)
*   差异更新(只有*将*更改为要写入的实体)
*   实体桶(将小文档聚集在一起，[提高索引性能](https://www.mongodb.com/blog/post/building-with-patterns-the-bucket-pattern)
*   运行时类型发现(序列化和反序列化，无需指定每个“已知”类型)

MongoFramework 目前构建在官方 MongoDB C#驱动之上。

## 扩展ˌ扩张

这些扩展是增强 MongoFramework 功能的官方包，将它与其他系统和工具集成在一起。

### MongoFramework。剖析。微型剖析器

[![NuGet](../Images/f97bf3bb25d014ce968141af58bf3920.png)T2】](https://www.nuget.org/packages/MongoFramework.Profiling.MiniProfiler/)

支持分析数据库读写，将数据推送到[微型分析器](https://github.com/MiniProfiler/dotnet/)。

## 证明文件

### 核心实体映射

实体及其属性的核心映射…

</article>

[View on GitHub](https://github.com/TurnerSoftware/MongoFramework)
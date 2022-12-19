# myPrayerJournal 之旅:数据仓库

> 原文：<https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-data-store-5efa>

*备注:*

*   这是系列文章中的第 6 篇；请参见[介绍](/danieljsummers/a-tour-of-myprayerjournal-introduction-jl5)中的所有内容，以及构建该软件的要求。
*   *以文本“mpj:”开头的链接是指向 myPrayerJournal 的 1.0.0 标签(1.0 版本)的链接，除非另有说明。*

到目前为止，我们已经讨论了很多数据，但都是在我们讨论的其他内容的背景下进行的。让我们深入研究一下数据结构，看看我们的信息是如何保存和检索的。

## 概念设计

最初的想法是创建一个只有一种文档类型的文档存储，即请求。该请求将有一个 ID、创建它的用户的 ID 以及一组更新/记录。在开发的最初阶段，我们首选的文档数据库( [RethinkDB](https://rethinkdb.com) )经历了一段艰难的时期，他们的公司倒闭了；谢天谢地，它们现在是 Linux 基金会的一部分，所以它们仍然存在。RethinkDB 支持文档中的计算字段，所以我们的计划是使用一些计算字段，这样我们就不必在一系列更新中检索或搜索了。

我们也考虑过使用 [PostgreSQL](https://www.postgresql.org) 的本地 <abbr title="JavaScript Object Notation">JSON</abbr> 支持的类似设计。虽然它本身不支持计算字段，但一组创造性的索引也足够了。然而，当我们想得更多一点时，这似乎是过度工程化了；这不是非结构化数据，PostgreSQL 很好地处理了最大长度字符字段。(这应该是一个“极简”的应用吧？)关系结构非常适合我们的需求。

最初的设计使用了两张桌子。`request`有一个 ID 和一个用户 ID；`history`有请求 ID、“截止”日期、状态(创建、更新等)。)，以及与该更新相关联的可选文本。在开发的早期,`journal`视图将请求/用户 id 与影响请求文本的最新历史条目以及请求上一次发生动作的日期/时间放在一起。当添加 notes 功能时，它有了自己的`note`表；它的结构类似于`history`表，但是没有可选的文本和状态。随着休眠和循环功能的增加，这些字段被添加到`request`表(和`journal`视图)中。

最终设计使用 3 个表，其中 2 个与第三个有一对多关系；和 1 个视图，它提供了我们最初计划让 RethinkDB 计算的计算字段。

## 数据库变更(迁移)

在这个项目的过程中，我们最终使用了 3 种不同的服务器环境，我们最终基于现有的结构编写了一个`DbContext`类。对于 Node.js 后端，我们创建了一个 <abbr title="Data Definition Language">DDL</abbr> 文件( [mpj:ddl.js](https://github.com/bit-badger/myPrayerJournal/blob/3c3f0a7981fa8f82d3cc904630960ca43c910cd2/src/api/src/db/ddl.js) ，v0.8.4+)，它检查每个表和视图是否存在，如果检查失败，还有 SQL 要执行。对于 Go 版本( [mpj:data.go](https://github.com/bit-badger/myPrayerJournal/blob/d0ea7cf3c631512ea6b3afba61a25c83aaded6c8/src/api/data/data.go#L307) ，v 0 . 9 . 6+),`EnsureDB`函数做类似的事情；查看第 347 行，它检查了`request`表中的特定列，如果不存在，运行`ALTER TABLE`语句添加它。

自从 F#/Giraffe 后端出现以来，唯一需要的改变是支持请求重现。由于我们最终没有一个搭建好的 EF 核心初始迁移/模型，我们简单地编写了一个 SQL 脚本来完成这些更改( [mpj:sql directory](https://github.com/bit-badger/myPrayerJournal/tree/1.0.0/src/sql) )。 [<sup>1</sup>](#note-1)

## 英孚核心模型

EF 核心使用以前版本的实体框架中熟悉的`DbContext`类。不过，myPrayerJournal 确实利用了 EF Core 2.1 中刚刚推出的一个特性——即`DbQuery`类型。`DbSet`是通常映射到底层数据库表的实体的集合。它们可以映射到视图，但是除非是可更新的视图，否则更新这些实体会导致运行时错误；另外，因为它们不能被更新，所以变更跟踪机制不需要关心返回的实体。`DbQuery`解决了这两个问题，提供了对视图数据的轻量级只读访问。

在 Data.fs ( [mpj:Data.fs](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Data.fs) )中定义了`DbContext`类，从第 189 行开始。这相对简单，尽管如果你只见过 C#模型，这有点不同。`val mutable x : [type]`和`[<DefaultValue>]`属性的组合是 C#的`[type] x;`声明的 F#等价物，它创建一个变量并将引用类型初始化为`null`。EF 核心运行时将这些实例提供给它们的 setters(第 203、206、209 和 212 行)，应用程序代码通过 getters(前面一行)使用它们。

当运行时第一次创建数据模型的实例时，调用被覆盖的方法(第 214 行)。在这个方法中，我们调用每个数据库类型的`.configureEF`函数。这个函数的名字没有规定，我们可以定义整个模型，甚至不用引用我们实体的数据类型；然而，这种技术为我们提供了一种针对每种实体类型的“在定义的地方配置”范例。虽然 EF“代码优先”模型创建了不需要大量配置的表，但是我们必须提供更多关于数据库表布局的信息，因为我们正在编写一个针对现有数据库的`DbContext`。

让我们从看一看`History.configureEF`(第 50 行)开始。第 53 行说我们要去牌桌`history`。这似乎是显而易见的，但是 EF 核心(按照惯例)会期待一个`History`表；由于 PostgreSQL 对区分大小写的名称使用不同的语法，这些查询看起来像`SELECT ... FROM "History" ...`，导致一个漂亮的“关系不存在”错误。第 54 行定义了我们的复合键(`requestId`和`asOf`)。第 55-57 行根据需要定义了实体的某些属性；如果我们试图在没有设置这些字段的地方存储一个实体，运行时甚至会在试图将它带到数据库之前引发一个异常。 *(F#的不可空性使得这不是一个问题，但是它仍然需要被定义以匹配数据库。)*第 58 行可能看起来什么都不做，但是它做的是让`text`属性立即对模型构建器可见；然后，我们可以为它定义一个`OptionConverter<string>` [<sup>2</sup>](#note-2) ，在`null`和`string option`之间进行翻译(`None` = `null`，`Some [x]` = `[x]`)。*(第 60-61 行是我试图弄清楚为什么第 62 行会引发异常时留下的，这导致了第 58 行的添加；它们可以被安全地删除，并将用于 1.0 以后的版本。)*

`History`是最复杂的配置，但是让我们看一下`Request.configureEF`(第 126 行)来看看一个更有趣的技术。第 107-110 行定义了`Request`类型上的`history`和`notes`集合；第 138-145 行定义了一对多关系(子类型中没有外键实体)。注意对`IEnumerable<x>`(第 138 和 142 行)和`obj`(第 140 和 144 行)的强制转换；虽然 F#在很多情况下擅长推断类型，但这些函数有两个地方不行。我们可以使用`:>`操作符进行强制转换，因为这些类型是继承链的一部分。*(`:?>`操作符用于潜在的不安全类型转换。)*

最后，每个记录类型上面的属性需要解释一下；每一个都有`[<CLIMutable; NoComparison; NoEquality>]`。属性为记录类型创建一个无参数构造函数，运行时可以用它来创建该类型的实例。(副作用是，我们可能会得到预期为非空类型的空实例，但我们稍后将研究如何处理这个问题。)属性`NoComparison`和`NoEquality`阻止 F#在类型上创建字段级相等和比较方法。虽然这些通常是有帮助的，但也有例外情况，它们会引发`NullReferenceException`，尤其是在空实例上使用时。因为这些记录类型只是我们的数据传输对象(从 SQL 到 JSON)，所以我们不需要这个功能。

## 读写数据

EF Core 使用“工作单元”模式和它的`DbContext`类。每个实例维护它所加载的实体的知识，并根据这些实体进行变更跟踪，因此它知道在调用`.SaveChanges()`(或`.SaveChangesAsync()`)时发出什么命令。然而，这并不是免费的，尽管 EF Core 比 Entity Framework 本身更有效地做到了这一点，但 F#记录类型不支持变异；例如，如果`req`是一个`Request`实例，`{ req with showAfter = 123456789L }`将返回一个**新的**实例。

Data.fs 中的第 227-233 行解决了这个问题。我们可以手动将一个实体的实例注册为已添加或已修改，当我们调用`.SaveChanges()`时，运行时将生成 SQL 来相应地更新数据存储。这也允许我们在查询中使用`.AsNoTracking()`(第 250、258、265 和 275 行)，这意味着生成的实体将不会向变更跟踪器注册，从而节省了开销。请注意，我们没有在第 243 行指定它；由于`Journal`被定义为`DbQuery`而不是`DbSet`，我们可以免费获得变更跟踪避免。

一般来说，针对`DbContext`实例编写查询的首选方法是针对它定义扩展方法。默认情况下，这些是`static`，它们使上下文尽可能的轻量级，同时在必要时扩展它。然而，由于这个上下文非常小，我们在上下文中创建了 6 个方法来获取数据。

如果您一直在阅读本教程，我们已经看到了一些使用数据上下文的 API 处理函数( [mpj:Handlers.fs](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Handlers.fs) )。第 137 行有`/api/journal`的处理程序，它是检索用户主动请求的端点。它使用 Data.fs 第 242 行中定义的`.JournalByUserId()`，其签名是`string -> JournalRequest seq`。(后者是`IEnumerable<JournalRequest>`的 F#别名。)回到处理程序中，我们使用`db ctx`来获取上下文(下面会详细介绍)，然后调用方法；我们将`userId ctx`的输出通过管道传递给它，所以它从管道中获得它的唯一参数，然后它的输出通过管道传递给我们讨论过的作为 API 一部分的[函数。](/danieljsummers/a-tour-of-myprayerjournal-the-api-56l5)

第 192 行是`/api/request/[id]/history`的处理程序，演示了插入和更新数据。我们试图通过 ID 和用户 ID 来检索请求；如果失败，我们返回 404。但是，如果它成功了，我们添加一个历史条目(第 201-207 行)，并根据它的重复更新请求的`showAfter`字段。最后，第 212 行上的调用提交了这个特定实例的更改。因为`.SaveChanges[Async]()`方法返回受影响的记录数，所以我们不能使用`do!`操作符；F#让你显式地忽略那些既不返回也不赋值给名字的值。然而，将`_`定义为一个参数或名称表明我们意识到有一个值得拥有的值，我们只是不打算用它做任何事情。

我们提到过`CLIMutable`记录类型可能为空。由于记录类型通常不能为空，我们不能编写类似于`match [var] with null -> ...`的代码；这是一个编译器语法错误。但是，我们可以使用`box`操作符。`box`“盒子”将我们拥有的任何值放入一个对象容器中，然后我们可以根据`null`对其进行检查。第 11 行的 Data.fs 中的函数`toOption`为我们完成了这项工作；在整个检索方法中，我们使用它来返回存在或不存在的项目的`option`。这就是为什么我们可以针对`Some`和`None`值在`/api/request/[id]/history`处理程序中执行`match`语句。

## 虎子一个`DbContext`

由于长颈鹿位于 ASP.NET 核心的顶部，我们使用相同的技术；我们在`IServiceCollection`接口上使用`.AddDbContext()`扩展方法，并在设置依赖注入容器时分配它。在我们的例子中，它位于 program . fs([mpj:program . fs](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Program.fs))第 50 行，在这里我们还指示它使用由连接字符串“mpj”定义的 PostgreSQL 连接。(这来自于`appsettings.json`和`appsettings.[Environment].json`构建的统一配置。)如果我们回头看 Handlers.fs，第 45-47 行，我们会看到我们之前使用的`db ctx`调用的定义。我们使用长颈鹿提供的`GetService<'T>()`扩展方法来返回这个实例。

我们的旅行即将结束，但我们还有几站要走。下一次，我们将看看[我们如何生成文档](/danieljsummers/a-tour-of-myprayerjournal-documentation-4nf3)来告诉人们如何使用这个应用程序。

* * *

 *写这篇文章告诉我，我需要为回购创建一个 SQL 创建脚本，或者创建一个 EF 核心初始迁移/模型，因此数据库必须从头开始重新创建。事情做了之后再写出来，挺好的！*

 *这也是我写的一个包；它是 NuGet 上可用的[，我也给](https://www.nuget.org/packages/FSharp.EFCore.OptionConverter/)[写了一篇关于它做什么的帖子](https://dev.to/danieljsummers/f-options-with-ef-core-417a)。*
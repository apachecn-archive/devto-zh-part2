# 中的无 ORM 数据访问。网络核心

> 原文：<https://dev.to/ruidfigueiredo/orm-less-data-access-in-net-core-23ac>

对象关系映射库(ORM)的使用如今如此普遍，以至于很少看到有人质疑它们的用途。

这是有充分理由的。在过去，你会看到到处都是 SQL 代码。用户输入与 SQL 语句直接连接的例子屡见不鲜，这为 SQL 注入攻击打开了方便之门(我想到了小鲍比表格)。

尽管使用 ORM 带来了很多好处，但也带来了一些不好的东西。首先是性能，性能更差(有时差很多)。但是除了性能之外，还有一系列其他的问题，虽然它们不是缺点，但是它们对使用 ORM 的体验有负面影响。它们都与 ORM 隐藏了大量关于数据如何检索和保存的细节有关。通常，人们由于没有意识到这些细节而搬起石头砸自己的脚。

仅举几个例子:使用[惰性加载，即使这可能不是一个好主意](https://www.blinkingcaret.com/2017/06/07/orms-lazy-loading-and-web-applications/)(例如 web 应用程序)，或者触发数据获取的机制(这里特别考虑实体框架)产生的 N+1 问题有时并不明显。

我并不是主张不应该使用 ORM，一点也不是。然而，我的看法是，现在大多数在美国工作的人。如果不使用实体框架，Net 生态系统将无法在数据库中检索和创建记录。

这很不幸，因为这一点都不难，而且可能比建立实体框架要快。我在小项目中一直遵循这种方法，我确信没有实体框架比有了它我可以更快地建立一些东西([建立 EF 可能是一件痛苦的事情](https://www.blinkingcaret.com/2018/03/28/setting-up-ef-core/))。

在这篇博文中，我想向您展示如何使用中的“原始”数据访问机制(ADO.NET)。Net 核心，也是一个名为 Dapper 的实体框架的替代方案(由 Stack Overflow 使用)。Dapper 有时被描述为 ORM，但正如我们将看到的，它更像是一个“对象映射器”。

[![orm or not orm image showing wheel spinning suggesting speed, in this context it suggests that not using ORMs is much better in terms of performance](img/21888366a48a6565fe7fe28f068eb9d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vhysf4wt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/04/ormless_data_access.jpg)

## 与 ADO.NET 混为一谈。网络核心

要在没有实体框架核心的情况下进行数据访问，您只需要掌握三个概念。这些概念是连接、命令和数据读取器。

一个[连接](https://msdn.microsoft.com/en-us/library/system.data.idbconnection.aspx)对象代表一个到数据库的连接。您必须为想要与之交互的数据库使用特定的连接对象。例如对于 [PostgreSQL](https://www.postgresql.org/) 我们会使用`NpgsqlConnection`，对于 MySql `MysqlConnection`，对于 SQL Server `SqlConnection`。你明白了。所有这些连接类型都实现了接口 [IDbConnection](https://msdn.microsoft.com/en-us/library/system.data.idbconnection.aspx) 。

您需要为您想要使用的数据库安装正确的 Nuget 包，例如对于 Postgres，包的名称很简单:`Npgsql`(记住它的一个简单方法是 N - for。Net 和 pgsql for PostGreSQL)。

要创建一个连接对象，我们需要一个到我们想要与之交互的数据库的连接字符串。例如，对于 Postgres 中用户为“johnDoe”的名为“example”的数据库，我们可以这样创建一个连接:

```
var connection = new NpgsqlConnection("User ID=johnDoe;Password=thePassword;Host=localhost;Database=example;Port=5432"); 
```

Enter fullscreen mode Exit fullscreen mode

寻找如何创建这些连接字符串的信息的一个很好的资源是 https://www.connectionstrings.com/。

创建连接对象后，为了实际连接到数据库，我们需要对它调用`Open`:

```
connection.Open(); 
```

Enter fullscreen mode Exit fullscreen mode

连接对象都是`IDisposable`，你要把它们处理掉。因此，通常在 using 块中创建连接:

```
using (var connection = new NpgsqlConnection("User ID=johnDoe;Password=thePassword;Host=localhost;Database=example;Port=5432"))
{
    connection.Open();
    //use the connection here
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是你需要开始的全部。

## 创建记录

为了创建记录，我们需要使用一个[命令](https://msdn.microsoft.com/en-us/library/system.data.idbcommand.aspx)。命令是在数据库中执行操作所需的所有内容的容器。

创建命令最简单的方法是向连接对象请求一个命令:

```
using(var command = connection.CreateCommand()) 
{
    //use command here
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，通过属性`CommandText`指定想要执行的 SQL，并在属性`Parameters`中指定 SQL 中的参数值。例如，如果您想向一个名为`people`的表中添加一条记录，该表包含列`first_name`、`last_name`、`age`，该记录如下所示:

```
command.CommandText = "insert into people (first_name, last_name, age) values (@firstName, @lastName, @age)";
command.Parameters.AddWithValue("@firstName", "John");
command.Parameters.AddWithValue("@lastName", "Doe");
command.Parameters.AddWithValue("@age", 38); 
```

Enter fullscreen mode Exit fullscreen mode

您应该使用参数，因为这可以防止 SQL 注入攻击。如果您不这样做，而是使用用户输入的数据通过字符串连接来创建您的 SQL，那么您就可以允许用户输入某些内容，而这些内容[将被解释为 SQL](https://www.troyhunt.com/everything-you-wanted-to-know-about-sql/) 。

命令的“执行”方式取决于您对它的预期结果。对于添加记录，如果您不关心任何自动生成的列值(例如新记录的 id)，您可以这样做:

```
int numberOfUpdatedRows = command.ExecuteNonQuery(); 
```

Enter fullscreen mode Exit fullscreen mode

此方法返回更新/创建的行数。虽然它在 insert 语句中不是特别有用，但是在 update 语句中，这个值可能会有用。

或者，如果您想要插入并获取新记录的 id，您可以更改 insert 语句的 SQL，以便返回新的 id。您这样做的方式取决于您正在使用的数据库，例如在 postgres 中，SQL 看起来像这样`insert into people (first_name, last_name, age) values (@firstName, @lastName, @age) returning id`。

在 sql server 中，它看起来像这样`insert into people (first_name, last_name, age) values (@firstName, @lastName, @age); select scope_identity()`。

要运行插入并获得新的 id，可以在命令中使用 [`ExecuteScalar`](https://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlcommand.executescalar(v=vs.110).aspx) 方法。

`ExecuteScalar`方法执行 SQL 并返回第一行第一列的值(作为类型对象),例如在 postgres:

```
command.CommandText = "insert into people (first_name, last_name, age) values (@firstName, @lastName, @age) returning id";
command.Parameters.AddWithValue("@firstName", "Jane");
command.Parameters.AddWithValue("@lastName", "Doe");
command.Parameters.AddWithValue("@age", 37);

var newId = (int)command.ExecuteScalar(); 
```

Enter fullscreen mode Exit fullscreen mode

您现在可能会想，这些 SQL 语句需要大量的输入。最重要的是，所有这些都没有智能感知。谢天谢地，有相关的技术。[您可以看到我从头开始创建一个 insert 语句，而不必手动输入任何列名](https://www.youtube.com/watch?v=R2XGaOz42i8)。

## 写着

要读取数据，您只需编写 SQL 查询并在命令对象中调用`ExecuteReader`方法。这将返回一个`DataReader`的实例，您可以用它来检索查询的实际结果。

例如，如果我们想要检索`people`表中的所有记录:

```
command.CommandText = "select * from people";
DataReader reader = command.ExecuteReader(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，你得到实际值的方法有点笨拙。肯定不如使用实体框架舒服，但是正如我在视频[中展示的如何使用 Sublime 构建查询](https://www.youtube.com/watch?v=R2XGaOz42i8)一样，您也可以使用相同的技术更快地创建这些代码。

假设 first_name 和 last_name 是字符串，age 是一个 int，下面是如何迭代所有结果的方法。

```
command.CommandText = "select * from people";
using(DataReader reader = command.ExecuteReader()) 
{
    while(reader.Read())
    {
        string firstName = reader.GetString(reader.GetOrdinal("first_name"));
        string lastName = reader.GetString(reader.GetOrdinal("last_name"));
        int age = reader.GetInt32(reader.GetOrdinal("age"));

        //do something with firstName, lastName and age

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`GetString`、`GetIn32`、`GetBoolean`等方法需要一个表示列索引的数字。您可以通过调用`reader.GetOrdinal("columnName")`来获取列索引。

## 更新和删除

更新和删除记录包括用正确的 SQL 创建一个命令，并在该命令中调用`ExecuteNonQuery`。

例如，如果我们想将 people 表中姓氏为“Doe”的所有记录更新为“Smith ”,我们可以这样做

```
command.CommandText = "update people set last_name='Smith' where last_name='Doe'";
int numberOfAffectedRows = command.ExecuteNonQuery(); 
```

Enter fullscreen mode Exit fullscreen mode

删除非常类似，例如，让我们删除所有没有姓氏的记录

```
command.CommandText = "delete from people where last_name is null";
int numberOfAffectedRows = command.ExecuteNonQuery(); 
```

Enter fullscreen mode Exit fullscreen mode

## 交易

当使用 ORM 类实体框架时，你可以免费得到的一件事是，当你持久化你的改变(即调用`.SaveChanges()`)时，这发生在一个事务中，所以所有的改变都被持久化或者都不被持久化。

谢天谢地，使用 ADO.NET 创建交易非常简单。这里有一个例子，我们在 db 事务中添加一个新的人，删除另一个人，更新另一个人:

```
using (var transaction = connection.BeginTransaction())
{
    var insertCommand = connection.CreateCommand();
    insertCommand.CommandText = "insert into people (first_name) values (@first_name)";
    insertCommand.Parameters.AddWithValue("@first_name", "Jane Smith");
    insertCommand.ExecuteNonQuery();

    var deleteCommand = connection.CreateCommand();
    deleteCommand.CommandText = "delete from people where last_name is null";
    deleteCommand.ExecuteNonQuery();

    var updateCommand = connection.CreateCommand();
    updateCommand.CommandText = "update people set first_name='X' where first_name='Y'";
    updateCommand.ExecuteNonQuery();

    transaction.Commit();
} 
```

Enter fullscreen mode Exit fullscreen mode

创建事务的最简单方法是从连接对象中请求一个事务。使用*隔离级别*创建一个事务。尽管我们没有在这里指定(将使用特定数据库的默认值)，您应该检查可用的[隔离级别](https://msdn.microsoft.com/en-us/library/system.data.isolationlevel.aspx)列表，并选择适合您需求的一个。

创建事务后，我们可以像以前一样进行所有操作，最后我们在事务上调用`.Commit()`。如果在调用`.Commit()`之前出现任何问题，所有的更改都会被回滚。

## 从数据库中获取元数据

这是一个题外话，但知道它是有用的。使用 about 可以提取数据库的元数据。例如，所有的列名及其类型构成了一个特定的表。

以下代码片段显示了如何从特定数据库表的所有列中获取所有列名和数据类型:

```
command.CommandText = "select * from people";
using(var reader = command.ExecuteReader()) 
{
    var columnSchema = reader.GetColumnSchema();
    foreach(var column in columnSchema)
    {
        Console.WriteLine($"{column.ColumnName} {column.DataTypeName}");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用衣冠楚楚

除了只使用 ADO.NET 之外，使用 Dapper 也同样简单，而且在性能方面差别很小。

如果你对 Dapper 不熟悉，这是来自 [StackExchange](https://stackexchange.com/) 的一个项目，它为网站的 StackExchange 家族提供动力( [StackOverflow](https://stackoverflow.com/) 、[超级用户](https://superuser.com/)、 [AskUbuntu](https://askubuntu.com/) 等)。

要使用 Dapper，您需要安装一个名为`Dapper`的 Nuget 包，以及针对目标数据库的特定 Nuget 包。例如，对于 Postgres:

```
$ dotnet add package Npgsql
$ dotnet add package Dapper 
```

Enter fullscreen mode Exit fullscreen mode

Dapper 给你的连接对象增加了几个扩展方法，分别是`Query<T>`和`Execute`。

`Query<T>`允许您运行查询，并将结果映射到您在通用参数中指定的类型。例如，您可以通过以下方式获取 people 表中的所有记录:

```
using Dapper; //you need this to get the extension methods on the connection object
//...

using (var connection = new NpgsqlConnection("theConnectionString"))
{
    IEnumerable<Person> people = connection.Query<Person>("select * from people");                        
} 
```

Enter fullscreen mode Exit fullscreen mode

`Query<T>`方法总是返回一个`IEnumerable<T>`，即使您只期望一条记录。例如，您可以这样做来插入一个新的人并获得新的 Id:

```
int newId = connection.Query<int>("insert into people (first_name, last_name, age) values (@FirstName, @LastName, @Age) returning id", new {
    FirstName = "John",
    LastName = "Doe",
    Age = "40"
}).FirstOrDefault(); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们为`Query`方法提供了两个参数，第一个是 SQL 语句，第二个是匿名对象，其属性名与 SQL 语句中的参数相匹配。也可以使用类的实例(例如`new Person { ... }`)来代替。

如果您不关心任何结果，例如您只想删除一条记录，您可以使用`Execute`方法，它将返回数据库中受影响的记录的数量。例如，如果您想要删除姓氏为空的所有记录:

```
var numberOfDeletedRecords = connection.Execute("delete from person where last_name is null"); 
```

Enter fullscreen mode Exit fullscreen mode

这只是对 Dapper 的简单介绍，如果你有兴趣了解更多，项目的 [github 页面是一个很好的资源。](https://github.com/StackExchange/Dapper)

我希望这篇博客文章已经给了你足够的信息，告诉你如何去整形。网芯。请在评论中告诉我你的想法。
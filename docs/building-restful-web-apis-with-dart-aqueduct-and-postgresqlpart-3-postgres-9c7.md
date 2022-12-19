# 用 Dart、Aqueduct 和 PostgreSQL 构建 RESTful Web APIs 第 3 部分:Postgres

> 原文：<https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-3-postgres-9c7>

[![Featured image for Building RESTful Web APIs with Dart, Aqueduct, and PostgreSQL](img/cbdf930372b6e08ad5ad2b8c1296074d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PZk7TA-r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dv19znif0x9wpvdcdt13.jpg)

* * *

***请注意:从 Dart 2 开始，导水管的 API 发生了变化，导致了重大变化。本文基于 Dart v1 的 Aqueduct 2.5.0。**T3】*

***我已经更新了这个作为新的视频系列:【http://bit.ly/aqueduct-tutorial】***

* * *

在第 2 部分中，我们实现了一个具有 CRUD 功能的“/books”路径，作为我们的 FaveReads 阅读列表应用程序的一部分，查看了 Aqueduct 的*路由器*和*http controller*。最终的解决方案留给我们完整的路由(*\0/*)和一个充当我们的数据存储的`List`类型。

本文是系列文章的一部分，涵盖以下主题:

*   [第 1 部分:设置并运行示例](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresql-55k)
*   [第 2 部分:用 CRUD 操作实现路由](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-2-routing-53ip)
*   第 3 部分:将 Web APIs 连接到 PostgreSQL 数据库(我们到了)
*   第 4 部分:编写自动化测试
*   [***奖励内容*** DB 迁移和模型关系](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlbonus-content-551g)😄

在这一部分中，我们将使用 PostgreSQL 数据库替换列表，从而允许我们持久保存发送给 API 的信息。我们将使用内置的 ORM 来帮助解决这个问题。

* * *

## 1。设置 PostgreSQL 服务器

我推荐在 Mac 上安装 [Postgres.app](https://postgresapp.com) ，因为这是最简单的开始方式，或者使用[官方下载页面](https://www.postgresql.org/download/)获得其他选项/平台。

一旦服务器运行，使用它的命令行工具(`psql`)创建您的数据库和一个可以使用下面的 SQL 命令访问它的用户:

```
CREATE DATABASE fave_reads;
CREATE USER dartuser;
ALTER USER dartuser WITH password 'dbpass123';
GRANT all ON database fave_reads TO dart; 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在使用 Postgresapp，您可以通过单击窗口中显示的任何数据库图标来打开这个命令行工具。

## 2。重构 Book 类

为了给我们的数据库提供正确的模型，我们需要扩展`ManagedObject<T>`类。托管对象处理数据库行到应用程序对象的转换，反之亦然。

将`lib/model/book.dart`中的`Book`型号修改如下:

```
import '../fave_reads.dart';

class Book extends ManagedObject<_Book> implements _Book {}

class _Book {
  @managedPrimaryKey
  int id;

  String title;
  String author;
  int year;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的数据库列将基于在`_Book`中定义的属性。属性被标记为每一列的主键，用`@managedPrimaryKey`表示。我们每增加一本新书，它就会自动增加。

*[了解有关数据建模的更多信息](https://aqueduct.io/docs/db/modeling_data/)*

## 3。创建 ManagedContext

为了将我们的应用程序绑定到数据库，它需要:

*   维护数据库连接
*   执行数据库查询，以及
*   将数据库行的结果转换为应用程序对象

这就是`ManagedContext`的用武之地！正是如此。

修改`FaveReadsSink`构造函数如下:

```
class FaveReadsSink extends RequestSink {
  FaveReadsSink(ApplicationConfiguration appConfig) : super(appConfig) {
    logger.onRecord.listen(
        (rec) => print("$rec  ${rec.error ?? ""}  ${rec.stackTrace ?? ""}"));

    var managedDataModel = new ManagedDataModel.fromCurrentMirrorSystem(); // load our models
    var persistentStore = new PostgreSQLPersistentStore.fromConnectionInfo(
      "dartuser", "dbpass123", "localhost", 5432, "fave_reads"); // configure the db connection

    ManagedContext.defaultContext = new ManagedContext(managedDataModel, persistentStore);
  }
  // ...
  // ...remaining logic
} 
```

Enter fullscreen mode Exit fullscreen mode

我们加载我们的模型，配置我们的数据库连接，并将它们作为依赖项传递，形成我们的托管上下文。这个托管上下文是在静态属性`defaultContext`下设置的，在与我们的数据库交互期间`Query<T>`对象将需要这个属性(稍后将详细介绍)。

## 4。构建我们的数据库模式

为此，我们将使用一个模式构建器。它将采用我们的数据模型和 Postgres 连接，并从中创建我们的数据库表。为了使用它，让我们在我们的`FaveReadsSink`类:
中定义一个名为`createDatabaseSchema`的方法

```
class FaveReadsSink extends RequestSink {
  //...
  //...
  //...
  Future createDatabaseSchema(ManagedContext context) async {
    var builder = new SchemaBuilder.toSchema(
      context.persistentStore,
      new Schema.fromDataModel(context.dataModel),
      isTemporary: false); // Set to false to persist our data

    for (var cmd in builder.commands) {
      await context.persistentStore.execute(cmd);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

生成了一堆 SQL 命令，这些命令在`for`循环中执行。在`willOpen`方法中，让我们触发这个操作:

```
@override
Future willOpen() async {
  try {
    await createDatabaseSchema(ManagedContext.defaultContext);
  } catch (e) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

在应用程序准备好接收请求之前，将创建模式。try/catch 块用于防止由于模式已经存在而第二次引发异常，从而退出进程。*很可能有更好的方法，所以我会让你自己玩这个*😉

我们现在应该能够重新启动我们的应用程序，并且应该仍然运行。只需确保您的 PostgreSQL 服务器处于活动状态。

最后，让我们重构我们的`BooksController`来与这个数据库通信。

## 5。重构我们的 BooksController 类

与数据库对话包括创建查询对象和调用方法来创建、读取、更新和删除数据。这些查询对象来自于`Query<T>`类的实例，其中`T`代表我们的模型:

```
// Example:
// Build your query
var query = new Query<Book>()
  ..values.name = 'Book name'
  ..values.author = 'John Smith'
  ..values.year = 2016;

// Execute query
var result = await query.insert(); 
```

Enter fullscreen mode Exit fullscreen mode

`Query<Book>`对象的`values`属性被设置为托管对象的实例，在本例中是`Book`。可用的 CRUD 方法包括`insert`(创建)`fetch`(读取)`update`和`delete`。

让我们修改`getAllBooks` responder 方法，从我们的数据库中读取:

```
@httpGet
Future<Response> getAllBooks() async {
  var query = new Query<Book>();
  return new Response.ok(await query.fetch());
} 
```

Enter fullscreen mode Exit fullscreen mode

并将`getBook` responder 方法修改如下:

```
@httpGet
Future<Response> getBook(@HTTPPath("index") int idx) async {
  var query = new Query<Book>()..where.id = whereEqualTo(idx);
  var book = await query.fetchOne();

  if (book == null) {
    return new Response.notFound(body: 'Book does not exist')
      ..contentType = ContentType.TEXT;
  }
  return new Response.ok(book);
} 
```

Enter fullscreen mode Exit fullscreen mode

查询对象使用`where`属性，它也是`Book`的一个实例。这允许我们对数据进行过滤。同一行的`whereEqualTo`功能是 Aqueduct 自带的匹配器功能之一。上面将执行下面的 SQL 查询:

```
SELECT (id, name, author, year) FROM _book WHERE id = "<idx>" 
```

Enter fullscreen mode Exit fullscreen mode

这里是完全更新的`BooksController`和各种查询方法:

```
class BooksController extends HTTPController {
  @httpGet
  Future<Response> getAllBooks() async {
    var query = new Query<Book>();
    return new Response.ok(await query.fetch());
  }

  @httpGet
  Future<Response> getBook(@HTTPPath("index") int idx) async {
    var query = new Query<Book>()..where.id = whereEqualTo(idx);
    var book = await query.fetchOne();

    if (book == null) {
      return new Response.notFound(body: 'Book does not exist')
        ..contentType = ContentType.TEXT;
    }
    return new Response.ok(book);
  }

  @httpPost
  Future<Response> addBook(@HTTPBody() Book book) async {
    var query = new Query<Book>()..values = book;
    return new Response.ok(await query.insert());
  }

  @httpPut
  Future<Response> updateBook(@HTTPPath("index") int idx, @HTTPBody() Book book) async {
    var query = new Query<Book>()
      ..values = book
      ..where.id = whereEqualTo(idx);
    var updatedBook = await query.updateOne();

    if (updatedBook == null) {
      return new Response.notFound(body: 'Book does not exist');
    }
    return new Response.ok(updatedBook);
  }

  @httpDelete
  Future<Response> deleteBook(@HTTPPath("index") int idx) async {
    var query = new Query<Book>()..where.id = whereEqualTo(idx);
    var deletedBookId = await query.delete();

    if (deletedBookId == 0) {
      return new Response.notFound(body: 'Book does not exist');
    }
    return new Response.ok('Successfully deleted book.');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

重启应用程序，用 [Postman](https://www.getpostman.com/apps) 测试。

## 结论

就后端而言，我们现在有了一个完全工作的 API。在第 4 部分中，我们将编写一些测试。

我鼓励你浏览下面的进一步阅读材料，以完全掌握我们所涉及的一些概念。一如既往，**我喜欢反馈**。让我知道你喜欢什么，不喜欢什么，以及你接下来想看什么。我真的很感激。

本系列的第 3 部分到此结束。源代码在 github 上[可以获得，并且会随着我们在这个系列中的进展而更新。敬请关注更多内容。](https://github.com/graphicbeacon/favereads)

* * *

## 进一步阅读

1.  [渡槽数据库概述](https://aqueduct.io/docs/db/overview/)
2.  [导水管状片段](https://aqueduct.io/docs/snippets/orm/)
3.  被管理对象< T >类文档
4.  [ManagedContext 类文档](https://www.dartdocs.org/documentation/aqueduct/latest/aqueduct/ManagedContext-class.html)

* * *

[![graphicbeacon image](img/ebd69e31cdafde0c3cc551828feae27a.png)](/graphicbeacon) [## 用 Dart、Aqueduct 和 PostgreSQL 构建 RESTful Web APIs 第 4 部分:测试

### 杰梅因·奥蓬 5 月 31 日 187 分钟阅读

#api #webdev #dart #testing](/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-4-testing-3974)

* * *

*最初贴在[介质上](https://medium.com/@98d967ac6f3a/90ff70742675)T3】*
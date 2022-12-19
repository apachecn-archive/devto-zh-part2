# 使用 Dart、Aqueduct 和 PostgreSQL 构建 RESTful Web APIs 额外内容

> 原文：<https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlbonus-content-551g>

[![Featured image goes here](../Images/91d5958f1a27eca1e9f70c5e0a4ada74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r4h69LMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bckufxk26wr197vhvrvd.jpg)

* * *

***请注意:从 Dart 2 开始，导水管的 API 发生了变化，导致了重大变化。本文基于 Dart v1 的 Aqueduct 2.5.0。**T3】*

***我已经更新了这个作为新的视频系列:【http://bit.ly/aqueduct-tutorial】***

* * *

从我们离开的地方继续，我们现在已经为我们的工作 API 编写了一些测试，搭建了我们的项目，设置了一些路线并集成了一个关系数据库。

本文是系列文章的一部分，涵盖:

*   [第 1 部分:设置并运行示例](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresql-55k)
*   [第 2 部分:用 CRUD 操作实现路由](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-2-routing-53ip)
*   [第 3 部分:将 Web APIs 连接到 PostgreSQL 数据库](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-3-postgres-9c7)
*   第 4 部分:编写自动化测试
*   **奖励内容**数据库迁移和模型关系*(我们在这里😄)*

在这个额外的部分中，我们将介绍 Aqueduct 的数据库迁移工具，以及在对我们当前的应用程序模型进行修改时，它是如何提供帮助的。到目前为止，我们只使用了`Book`模型，这是为了简化事情。

* * *

## 1。创建迁移文件

Aqueduct 的迁移工具对于创建和执行迁移文件非常有用。这些文件包含基于当前应用程序模型创建和修改数据库表的 SQL 命令。

在我们项目的根目录下，让我们通过运行下面的命令来生成我们的迁移文件:

```
aqueduct db generate 
```

Enter fullscreen mode Exit fullscreen mode

这将在`migrations/00000001_Initial.migration.dart`创建一个迁移文件。

[![Initial migration file](../Images/48efa99abfb7ee4251f6b89a9a442dc8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1g5epiiy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ut7mmydcl2fmu44y7fy.png)

打开该文件详细描述了基于`lib/model`中的模型定义构建数据库表的步骤，在本例中是`book.dart`。

## 2。创建作者模型

我们当前的`Book`模型的作者被设置为类型为`String`的实例属性，它转化为我们数据库中的 **_book** 表中的一列。

在这种简单的情况下是可行的，但是如果我们需要指定多个作者呢？当然，我们可以使用逗号作为分隔符，在处理每个作者时将它们分开，但是如果我们希望每个作者都有一个简短的描述呢？将所有信息存储在一个数据库列中当然不是什么好事！

理想情况下，我们希望捕获这个`author`属性作为它自己的实体，这样我们就可以随着应用程序的增长来分离这个问题。

让我们用我们的`Author`模型:
创建`lib/model/author.dart`

```
import '../fave_reads.dart';
import './book.dart';

class Author extends ManagedObject<_Author> implements _Author {}

class _Author {
  @managedPrimaryKey
  int id;

  String name;

  @ManagedRelationship(#authors) // <--- Sets our column as a foreign key pointing to its associated book id
  Book book;
} 
```

Enter fullscreen mode Exit fullscreen mode

这类似于`lib/model/book.dart`，除了我们现在已经建立了一个一对多的关系，其中一本书有许多作者。我们有一个`book`属性，并使用`@ManagedRelationship()`注释来设置这种关系。

第一个参数(#authors)是一个符号，表示我们的`Book`模型上的属性，它标记了这种关系。让我们修改我们的`Book`模型，使其具有这个属性:

```
import '../fave_reads.dart';
import './author.dart';

class Book extends ManagedObject<_Book> implements _Book {}

class _Book {
  @managedPrimaryKey
  int id;

  String title;
  int year;

  ManagedSet<Author> authors; // <-- over here
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`authors`属性是一个`ManagedSet<T>`类型，意味着包含一组作者的书。这意味着当我们运行我们的应用程序时，将创建一个名为 **_author** 的新表，其中包含列`id`、`name`和`book_id`。后者是一个外键，指向 **_book** 表上的相关行 id。

让我们通过运行`aqueduct db generate`来更新我们的迁移目录。这将在前面的文件旁边生成另一个`migration/*.migration.dart`文件。它在初始迁移文件的基础上添加了 SQL 命令，用于:

1.  构建我们的**_ 作者**表
2.  删除我们的 **_book** 表中的`_author`列
3.  在作者表中添加一个`book_id`列

运行下面的命令会将数据库升级到更新的模式:

```
aqueduct db upgrade --connect postgres://<user>:<password>@localhost:5432/<database-name> 
```

Enter fullscreen mode Exit fullscreen mode

[![Updated database schema](../Images/37f303329cd1441c5ba5b1a1738c0764.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b9H9JHOK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qzvuicjzg40yywd4nhwu.png)

*这假设您要迁移到的数据库中没有任何预先存在的表，即 **_book** 和 **_author** 表不存在，否则将会抛出异常。*

## 3。修改测试

我们的测试设置需要修改，以包含我们管理的作者集合:

```
// Populate DB
var books = [
  new Book()
    ..title = "Head First Design Patterns"
    ..authors = (new ManagedSet()
      ..add(new Author()..name = "Bert Bates")
      ..add(new Author()..name = "Kathy Sierra")
      ..add(new Author()..name = "Eric Freeman"))
    ..year = 2004,
  new Book()
    ..title = "Clean Code: A handbook of Agile Software Craftsmanship"
    ..authors =
        (new ManagedSet()..add(new Author()..name = "Robert C. Martin"))
    ..year = 2008,
  new Book()
    ..title = "Code Complete: A Practical Handbook of Software Construction"
    ..authors =
        (new ManagedSet()..add(new Author()..name = "Steve McConnell"))
    ..year = 2004
]; 
```

Enter fullscreen mode Exit fullscreen mode

在随后的循环中，我们创建了两个查询来插入书籍和作者，两个查询是因为模型数据放在不同的表中:

```
// Query for books
await Future.forEach(books, (Book b) async {
  var query = new Query<Book>()..values = b;
  var insertedBook = await query.insert();

  // Query for authors
  await Future.forEach(b.authors, (Author a) async {
    var query = new Query<Author>()
      ..values = a
      ..values.book = insertedBook; // Reference to its associated book, which becomes a foreign key column `book_id`
    return (await query.insert());
  });
  return insertedBook;
}); 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们修改第一个单元测试的断言:

```
// ...
// ...
test("GET /books returns list of books", () async {
  // ..
  // ..
  expectResponse(response, 200, body: allOf([
      hasLength(greaterThan(0)),
      everyElement(partial(
        {
          "title": isString,
          "year": isInteger,
          "authors": isList
        }))
    ]));
}); 
```

Enter fullscreen mode Exit fullscreen mode

测试的全部变化是[这里是](https://github.com/graphicbeacon/favereads/blob/master/test/books_controller_test.dart)。

## 4。让测试通过

在`lib/controller/books_controller.dart`中，将`getAllBooks()`方法修改如下:

```
// ..
class BooksController extends HTTPController {
  @httpGet
  Future<Response> getAllBooks() async {
    var query = new Query<Book>()..join(set: (book) => book.authors);
    return new Response.ok(await query.fetch());
  }
  // ..
  // ..
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用`join`方法从与每本书相关的 **_author** 表中提取行数据。

向`http://localhost:8000/books`发送 GET 请求将返回下面的 JSON 响应:

```
{  "id":  1,  "title":  "Dart: Scalable Application Development",  "year":  2016,  "authors":  [{  "id":  1,  "name":  "Dave Mitchells"  "book":  {  "id":  1  }  }]  } 
```

Enter fullscreen mode Exit fullscreen mode

我们的`addBook()` responder 方法现在修改如下:

```
// ..
class BooksController extends HTTPController {
  //..
  @httpPost
  Future<Response> addBook(@HTTPBody() Book book) async {
    var query = new Query<Book>()..values = book;
    var insertedBook = await query.insert();

    // Insert authors from payload
    await Future.forEach(book.authors, (Author a) async {
      var author = new Query<Author>()
        ..values = a
        ..values.book = insertedBook; // set foreign key to inserted book

      return (await author.insert());
    });

    var insertedBookQuery = new Query<Book>()
      ..where.id = whereEqualTo(insertedBook.id);

    insertedBookQuery.join(set: (book) => book.authors)
      ..returningProperties((Author author) => [author.name]); // <-- Specify the columns returned for each author in the response

    return new Response.ok(await insertedBookQuery.fetchOne());
  }
  //..
} 
```

Enter fullscreen mode Exit fullscreen mode

这需要一个格式为
的有效负载

```
{  "title":  "Dart: Scalable Application Development",  "year":  2016,  "authors":  [{  "name":  "Dave Mitchells"  }]  } 
```

Enter fullscreen mode Exit fullscreen mode

这里的可以看到全部的变化[。](https://github.com/graphicbeacon/favereads/blob/master/lib/controller/books_controller.dart)

## 结论

查看下面的进一步阅读材料，更详细地了解数据库迁移和模型关系。一如既往，欢迎反馈。让我知道你喜欢什么，不喜欢什么，接下来你想看什么。

这个系列到此结束。源代码[可在 github](https://github.com/graphicbeacon/favereads) 上获得。

感谢您坚持阅读本系列，并关注我的更多 Dart 和 Aqueduct 教程。

## 进一步阅读

1.  [数据库迁移和工具](http://aqueduct.io/docs/db/db_tools)
2.  [建模数据](http://aqueduct.io/docs/db/modeling_data)
3.  [高级数据库查询](http://aqueduct.io/docs/tut/model-relationships-and-joins/#more-on-joins-and-relationships)
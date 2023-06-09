# 用 Dart、Aqueduct 和 PostgreSQL 构建 RESTful Web APIs 第 4 部分:测试

> 原文：<https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-4-testing-3974>

[![Featured image for Building RESTful Web APIs with Dart, Aqueduct, and PostgreSQL](img/cbdf930372b6e08ad5ad2b8c1296074d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PZk7TA-r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dv19znif0x9wpvdcdt13.jpg)

* * *

***请注意:从 Dart 2 开始，导水管的 API 发生了变化，导致了重大变化。本文基于 Dart v1 的 Aqueduct 2.5.0。**T3】*

***我已经更新了这个作为新的视频系列:【http://bit.ly/aqueduct-tutorial】***

* * *

在第 3 部分中，我们将 API 与 PostgreSQL 数据库集成，利用 Aqueduct 的 ORM 作为管理数据事务的手段。了解了托管对象和托管上下文之后，我们得到了一个解决方案，它提供了数据持久性，而不需要编写复杂的 SQL 查询。

本文是系列文章的一部分，涵盖以下主题:

*   [第 1 部分:设置并运行示例](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresql-55k)
*   [第 2 部分:用 CRUD 操作实现路由](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-2-routing-53ip)
*   [第 3 部分:将 Web APIs 连接到 PostgreSQL 数据库](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-3-postgres-9c7)
*   第四部分:写作测试
*   [***奖励内容*** DB 迁移和模型关系](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlbonus-content-551g)😄

在这一部分中，我们将编写测试，同时重构我们的逻辑以适应这些测试。我们将使用建立在 Dart 团队[测试](https://pub.dartlang.org/packages/test)包之上的 Aqueduct 内置测试库，这样我们就不用自己设置了。

* * *

## 测试线束

使用第 1 部分中的搭建工具在项目根目录下创建了一个`test/`文件夹，文件结构如下:

```
test/
|--harness
   |--app.dart
   example_test.dart 
```

Enter fullscreen mode Exit fullscreen mode

测试工具`harness/app.dart`负责启动和停止我们的应用程序。当我们在`test/example_test.dart` :
中查看这个片段时，我们看到了这一点

```
TestApplication app = new TestApplication();

// Runs before all tests
setUpAll(() async {
  await app.start();
});

// Runs after all tests
tearDownAll(() async {
  await app.stop();
}); 
```

Enter fullscreen mode Exit fullscreen mode

应用程序在运行所有测试之前启动，之后立即停止。我们的测试工具复制了`bin/main.dart`,但有以下例外:

1.  指定了一个端口`0`,这样我们的测试可以在任何可用的端口上运行
2.  给出了一个单独的配置文件(config.src.yaml ),其中包含特定于测试的数据
3.  当调用`application.start`时,`runOnMainIsolate`选项被设置为 true，在主线程上运行我们的测试。这将禁用多线程，以便我们可以访问应用程序的状态和服务来执行我们的断言。
4.  一个`TestClient`被实例化以提供一个 HTTP 客户端来执行对我们的 API 的请求。使用它将为我们的断言提供测试响应。
5.  提供了一种停止应用程序的方法，以便在所有测试运行后调用

为了运行我们的测试，让我们在第 3 部分中重构我们的解决方案，从我们的数据库配置开始。这是为了支持测试和生产环境的灵活性。

## 1。配置数据库

再次查看`lib/fave_reads_sink.dart`中的`FaveReadsSink`，相同的 Postgres 细节将被用于我们的测试和生产环境(*哎呀！*)

通过分离测试和生产信息，配置文件的使用有助于缓解这一问题。在我们的项目中，我们将在根级别使用`config.src.yaml`和`config.yaml`文件。

让我们首先用我们的数据库连接信息来修改这些文件:

```
# config.src.yaml - for test environment
database:
  username: dartuser
  password: dbpass123
  host: localhost
  port: 5432
  databaseName: fave_reads_test
  isTemporary: true 
```

Enter fullscreen mode Exit fullscreen mode

在我们的 config.yaml 文件中:

```
# for production environment
database:
  username: dartuser
  password: dbpass123
  host: localhost
  port: 5432
  databaseName: fave_reads
  isTemporary: false 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们对`FaveReadsSink` :
进行以下修改

```
// fave_reads_sink.dart
// ...
// ...
class FaveReadsSink extends RequestSink {
  FaveReadsConfiguration config;

  FaveReadsSink(ApplicationConfiguration appConfig) : super(appConfig) {
    logger.onRecord.listen(
        (rec) => print("$rec  ${rec.error ?? ""}  ${rec.stackTrace ?? ""}"));

    var configFilePath = appConfig.configurationFilePath;
    config = new FaveReadsConfiguration(configFilePath);

    var managedDataModel = new ManagedDataModel.fromCurrentMirrorSystem();
    var persistentStore = new PostgreSQLPersistentStore.fromConnectionInfo(
        config.database.username,
        config.database.password,
        config.database.host,
        config.database.port,
        config.database.databaseName);

    ManagedContext.defaultContext =
        new ManagedContext(managedDataModel, persistentStore);
  }
  // ...
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的配置文件路径由`bin/main.dart`和`test/harness/app.dart`中的`Application`构造函数的`configurationFilePath`属性指定。我们的`FaveReadsSink`类在一个应用程序对象中被实例化，通过它接收请求接收器构造函数的`appConfig`参数中的配置路径。

然后，我们通过实例化`ConfigurationItem`助手类的子类`FaveReadsConfiguration`来提取配置信息。这会将配置文件解析为一个映射。

在请求接收器之后，让我们定义我们的配置项:

```
class FaveReadsConfiguration extends ConfigurationItem {
  FaveReadsConfiguration(String fileName) : super.fromFile(fileName);

  DatabaseConnectionConfiguration database;
} 
```

Enter fullscreen mode Exit fullscreen mode

属性映射到我们配置文件中的同一个键。这就是暴露我们的数据库信息被这样访问的原因:`config.database.username`

## 2。将 SchemaBuilder 提取到实用程序文件中

让我们将`createDatabaseSchema`方法移到一个单独的文件中，这个文件也将被我们的测试工具使用:

```
// This goes in lib/utils/utils.dart
import 'dart:async';
import 'package:aqueduct/aqueduct.dart';

Future createDatabaseSchema(ManagedContext context, bool isTemporary) async {
  try {
    var builder = new SchemaBuilder.toSchema(
        context.persistentStore,
        new Schema.fromDataModel(context.dataModel),
        isTemporary: isTemporary);

    for (var cmd in builder.commands) {
      await context.persistentStore.execute(cmd);
    }
  } catch (e) {
    // Database may already exist
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有第二个参数`isTemporary`要由我们的配置文件来设置。这个选项决定了我们的数据是否被持久化。对于我们的测试，我们将其设置为`true`。

现在让我们将这个实用程序重新导入到`lib/fave_reads_sink.dart` :

```
import 'fave_reads.dart';
import './controller/books_controller.dart';
import './utils/utils.dart'; // 👈👈👈

class FaveReadsSink extends RequestSink {
  //...
  //...
  @override
  Future willOpen() async {
    await createDatabaseSchema(
        ManagedContext.defaultContext, config.database.isTemporary);
  }
  //...
}
//... 
```

Enter fullscreen mode Exit fullscreen mode

## 3。建立测试数据库

打开`psql`工具并运行下面的查询:

```
CREATE DATABASE fave_reads_test;
CREATE USER dartuser;
ALTER USER dartuser WITH password 'dbpass123';
GRANT ALL ON database fave_reads_test TO dartuser; 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经在第 3 部分中这样做了，那么您可以跳过第 2 行和第 3 行。

## 4。编写您的测试

将`example_test.dart`重命名为`books_controller_test.dart`,并用以下内容替换其内容:
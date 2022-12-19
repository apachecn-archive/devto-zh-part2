# 用 Dart、Aqueduct 和 PostgreSQL 构建 RESTful Web APIs 第 1 部分

> 原文：<https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresql-55k>

[![Featured image for Building RESTful Web APIs with Dart, Aqueduct and PostgreSQL](../Images/cbdf930372b6e08ad5ad2b8c1296074d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PZk7TA-r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dv19znif0x9wpvdcdt13.jpg)

* * *

***请注意:从 Dart 2 开始，导水管的 API 发生了变化，导致了重大变化。本文基于 Dart v1 的 Aqueduct 2.5.0。**T3】*

***我已经更新了这个作为新的视频系列:【http://bit.ly/aqueduct-tutorial】***

* * *

在这个关于另一个 RESTful API 教程(# yetanotherrat)的新系列中，我们将探索 Dart 平台及其在构建服务器端应用程序方面的贡献。

继[我的上一篇探索 Dart 语言的文章](https://dev.to/graphicbeacon/learn-dart-before-you-flutter-2997)之后，我还研究了如何用 Dart 构建 RESTful 服务。进一步挖掘后，我在 Stable Kernel 旁偶然发现了渡槽。我第一眼看到它已经通过了第 2 版时印象深刻，一旦我发现文档很全面，有易于遵循的示例，我就确信要进一步探索。

在经历了如此多产和令人耳目一新的时间来浏览它的文档和跟随他们的例子之后，我现在很高兴带你和我一起踏上构建一个从头到尾都可以工作的 api 的旅程！

更喜欢视频？[在 Youtube 上观看](https://youtu.be/XBRyW5Nrcqs)

* * *

## 那么什么是渡槽呢？

这是一个开源框架，用于在服务器上创建和部署 RESTful web apis。它与*快车*和*哈比神*，甚至*有着相似的味道。NET Web API* ，提供了一个具有全面特性列表的入口点。

我最喜欢的是:

1.  流体，可链接**路线**。*组成你的路线的函数式风格及其处理方法*
2.  一个 **CLI** 工具。*这允许你通过发布命令来搭建你的下一个项目*
3.  **开箱即用的多线程**。*通过 Dart 的“隔离”,加速应用程序的多个实例，在服务器上的所有 CPU 内核间扩展。*
4.  有一个内置的表单。*如果你使用关系数据库，这是必备的！还支持数据库迁移。*
5.  拥有一个**集成测试**库。*因为你需要测试所有的东西！与 TravisCI 等工具配合良好。*

在这个系列中，我们将为个人阅读列表(*姑且称之为**收藏夹*** )构建一个 api📚)，涵盖以下主题:

*   **第 1 部分:设置并运行示例** *(我们在这里)*
*   [第 2 部分:用 CRUD 操作实现路由](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-2-routing-53ip)
*   [第 3 部分:将 Web APIs 连接到 SQL 数据库](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-3-postgres-9c7)
*   第 4 部分:编写自动化测试
*   [***奖励内容*** DB 迁移和模型关系](https://dev.to/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlbonus-content-551g)😄

[![Shall we begin?](../Images/28b7e6d6ea5141fc8e1c3bf3adfde93d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P5nlnCLE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sh243oz27767ygjannp5.gif)

## 设置和运行示例

如果你还没有，我鼓励你安装 Dart SDK。它有针对 Windows、Mac 和 Linux 的说明。

您可以通过检查终端:
来确认 Dart 正在运行

```
dart --version 
```

Enter fullscreen mode Exit fullscreen mode

Dart 附带了一个名为 **pub** 的工具来管理它的包。让我们用这个工具来安装渡槽:

```
pub global activate aqueduct 
```

Enter fullscreen mode Exit fullscreen mode

这使我们能够访问可执行文件`aqueduct`。转到您的工作目录，让我们创建我们的项目:

```
aqueduct create fave_reads && cd fave_reads 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该位于包含所有项目文件的`fave_reads`目录中。现在我们将关注:

```
bin/
  main.dart
lib/
  fave_reads_sink.dart
pubspec.yaml 
```

Enter fullscreen mode Exit fullscreen mode

*   **bin/main.dart** *创建我们的服务器并启动应用*
*   **lib/fave_reads_sink.dart**
*   **pubspec.yaml** *包含关于项目的元数据。类似于有一个 package.json 用于 Node.js 开发*

让我们使用下面的命令启动应用程序:

```
aqueduct serve # or `dart bin/main.dart` 
```

Enter fullscreen mode Exit fullscreen mode

我们开始吧:

[![Aqueduct serve terminal](../Images/78e33c0c3f8381c1995ea4285299de63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BTJWgBo5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fplcso11d8iadkfpq4yd.png)

我们现在有一个在`http://localhost:8081`运行的服务器，`/example`是唯一创建的路由。访问`http://localhost:8081/example`将返回如下响应:

```
{  "key":  "value"  } 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，值得一提的是，Aqueduct 有一个 RequestSink 的概念，根据文档:

> 处理应用程序的初始化，包括设置路由、授权和数据库连接。

应用程序需要一个`RequestSink`子类来接收请求。我们的例子有一个扩展基类`RequestSink`的`FaveReadsSink`，使我们能够用自己的实现覆盖它的方法。这些是`setupRouter`和`willOpen`。第一种方法将允许我们定义与相关控制器和其他中间件的路由，而后者允许我们在路由建立之后和应用程序接收请求之前执行任何异步初始化。

对于当前请求，默认响应类型设置为 JSON。然而，这可以被设置为适合满足我们需求的任何内容类型。

让我们通过在 setupRouter 方法中创建第二条路由来演示这一点:

```
router.route('/').listen((request) async {
  return new Response.ok('Hello world')
    ..contentType = ContentType.TEXT;
}); 
```

Enter fullscreen mode Exit fullscreen mode

使用方法级联，我们能够将`contentType`属性设置为`text/plain`。`ContentType`工具自带[内置](https://api.dartlang.org/stable/1.24.3/dart-io/ContentType-class.html)与飞镖，可以通过以下方式使用:

```
new ContentType(primaryType, subType, {String charset, Map parameters}); 
```

Enter fullscreen mode Exit fullscreen mode

它已经有了 HTML、JSON 和 TEXT 的常量，所以在大多数情况下你应该使用它们。

现在，通过终止当前进程并再次运行`aqueduct serve`来重启服务器。访问根路径应该会得到以下结果:

[![Aqueduct server localhost](../Images/f7eed30fb74e7a590d4edfed6e31d611.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YqbBl-7B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pyetxyk5nm91dbtjzcnq.png)

在总结之前，让我们将应用实例扩展到我们拥有的内核数量。在 **bin/main.dart** 中，当调用 app.start 时，当前设置为 2:

```
await app.start(numberOfInstances: 2); 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，我们将从“dart:io”库中导入 Dart 的[平台](https://api.dartlang.org/stable/1.24.3/dart-io/Platform-class.html)类，并将 **main.dart** 修改如下:

```
import 'dart:io' show Platform;

Future main() async {
  ...
  ...
  await app.start(numberOfInstances: Platform.numberOfProcessors);
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## VS 代码的一些开发技巧👈

首先，确保通过 Dart 代码安装 [Dart](https://marketplace.visualstudio.com/items?itemName=Dart-Code.dart-code) 扩展，以启用 Dart 和颤振支持。

其次，使用以下设置为 Dart 添加一个启动配置:

```
{  "name":  "Dart",  "type":  "dart",  "request":  "launch",  "program":  "${workspaceFolder}/bin/main.dart"  } 
```

Enter fullscreen mode Exit fullscreen mode

这将允许您通过 VS 代码运行应用程序，并能够添加断点来帮助调试。

[![VS Code debugging with Dart](../Images/e9432ad9402e2a2738408b5d78e763b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q3EzQN2v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2434ol0dmaafrj1y74qn.gif)

* * *

在我们结束之前，我想问你一些事情… **在这个系列的结尾，你期望得到什么？**在下面加个评论让我知道，谢谢。

本系列的第 1 部分到此结束。这个系列的代码[可以在 github](https://github.com/graphicbeacon/favereads) 上获得，并且会随着我们在这个系列中的进展而更新。敬请关注更多内容。

## 进一步阅读

1.  [渡槽文件](https://aqueduct.io/docs)
2.  [开始使用 Pub](https://www.dartlang.org/tools/pub/get-started)
3.  [Joe Conway 在 2016 年 Dart 开发者峰会上的演讲](https://www.youtube.com/watch?v=twr3cDFCeo4)

* * *

[![graphicbeacon image](../Images/ebd69e31cdafde0c3cc551828feae27a.png)](/graphicbeacon) [## 用 Dart、Aqueduct 和 PostgreSQL 构建 RESTful Web APIs 第 2 部分:路由

### 杰梅因奥蓬 5 月 18 日 188 分钟阅读

#api #webdev #dart #sql](/graphicbeacon/building-restful-web-apis-with-dart-aqueduct-and-postgresqlpart-2-routing-53ip)

* * *

*最初贴在[介质上](https://itnext.io/building-restful-web-apis-with-dart-aqueduct-and-postgresql-3cc9b931f777)T3】*
# 在 Heroku 上部署 Dart 2 应用程序

> 原文：<https://dev.to/graphicbeacon/deploying-dart-apps-to-heroku-23ih>

作为探索 Dart 客户端开发的一部分，我花了两周时间开发了一个 web 应用程序，我已经将它[部署在 Heroku 上](https://memorygameweb.herokuapp.com),我想向您展示如何做同样的事情。我不会讲述我是如何编写这个应用程序的，因为这里的重点是部署。也就是说，我将在文章末尾链接到源代码😉

## 先决条件🔥

你需要这些来开始:

1.  [Dart 2 SDK](https://www.dartlang.org/tools/sdk#install)
2.  [去](https://git-scm.com/downloads)
3.  [Heroku 账号](https://signup.heroku.com/)
4.  [英雄库 CLI](https://devcenter.heroku.com/articles/heroku-cli)

设置好之后，在您的终端上运行`heroku login`，使用您的凭证登录。

* * *

## 1。设置您的 web 项目

在终端中导航到您的项目。如果你没有，你可以[复制我的包含我的解决方案的回购](https://github.com/graphicbeacon/dart_web_project),直接跳到第 3 步。

或者，您可以使用 **stagehand** 脚手架工具:
生成一个测试项目

```
$ pub global activate stagehand
$ mkdir web_project && cd web_project
$ stagehand web-simple 
```

Enter fullscreen mode Exit fullscreen mode

## 2。写一个 HTTP 服务器

将生成的`pubspec.yaml`文件的依赖关系修改为:

```
dependencies:
  shelf: any
  shelf_static: any 
```

Enter fullscreen mode Exit fullscreen mode

运行`pub get`来更新我们的依赖关系。我们使用**货架**来简化创建 web 服务器。

用生成我们的服务器:
的逻辑创建`bin/main.dart`

```
import 'dart:io' show Platform;
import 'dart:async' show runZoned;
import 'package:path/path.dart' show join, dirname;
import 'package:shelf/shelf_io.dart' as io;
import 'package:shelf_static/shelf_static.dart';

void main() {
  // Assumes the server lives in bin/ and that `webdev build` ran
  var pathToBuild = join(dirname(Platform.script.toFilePath()), '..', 'build');

  var handler = createStaticHandler(pathToBuild, defaultDocument: 'index.html');

  var portEnv = Platform.environment['PORT'];
  var port = portEnv == null ? 9999 : int.parse(portEnv);

  runZoned(() {
    io.serve(handler, '0.0.0.0', port);
    print("Serving $pathToBuild on port $port");
  }, onError: (e, stackTrace) => print('Oh noes! $e  $stackTrace'));
} 
```

Enter fullscreen mode Exit fullscreen mode

## 3。在本地运行项目

安装 **webdev** 工具:

```
$ pub global activate webdev 
```

Enter fullscreen mode Exit fullscreen mode

然后运行下面的命令来生成我们的包:

```
$ webdev build # or `pub global run webdev build` 
```

Enter fullscreen mode Exit fullscreen mode

这将产生一个`build`文件夹。

[![Production bundle generated with webdev tool](img/30dc89b27c993c4d5ddcc5dec4ae2d4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0tcfw4WM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ll3793zxvasgw4yfpgt4.png)

运行`dart bin/main.dart`并访问`http://localhost:9999`

[![Image of localhost:9999](img/4a7227da01a6b810800005cd42883699.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zVo7M4nm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oxepyr1nnqtqfvf3clov.png)

## 4。创建您的 Heroku 应用程序

运行以下命令创建一个新应用:

```
$ heroku create
Creating secure-spire-84236... done, stack is cedar-14
http://secure-spire-84236.herokuapp.com/ | https://git.heroku.com/secure-spire-84236.git
Git remote heroku added 
```

Enter fullscreen mode Exit fullscreen mode

这将为您的应用程序生成一个随机名称，因此在这种情况下为`secure-spire-84236`。

最后，提交您的项目并添加`heroku` remote:

```
$ git init
$ git add .
$ git commit -m "Initial commit."

$ heroku git:remote -a secure-spire-84236 
```

Enter fullscreen mode Exit fullscreen mode

## 5。配置构建包

运行部署命令(`git push heroku master`)会抛出异常，因为 Heroku 需要正确的*构建包*来运行 Dart 应用。

buildpack 包含一些脚本，这些脚本设置了构建和服务项目所必需的依赖项。Heroku 目前没有正式支持 Dart。

然而，平台允许我们指定定制的构建包，虽然 Dart 团队，我做了一个[调整](https://github.com/graphicbeacon/heroku-buildpack-dart/blob/ae89937a28eeb463d25299d5b37341dd138f4793/bin/compile#L103-L107)让它与 Dart 2 一起工作，所以我们将使用我的构建包:

```
$ heroku buildpacks:set https://github.com/graphicbeacon/heroku-buildpack-dart.git 
```

Enter fullscreen mode Exit fullscreen mode

并设置 buildpack 所需的配置:

```
$ heroku config:set DART_SDK_URL=https://storage.googleapis.com/dart-archive/channels/dev/release/2.0.0-dev.67.0/sdk/dartsdk-linux-x64-release.zip # From the dart install page
$ heroku config:set DART_BUILD_CMD="/app/dart-sdk/bin/pub global activate webdev && /app/dart-sdk/bin/pub global run webdev build" 
```

Enter fullscreen mode Exit fullscreen mode

告诉 buildpack 获取最新的 Dart SDK，并使用 **webdev** 工具构建项目。

## 6。部署所有的东西！🚀

在我们的项目根目录下创建一个`Procfile`，其中包含启动我们的服务器的指令:

```
web: ./dart-sdk/bin/dart bin/main.dart 
```

Enter fullscreen mode Exit fullscreen mode

提交更改并立即部署:

```
$ git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到一条带有网址的确认消息:

[![Confirmation message with web url](img/fd178f675ce7a631bb7b893c0563915d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--REeYEvGQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2cmna80oxs7vfw54ftuk.png)

并在浏览器中访问:

[![Site open in browser](img/b47216a5351bb6b37b8c583f60a37656.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zoljQc2j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vgu17zr7xl5k1bwd0c6v.png)

## 结论

我希望你喜欢这个教程。记下一些评论，让我知道你对此有何感想。

这里是我部署的[游戏](https://memorygameweb.herokuapp.com)和[源代码](https://github.com/graphicbeacon/memory-game-web)。

**喜欢，分享，关注我**😍有关 Dart 的更多内容。

## 快速链接

1.  [Dart 2 SDK](https://www.dartlang.org/tools/sdk#install)
2.  [Heroku 如何工作](https://devcenter.heroku.com/articles/how-heroku-works)
3.  [开始| Webdev Dart](https://webdev.dartlang.org/guides/get-started)
4.  **[免费飞镖截屏在 Egghead.io](http://egghead.io/instructors/jermaine-oppong)**
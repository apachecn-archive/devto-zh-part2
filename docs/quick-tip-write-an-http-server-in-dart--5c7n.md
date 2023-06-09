# 快速提示:用 Dart 编写 HTTP 服务器💻

> 原文：<https://dev.to/graphicbeacon/quick-tip-write-an-http-server-in-dart--5c7n>

在今天的快速技巧中，我们将探索 Dart 为我们提供的用于创建 web 服务器的内置类之一。这个类是 SDK 中“ [**dart:io**](https://api.dartlang.org/dev/2.0.0-dev.68.0/dart-io/dart-io-library.html) ”库的一部分。

* * *

这个类被恰当地命名为`HttpServer`。下面是我们如何使用它:

```
HttpServer.bind("localhost", 8080).then((HttpServer server) { ... }); 
```

Enter fullscreen mode Exit fullscreen mode

`bind()`表示一个静态方法，它将主机名和端口作为必需的参数。这将返回一个`Future<HttpServer>`，允许我们链接像`then()`和`catch()`这样的方法。

一个成功绑定的主机名和端口现在允许我们通过调用服务器对象上的`listen()`方法来接收传入的请求，服务器对象有一个`Stream` ing 接口:

```
// ...
server.listen((HttpRequest request) {
  request.response.write('Hello world');
  request.response.close();
}); 
```

Enter fullscreen mode Exit fullscreen mode

接收请求允许我们写出我们的响应并结束它。下面是它辉煌的完整片段:

```
import 'dart:io';

void main() {
  HttpServer
    .bind("localhost", 8080)
    .then((HttpServer server) {
      server.listen((HttpRequest request) {
        request.response.write("Hello world!");
        request.response.close();
      });
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用异步/等待🔥

这里有一个更漂亮的编写基本服务器的方法:

```
 import 'dart:io';

main() async {
  var server = await HttpServer.bind("localhost", 8080);

  await for (var request in server) {
    request.response.write("Hello world");
    request.response.close();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个演示第一个例子的视频:

[https://www.youtube.com/embed/ko-urshBwU4](https://www.youtube.com/embed/ko-urshBwU4)

希望这是有见地的。

**喜欢、分享并关注我**了解 Dart 的更多内容。

## 快速链接

1.  [HttpServer 类](https://api.dartlang.org/dev/2.0.0-dev.68.0/dart-io/HttpServer-class.html)
2.  **[免费飞镖截屏在 Egghead.io](https://egghead.io/instructors/jermaine-oppong)**
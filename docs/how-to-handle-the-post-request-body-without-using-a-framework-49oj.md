# 不使用框架如何处理 POST 请求体

> 原文：<https://dev.to/graphicbeacon/how-to-handle-the-post-request-body-without-using-a-framework-49oj>

在基于 Node 的类似的[文章](https://itnext.io/how-to-handle-the-post-request-body-in-node-js-without-using-a-framework-cd2038b93190)的积极回应之后，我发现在 Dart 中演示这一点是合适的。长话短说，我发现这样更简单。

这个例子在请求有效负载的`Content-Type`是`application/x-www-form-urlencoded`的情况下工作，这实际上意味着表单数据在发送到服务器时被格式化为查询字符串。

## 1。创建我们的服务器

创建一个`main.dart`文件，并输入下面的代码片段:

```
import 'dart:io';

void main() async {
  var server = await HttpServer.bind('localhost', 9000);

  await for (HttpRequest req in server) {
    req.response
      ..headers.set('Content-Type', 'text/html')
      ..write('''
        <!doctype html>
        <html>
        <body>
          <form action="/" method="post">
            <input type="text" name="fname" /><br />
            <input type="number" name="age" /><br />
            <input type="file" name="photo" /><br />
            <button>Save</button>
          </form>
        </body>
        </html>
      ''')
      ..close();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将引导我们的服务器，并在向`http://localhost:9000`发出请求时用一个表单进行响应。该代码片段从导入`dart:io`库开始，因为它包含了创建服务器所需的类。整个引导过程发生在一个`main()`函数中，运行我们的 Dart 应用程序需要这个函数。

要运行该文件，请在终端中键入以下命令:

```
dart main.dart 
```

Enter fullscreen mode Exit fullscreen mode

您应该会在浏览器中看到一个表单:

[![localhost](img/a1d7ed4302ba2d566db0a4eaa29c6f5a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B9hcZur1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ba9z5p3rjxluez15m9se.png)

## 2。捕获发布的有效负载

现在让我们确保我们正在处理一个 POST 请求:

```
...
...
await for (HttpRequest req in server) {
  if (req.method == 'POST') {
   // deal with the payload 
  } else {
    req.response
      ..headers.set('Content-Type', 'text/html')
      ..write('''
        <!doctype html>
        <html>
        <body>
          <form action="/" method="post">
            <input type="text" name="fname" /><br />
            <input type="number" name="age" /><br />
            <input type="file" name="photo" /><br />
            <button>Save</button>
          </form>
        </body>
        </html>
      ''')
      ..close();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对服务器的请求被视为一个`Stream`，这意味着我们可以使用请求的`transform`方法来解码内容。

```
if (req.method == 'POST') {
  var content = await req.transform().join();
} ... 
```

Enter fullscreen mode Exit fullscreen mode

这不会马上生效，因为`transform`方法需要一个`StreamTransformer`。一个`StreamTransformer`包含一个`bind`方法，允许它以某种方式操纵流数据。我们需要一个来将我们的流请求数据转换成可读的格式，然后使用`join`方法来组合我们的*转换后的*块。

在我们的`dart:io`导入下面，我们需要`dart:convert`库:

```
import 'dart:io';
import 'dart:convert'; 
```

Enter fullscreen mode Exit fullscreen mode

并使用`Utf8Decoder`作为我们的变压器:

```
var content = await req.transform(Utf8Decoder()).join(); 
```

Enter fullscreen mode Exit fullscreen mode

如果您在表格中填写了相关的详细信息，打印出`content`将会得到以下结果:

```
fname=John&age=30&photo=file.jpg 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们仍然需要从查询字符串中提取信息的键/值对。幸运的是，我们在核心 Dart SDK 中有一个`Uri`类:

```
var content = await req.transform(Utf8Decoder()).join();
var queryParams = Uri(query: content).queryParameters; 
```

Enter fullscreen mode Exit fullscreen mode

## 3。回复帖子

现在让我们发回一个响应:

```
var content = await req.transform(Utf8Decoder()).join();
var queryParams = Uri(query: content).queryParameters;

req.response
  ..write('Parsed data belonging to ${queryParams['fname']}')
  ..close(); 
```

Enter fullscreen mode Exit fullscreen mode

**更新 20/10/2018**:Tobe osak we([天使](https://github.com/angel-dart/angel) 的*创造者)很有帮助的指出了使用`splitQueryString`静态方法提取查询参数:* 

```
// var queryParams = Uri(query: content).queryParameters;
var queryParams = Uri.splitQueryString(content); 
```

Enter fullscreen mode Exit fullscreen mode

我们的查询参数作为一个`Map`对象返回，允许我们像这样提取我们的值:

```
queryParams['fname'];
queryParams['age'];
queryParams['photo']; 
```

Enter fullscreen mode Exit fullscreen mode

[![Solution](img/df5d994e65212b42e3c72ef81b60d9b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eTrtsRzz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bml5w6pdx1oj7tuyp2p7.png)

下面是完整的解决方案:

```
import 'dart:io';
import 'dart:convert';

void main() async {
  var server = await HttpServer.bind('127.0.0.1', 9000);

  await for (HttpRequest req in server) {
    if (req.method == 'POST' && req.headers.contentType.toString() == 'application/x-www-form-urlencoded') {
      var content = await req.transform(Utf8Decoder()).join();
      var queryParams = Uri(query: content).queryParameters;
      req.response
        ..write('Parsed data belonging to ${queryParams['fname']}')
        ..close();
    } else {
      req.response
        ..headers.set('Content-Type', 'text/html')
        ..write('''
          <!doctype html>
          <html>
          <body>
            <form action="/" method="post">
              <input type="text" name="fname" /><br />
              <input type="number" name="age" /><br />
              <input type="file" name="photo" /><br />
              <button>Save</button>
            </form>
          </body>
          </html>
        ''')
        ..close();
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在关闭...

与相关文章一样，这适用于`application/x-www-form-urlencoded`内容类型，如果解析其他内容类型，则需要不同的方法。我们将在以后的文章中讨论这个问题。

**喜欢，分享，关注我**😍有关 Dart 的更多内容。

* * *

## 进一步阅读

1.  [Dart 中的 Hello world 服务器](https://www.dartlang.org/tutorials/dart-vm/httpserver#run-the-hello-world-server)
2.  [在 Dart 中创建流](https://www.dartlang.org/articles/libraries/creating-streams)
3.  [**免费飞镖截屏在 Egghead.io**](https://egghead.io/instructors/jermaine-oppong)
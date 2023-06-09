# 快速提示:如何发出 HTTP 请求(Dart)

> 原文：<https://dev.to/graphicbeacon/quick-tip-how-to-make-http-requests-dart-56dd>

构建从其他 web 应用程序获取数据的 web 应用程序是很常见的，这是由对第三方服务执行 HTTP 调用的逻辑启动的。

在今天的快速技巧中，我们将看看如何使用内置的`HttpClient`和`HttpRequest`类对外部服务进行 HTTP 调用。这些类分别在服务器和客户机上使用。

* * *

## [服务器上的](#on-the-server)

Dart 在 **dart:io** 库中提供了用于进行 HTTP 调用的`HttpClient`类。下面是它的一个例子:

```
import 'dart:io';
import 'dart:convert';

void main() {
  HttpClient()
    .getUrl(Uri.parse('https://swapi.co/api/people/1')) // produces a request object
    .then((request) => request.close()) // sends the request
    .then((response) => response.transform(Utf8Decoder()).listen(print)); // transforms and prints the response
} 
```

Enter fullscreen mode Exit fullscreen mode

运行这个文件将会得到下面的 JSON 响应:

```
{"name":"Luke Skywalker","height":"172","mass":"77","hair_color":"blond","skin_color":"fair","eye_color":"blue","birth_year":"19BBY","gender":"male","homeworld":"https://swapi.co/api/planets/1/","films":["https://swapi.co/api/films/2/","https://swapi.co/api/films/6/","https://swapi.co/api/films/3/","https://swapi.co/api/films/1/","https://swapi.co/api/films/7/"],"species":["https://swapi.co/api/species/1/"],"vehicles":["https://swapi.co/api/vehicles/14/","https://swapi.co/api/vehicles/30/"],"starships":["https://swapi.co/api/starships/12/","https://swapi.co/api/starships/22/"],"created":"2014-12-09T13:50:51.644000Z","edited":"2014-12-20T21:17:56.891000Z","url":"https://swapi.co/api/people/1/"} 
```

Enter fullscreen mode Exit fullscreen mode

还有更漂亮的**异步/等待**版本:

```
import 'dart:io';
import 'dart:convert';

void main() async {
  var request = await HttpClient().getUrl(Uri.parse('https://swapi.co/api/people/1')); // produces a request object
  var response = await request.close(); // sends the request

  // transforms and prints the response
  await for (var contents in response.transform(Utf8Decoder())) {
    print(contents);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我之前做的一个视频:

[https://www.youtube.com/embed/Sz2JbmRKxY8](https://www.youtube.com/embed/Sz2JbmRKxY8)

## [客户端上的](#on-the-client)

Dart 在 **dart:html** 库:
中提供了`HttpRequest`类

```
import 'dart:html';

void main() {
  HttpRequest.getString('https://swapi.co/api/people/1')
    .then(print);
} 
```

Enter fullscreen mode Exit fullscreen mode

与**异步/等待** :

```
import 'dart:html';

void main() async {
  print(await HttpRequest.getString('https://swapi.co/api/people/1'));
} 
```

Enter fullscreen mode Exit fullscreen mode

在[的镖靶](https://dartpad.dartlang.org)上试试这个片段。

## 更新时间:2018-06-10

我的一个媒体读者想知道如何在客户端上发布请求。觉得在这里得到我的答案也很有用:

您可以使用`postFormData`静态方法:
发出 POST 请求

```
var data = { 'firstName' : 'John', 'lastName' : 'Doe' };
HttpRequest.postFormData('/send', data).then((HttpRequest resp) {
  // Do something with the response.
}); 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下,`Content-Type`头将被设置为`Content-Type: application/x-www-form-urlencoded; charset=UTF-8`,除非您希望有效负载被视为一个 JSON 对象:

```
import 'dart:html';
import 'dart:convert';

void main() {
  var data = { 'title' : 'My first post' };
  HttpRequest.request(
    'https://jsonplaceholder.typicode.com/posts',
    method: 'POST',
    sendData: json.encode(data),
    requestHeaders: {
      'Content-Type': 'application/json; charset=UTF-8'
    }
  )
  .then((resp) {
    print(resp.responseUrl);
    print(resp.responseText);           
  });
}

// Response
// https://jsonplaceholder.typicode.com/posts
// { "title": "My first post", "id": "101" } 
```

Enter fullscreen mode Exit fullscreen mode

在[的镖靶](https://dartpad.dartlang.org)上试试这个片段。

为了给客户端和服务器端的请求提供一个统一的解决方案，Dart 团队创建了优秀的 [HTTP 包](https://pub.dartlang.org/packages/http)，现在可以在 Pub 上获得。

* * *

今天的快速提示就到这里。查看下面的文档链接，了解如何执行其他类型的请求。

**喜欢、分享并关注我**了解 Dart 的更多内容。

## 了解更多

1.  [HttpClient 类](https://api.dartlang.org/stable/2.0.0/dart-io/HttpClient-class.html)
2.  [HttpRequest 类](https://api.dartlang.org/stable/2.0.0/dart-html/HttpRequest-class.html)
3.  [动态获取数据-客户端](https://webdev.dartlang.org/tutorials/get-data/fetch-data)

***最后一个**...我想感谢大家对我的“Darticles”的积极反馈*🤓。*我得到了越来越多的支持* ⭐ *这真的意味着很多，我很高兴看到 Dart 社区的成长，因为有了像* [Flutter](https://flutter.io) *和* [Aqueduct](https://aqueduct.io) 这样的创新。
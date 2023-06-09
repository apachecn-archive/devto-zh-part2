# 在 Dart 2 中构建一个聊天应用程序(第 1 部分)

> 原文：<https://dev.to/graphicbeacon/build-a-basic-chat-application-in-dart-2-part-1-4ekj>

实时体验主宰了今天的网络。如果操作正确，这将产生响应迅速、流畅的界面，有助于获得良好的用户体验。

在这个由三部分组成的系列中，我们将通过构建必备的聊天应用程序来实现实时功能。我们将与各种各样的库一起工作，这些库带有用于实现这一点的 [Dart](https://dartlang.org) SDK 和用于 UI 风格的[布尔玛 CSS 框架](https://bulma.io)。

在这一部分的最后，我们将有我们聊天应用的第一个 UI 视图。任何访问该应用程序的用户都必须在进入聊天室之前在登录视图中输入自己的名字。

下图详细描述了整个流程:

[![Diagram of chat flow](img/cf822f44196a79f3dcab32ed3be1e671.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PYzJqyQh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l1s6n4fkcpur4hy7xwe2.png)

聊天流程是这样的:

1.  访问者输入他们的名字并点击**加入聊天**按钮。这将用户名通过`POST`请求发送到我们的后端。
2.  后端接收并检查用户名，如果有效，返回一个`200 OK`响应，否则返回一个`400 Bad Request`。
3.  在`200 OK`屏幕将被切换到聊天室。
4.  从这里开始，将有一个 WebSocket 连接监听从输入字段发送的消息，并广播给聊天中的其他用户。
5.  点击**离开聊天**按钮将关闭 WebSocket 连接并返回用户名登录屏幕。

我们开始吧，好吗？

* * *

## 1。设置您的项目

安装 [Stagehand](https://pub.dartlang.org/packages/stagehand) 包并创建一个 web 项目:

```
$ pub global activate stagehand 
```

Enter fullscreen mode Exit fullscreen mode

并创建一个 web 项目:

```
$ mkdir chat_app && cd chat_app
$ stagehand web-simple # scaffold a web project
$ pub get # install dependencies in `pubspec.yaml` file 
```

Enter fullscreen mode Exit fullscreen mode

安装 [webdev](https://pub.dartlang.org/packages/webdev) 工具来启动我们的服务器进行本地开发:

```
$ pub global activate webdev
$ webdev serve --live-reload # live reload is supported in Chrome 
```

Enter fullscreen mode Exit fullscreen mode

访问`http://localhost:8080`将向您显示此屏幕:

[![Dart web on localhost](img/09da9bdf1664668a87b215f7447f382f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wF4bdCOz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d2cwofqlhki4hm8o8o8b.png)

## 2。标记我们的 HTML 页面

我们将使用布尔玛的 UI 样式类来构建我们的屏幕。

在`web/index.html`中，在`<link rel="stylesheet" href="styles.css">`前的`<head>`中添加下面的链接标签，导入最新的缩小样式和字体 Awesome 图标字体依赖:

```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.1/css/bulma.min.css" integrity="sha256-zIG416V1ynj3Wgju/scU80KAEWOsO5rRLfVyRDuOv7Q=" crossorigin="anonymous" />
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous" /> 
```

Enter fullscreen mode Exit fullscreen mode

在`web/styles.css`中，将文件内容替换为以下内容:

```
html,
body {
  background-color: #f5f5f5;
} 
```

Enter fullscreen mode Exit fullscreen mode

在`web/index.html`中，将`<div id="output"></div>`替换为我们屏幕的标记:

```
<section class="section">
  <div class="container">

    <div id="ChatSignin">
      <h1 class="title">Chatter 🎯</h1>
      <div class="columns">
        <div class="column is-6">
          <div class="field">
            <label class="label">Please enter your name</label>
            <div class="control is-expanded has-icons-left">
              <input class="input is-medium" type="text" placeholder="Enter your name and hit ENTER" />
              <span class="icon is-medium is-left">
                <i class="fas fa-user"></i>
              </span>
            </div>
            <p class="help is-danger"></p>
          </div>
          <div class="field">
            <div class="control">
              <button class="button is-medium is-primary">
                Join chat
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div id="ChatRoom" hidden>
      <h1 class="title">Success!</h1>
    </div>

  </div>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

标记包含登录和聊天室屏幕的视图，在`<div id="ChatSignin">...</div>`和`<div id="ChatRoom" hidden>...</div>`中可以看到。`#ChatRoom`有一个`hidden`属性隐藏它，直到我们稍后验证用户名时删除它。

为了防止错误，从`web/main.dart` :
中的`main()`函数中移除实现

```
import 'dart:html';

void main() {
  // delete the line below
  // querySelector('#output').text = 'Your Dart app is running.';
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在应该会看到下面的输出:

[![Chatter sign in UI](img/484e742cb7f301ec471a18552d9e3959.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XvIGz480--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vgzc4i81sv7hkibzgk8c.png)

## 3。实现登录流程的浏览器逻辑

让我们添加接收用户名并发送到后端的逻辑。

在`web/main.dart`中，我们将从下面的内容开始:

```
import 'dart:html';

void main() {
  // Selectors
  var chatSigninBox = querySelector('#ChatSignin');
  var chatRoomBox = querySelector('#ChatRoom');
  var validationBox = chatSigninBox.querySelector('p.help');
  InputElement nameField = chatSigninBox.querySelector('input[type="text"]');
  ButtonElement submitBtn = chatSigninBox.querySelector('button');

  // Event listeners
  nameField.addEventListener('input', (evt) {
    // TODO: Run field validation
  });

  submitBtn.addEventListener('click', (evt) async { // using async/await ;)
    // TODO: Run name field validation
    // TODO: Submit name field to backend
    // TODO: Handle success response
    // TODO: Handle failure responses
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

该代码片段以我们的`dart:html`导入开始，它允许访问`window`和`document`对象，包括它们的方法。

在上面的代码片段中，我们使用`querySelector()`顶级函数来选择我们的 DOM 元素并监听它们上的各种事件。

* * *

### 我对定义选择器的一个建议...

**尽可能指定类型！**换句话说，不是使用`var`关键字，而是替换为预期的对象类型，所以:

```
InputField nameField = chatSigninBox.querySelector('input[type="text"]'); 
```

Enter fullscreen mode Exit fullscreen mode

这是因为`querySelector()`方法返回一个类型为`Element`的对象，这意味着特定 DOM 元素上的特定属性对于 Dart analyser( *应该是*)是不可见的，就像`nameField`上的`value`一样，因为它不存在于`Element`下。将`InputElement`作为子类型允许分析器找到`value`属性，并防止当您试图访问`Element` :
下的`value`时抛出错误

```
// Bad
var nameField = chatSigninBox.querySelector('input[type="text"]');
print(nameField.value); // Analyser will throw an error

// Good
InputElement nameField = chatSigninBox.querySelector('input[type="text"]');
print(nameField.value); // Analyser sees value prop in `InputElement` 
```

Enter fullscreen mode Exit fullscreen mode

太好了！继续前进...

* * *

让我们为用户名字段实现事件监听器:

```
nameField.addEventListener('input', (evt) {
  if (nameField.value.trim().isNotEmpty) {
    nameField.classes
      ..removeWhere((className) => className == 'is-danger')
      ..add('is-success');
    validationBox.text = '';
  } else {
    nameField.classes
      ..removeWhere((className) => className == 'is-success')
      ..add('is-danger');
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将验证该字段，并为成功和失败状态添加适当的类。这些类名(`is-success`和`is-danger`)是由布尔玛提供的。

让我们实现提交按钮的事件监听器:

```
submitBtn.addEventListener('click', (evt) async {
  // 1\. Validate name field
  if (nameField.value.trim().isEmpty) {
    nameField.classes.add('is-danger');
    validationBox.text = 'Please enter your name';
    return;
  }

  submitBtn.disabled = true;

  try {
    // 2\. Submit name to backend via POST
    var response = await HttpRequest.postFormData(
      'http://localhost:9780/signin', // TODO: Endpoint to be created in next step
      {
        'username': nameField.value,
      },
    );

    // 3\. Handle success response and switch view
    chatSigninBox.hidden = true;
    chatRoomBox.hidden = false;
  } catch (e) {
    // 4\. Handle failure response
    submitBtn
      ..disabled = false
      ..text = 'Failed to join chat. Try again?';
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

点击**加入聊天**按钮后会发生以下情况:

1.  我们检查用户名字段中的有效条目。如果无效，我们将显示一条错误消息并停止进一步执行。如果有效，那么我们转到步骤 2。
2.  我们在`http://localhost:9780/signin`将表单数据发送到端点
3.  成功响应后，我们将隐藏用户界面中的聊天标志，并显示聊天室用户界面
4.  如果后端返回一个错误，我们将替换**加入聊天**按钮的文本，并鼓励用户再试一次

以下是您现在应该拥有的:

[![Chat signin form](img/3f8606cb3277631c29cd5c7221a79df6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5EH3zClS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3b6aa4n61l0yqal35q8k.gif)

提交用户名会显示失败消息，因为我们还没有实现后端。

## 4。实现后端逻辑

我们现在需要后端接收包含用户名的请求。

创建一个`bin/server.dart`文件并创建一个服务器:

```
import 'dart:io';
import 'dart:convert';

main() async {
  // TODO: Get port from environment variable
  var port = 9780;
  var server = await HttpServer.bind(InternetAddress.loopbackIPv4, port);

  print('Server listening on port $port');

  await for (HttpRequest request in server) {
    // TODO: Handle requests
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

了解更多关于在 Dart 中编写 HTTP 服务器和处理 POST 请求的[。](https://dev.to/graphicbeacon/quick-tip-write-an-http-server-in-dart--5c7n)

将`// TODO: Handle requests`行替换为以下内容:

```
// TODO: Only needed in local development. Will be removed in future
request.response.headers.add('Access-Control-Allow-Origin', 'http://localhost:8080');

switch (request.uri.path) {
  case '/signin':
    String payload = await request.transform(Utf8Decoder()).join();
    var username = Uri.splitQueryString(payload)['username'];

    if (username != null && username.isNotEmpty) {
      // TODO: Check username is unique
      request.response
        ..write(username)
        ..close();
    } else {
      request.response
        ..statusCode = 400
        ..write('Please provide a valid user name')
        ..close();
    }
    break;
  case '/ws':
    // TODO: Upgrade request to Websocket connection
    break;
  default:
    // TODO: Forward to static file server
} 
```

Enter fullscreen mode Exit fullscreen mode

这个片段以一个 CORS 头开始，允许来自我们的`webdev`服务器的连接。这仅在当地发展时需要。当构建产品时，我们将重构这一行。

然后我们有一个 switch 语句来根据请求的路径执行适当的逻辑。现在相关的领域就是`/signin`的情况。它验证用户名并发送正确的响应。

用下面的命令运行这个文件:

```
$ dart bin/server.dart
  Server listening on port 9780 # You should see this message 
```

Enter fullscreen mode Exit fullscreen mode

返回`http://localhost:8080`并重试:

[![Successful username sign in](img/f070c5ed71ed77c292b7c1d294ee03bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lOnibLxh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yosso0vsncnlzpaz8t97.gif)

## 结论

本系列的第 1 部分到此结束。一如既往，我希望这是有见地的，你今天学到了一些新的东西。而工作方案就是这里的[这里的](https://github.com/graphicbeacon/dart-bulma-chat-app)。

你走之前还有一件事...**订阅[我的 Youtube 频道](https://www.youtube.com/channel/UCHSRZk4k6e-hqIXBBM4b2iA?view_as=subscriber)获取 Dart** 上的最新视频。谢谢！

**喜欢，[分享](https://twitter.com/home?status=%22How%20to%20build%20a%20basic%20chat%20application%20in%20Dart%20(Part%201)%22%20https%3A//dev.to/graphicbeacon/build-a-basic-chat-application-in-dart-2-part-1-4ekj%20%20%23dartlang%20%23bulmacss%20%23DEVCommunity)关注我**😍有关 Dart 的更多内容。

## 继续阅读:

[![graphicbeacon image](img/ebd69e31cdafde0c3cc551828feae27a.png)](/graphicbeacon) [## 在 Dart 2 中构建一个聊天应用程序(第 2 部分)

### 杰梅因奥彭 11 月 8 日 187 分钟阅读

#dart #http #beginners #showdev](/graphicbeacon/build-a-chat-application-in-dart-2-part-2-48df)

## 进一步阅读

1.  [dart:html 库](https://api.dartlang.org/stable/2.0.0/dart-html/dart-html-library.html)
2.  [如何在您的 Dart 应用程序中使用 JavaScript 库](https://dev.to/graphicbeacon/how-to-use-javascript-libraries-in-your-dart-applications--4mc6)
3.  [**免费飞镖截屏在 Egghead.io**](https://egghead.io/instructors/jermaine-oppong)
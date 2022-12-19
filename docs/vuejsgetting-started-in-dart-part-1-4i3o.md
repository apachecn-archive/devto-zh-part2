# vue . js–Dart 入门(第 1 部分)

> 原文：<https://dev.to/graphicbeacon/vuejsgetting-started-in-dart-part-1-4i3o>

我对 Vue.js 最近做的标记印象深刻。在过去使用过它之后，我想再次使用它，我很好奇在 Dart 中使用 Vue 是什么样子。

已经演示了在 Dart web apps 中[使用 JavaScript 库的可能性，我们将浏览 Vue.js " **Getting started** "页面，并使用 **`js`** interop 包重写 Dart 中的示例。](https://dev.to/graphicbeacon/how-to-use-javascript-libraries-in-your-dart-applications--4mc6)

[https://www.youtube.com/embed/zoN1_5tYzOM](https://www.youtube.com/embed/zoN1_5tYzOM)
→ [**在 Youtube 上观看**](https://youtu.be/zoN1_5tYzOM)

* * *

# 在我们开始之前:

## 1。设置您的项目

使用 Stagehand 快速设置您的 web 项目:

```
$ mkdir vue_dart && cd vue_dart
$ stagehand web-simple 
```

Enter fullscreen mode Exit fullscreen mode

## 2。安装 js 互操作包

确保`js`依赖项被添加到您的`pubspec.yaml`文件:

```
dependencies:
  js: ^0.6.1+1 
```

Enter fullscreen mode Exit fullscreen mode

保存并运行`pub get`以更新您的依赖关系。

## 3。导入 Vue.js 库

在`web/index.html`中的`<head>`之前`<script defer src="main.dart.js"></script>`导入 dev 版本的库:

```
<!-- development version, includes helpful console warnings -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以浏览示例了！

* * *

# 入门

用我们的库声明和导入:
创建一个`web/app.dart`文件

```
@JS()
library vue_interop;

import 'package:js/js.dart';

// TODO: The rest of the code to go here 
```

Enter fullscreen mode Exit fullscreen mode

## 陈述性渲染

下面是第一个带有`message`属性占位符的模板示例:

```
<div id="app">
  {{ message }}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

为`Vue` :
创建带注释的工厂构造函数

```
@JS()
class Vue {
  external factory Vue(VueOptions options);
} 
```

Enter fullscreen mode Exit fullscreen mode

当声明一个新的`Vue`实例时，JavaScript api 接受一个对象文字。注意声明的是`VueOptions`类型而不是`Map`？我们不能在这里使用 Dart `Map` s，因为它们在 JavaScript 中是不透明的。

因此，我们需要创建一个工厂构造函数来存放我们的选项:

```
@JS()
@anonymous
class VueOptions {
  external factory VueOptions({ String el, VueDataOptions data });
  external String get el;
  external VueDataOptions get data;
} 
```

Enter fullscreen mode Exit fullscreen mode

数据道具是一个`VueDataOptions`对象:

```
@JS()
@anonymous
class VueDataOptions {
  external factory VueDataOptions({
    String message = '', // Set to empty string as default
  });
  external String get message;
} 
```

Enter fullscreen mode Exit fullscreen mode

返回到`web/main.dart`，让我们使用这些工厂:

```
// Relative imports
import './app.dart';

void main() {
  Vue(VueOptions(
    el: '#app',
    data: VueDataOptions(
      message: 'Hello Vue!',
    ),
  ));
} 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该会看到文本“Hello Vue！”在屏幕上:

除了字符串插值，我们还可以绑定元素属性。

```
<div id="app-2">
  <span v-bind:title="message">
    Hover your mouse over me for a few seconds
    to see my dynamically bound title!
  </span>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们工厂不需要改变，只需要声明一个调用:

```
// web/main.dart
...
void main() {
  ...

  // App 2 example
  Vue(VueOptions(
    el: '#app-2',
    data: VueDataOptions(
      message: 'You loaded this page on ${DateTime(2018).toLocal()}',
    ),
  ));
} 
```

Enter fullscreen mode Exit fullscreen mode

## 条件句

使用`v-if`属性来切换元素的存在:

```
<div id="app-3">
  <span v-if="seen">Now you see me</span>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

因为我们正在观察一个新的属性(`seen`)，所以让我们在工厂中为此添加一个 getter:

```
@JS()
@anonymous
class VueDataOptions {
  external factory VueDataOptions({
    String message = '',
    bool seen = null, // <-- Added this
  });
  external String get message;
  external bool get seen; // <-- Added this
} 
```

Enter fullscreen mode Exit fullscreen mode

并且在`web/main.dart` :

```
...
void main() {
  ...
  // App 3 example
  var app3 = Vue(VueOptions(
    el: '#app-3',
    data: VueDataOptions(seen: true),
  ));
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们将调用`Vue()`的结果赋给了一个`app3`变量。文档演示了如何做`app3.seen = false`，这意味着我们必须向`web/app.dart` :
中的`Vue`类添加一个布尔类型的 getter

```
@JS()
class Vue {
  external factory Vue(VueOptions options);
  external void set seen(bool val); // <-- Added this
} 
```

Enter fullscreen mode Exit fullscreen mode

在`web/main.dart`中，我们将做:

```
import 'dart:async'; // <-- Added this line to use `Future.delayed`

// Relative imports
import './todo.dart'; // <-- Added this line
import './app.dart';

void main() {
  ...
  ...

  // App 3 example
  var app3 = Vue(VueOptions(
    el: '#app-3',
    data: VueDataOptions(seen: true),
  ));

  // Added a delay to see disappearing text
  Future.delayed(Duration(seconds: 2), () async {
    app3.seen = false;

    // Added a delay and then restored text visibility
    await Future.delayed(Duration(seconds: 2));
    app3.seen = true;
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

## 循环往复

在对数组
进行迭代时使用`v:for`属性

```
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这引入了一个新的工厂构造函数，我们称之为`Todo`。

用我们的工厂类
创建`web/todo.dart`

```
@JS()
library todo;

import 'package:js/js.dart';

@JS()
@anonymous
class Todo {
  external factory Todo({String text});
  external String get text;
} 
```

Enter fullscreen mode Exit fullscreen mode

在`web/app.dart`中，让我们定义一个`Todo`的列表:

```
@JS()
class Vue {
  external factory Vue(VueOptions options);
  external void set seen(bool val);
  external List<Todo> get todos; // <-- Added this line
}

...

@JS()
@anonymous
class VueDataOptions {
  external factory VueDataOptions({
    String message = '',
    bool seen = null,
    List<Todo> todos = const [],
  });
  external String get message;
  external bool get seen;
  external List<Todo> get todos; // <-- Added this line
} 
```

Enter fullscreen mode Exit fullscreen mode

在`web/main.dart`中，我们将使用它:

```
...
...
void main() {
  ...
  ...

  // App 4 example
  var app4 = Vue(VueOptions(
    el: '#app-4',
    data: VueDataOptions(todos: [
      Todo(text: 'Learn Dart'),
      Todo(text: 'Learn Aqueduct'),
      Todo(text: 'Build something awesome!'),
    ]),
  ));
} 
```

Enter fullscreen mode Exit fullscreen mode

为了向待办事项列表添加一个新项目，如文档所示:

```
app4.todos.push({ text: 'New item' }); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要在`Vue` :
上为`todos`添加一个 getter

```
// web/app.dart
...
...

@JS()
class Vue {
  external factory Vue(VueOptions options);
  external void set seen(bool val);
  external List<Todo> get todos; // <-- Added this line
} 
```

Enter fullscreen mode Exit fullscreen mode

并且在`web/main.dart` :

```
...
...
  // App 4 example
  var app4 = Vue(VueOptions(
    el: '#app-4',
    data: VueDataOptions(todos: [
      Todo(text: 'Learn Dart'),
      Todo(text: 'Learn Aqueduct'),
      Todo(text: 'Build something awesome!'),
    ]),
  ));

  app4.todos.add(Todo(text: 'New item')); // <-- Added this line 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这就把我们带到了第 1 部分的结尾。在第 2 部分中，我们将研究如何用 Vue 组件处理用户输入和组合。

一如既往，我希望这是有见地的，你今天学到了一些新的东西。

**订阅[我的 YouTube 频道](https://www.youtube.com/channel/UCHSRZk4k6e-hqIXBBM4b2iA?view_as=subscriber)获取 Dart** 上的最新视频。谢谢！

**喜欢，分享，关注我**😍有关 Dart 的更多内容。

## 进一步阅读

1.  [js 包](https://pub.dartlang.org/packages/js)
2.  [如何在您的 Dart 应用程序中使用 JavaScript 库](https://dev.to/graphicbeacon/how-to-use-javascript-libraries-in-your-dart-applications--4mc6)
3.  [**用 Dart 进行全栈 web 开发**](http://bit.ly/2P2N1jC)
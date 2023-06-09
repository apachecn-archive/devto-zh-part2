# 如何在 Dart 应用程序中使用 JavaScript 库

> 原文：<https://dev.to/graphicbeacon/how-to-use-javascript-libraries-in-your-dart-applications--4mc6>

你有没有经历过害怕把你最喜欢的 JS 库留在身后的恐惧？我们再也不用担心了，因为 Dart 团队已经提供了一种整合您最喜欢的 JavaScript 库的方法！

解决方案以 [**js**](https://pub.dartlang.org/packages/js) 包的形式出现，它提供了一个调用 JavaScript API 方法的代理。在本文中，我们将探索一些使用`window`对象的例子，以及一个使用 jQuery 的真实例子。

这里是包含解决方案的[源代码](https://gist.github.com/graphicbeacon/0a23381809fcddaffd553c9ad845b96f)。

## 先决条件

在我们开始之前，让我们用 [stagehand](https://pub.dartlang.org/packages/stagehand) 来搭建我们的网络项目:

```
stagehand web-simple 
```

Enter fullscreen mode Exit fullscreen mode

在 pubspec.yaml 的 dependencies 下添加以下内容:

```
dependencies:
  js: ^0.6.0 
```

Enter fullscreen mode Exit fullscreen mode

并运行`pub get`。还要确保你已经安装了**网站开发**(`pub global activate webdev`)

现在我们已经准备好进入示例了！

* * *

## 与`window`宾语的例子

安装 **js** 包公开了`@JS()`注释，作为访问`window`主机对象上的 JavaScript APIs 的一种方式。

下面是在`web/main.dart`中调用`window.console.log()`的一个片段:

```
@JS() // Sets the context, which in this case is `window`
library main; // required library declaration called main, or whatever name you wish

import 'package:js/js.dart'; // Pull in our dependency

@JS('console.log') // This marks the annotated function as a call to `console.log`
external void log(dynamic str);

void main() {
  log('Hello world!'); // calling console.log() in JavaScript land
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`webdev serve`并访问 localhost url 以查看输出。要查看更新，只需保存文件并重新加载页面！

带注释的文件必须以一个也有`@JS()`注释的库声明开始，我们在第 1–2 行可以看到。因为第一个注释没有参数，所以它设置了其他注释相对于`window`对象的上下文。所以到了这一行`@JS('console.log')`详细描述了从`window`到具有`log`方法的`console`属性的遍历。

下面是另一个将上下文设置为`window.console` :
的例子

```
@JS('console') // Our `console` namespace
library main;

import 'package:js/js.dart';

@JS('log') // Setting the proxy to the `console.log` method
external void log(dynamic str);

void main() {
  log('Hello worlddd!');
} 
```

Enter fullscreen mode Exit fullscreen mode

因为文件以名称空间`console`开始，所以`log`方法的下一个注释排除了前缀`console`。`log`方法的`external`关键字用于在 Dart 之外标记该方法，否则应该是一个函数体。此外，因为我们的函数与`console`上的方法同名，所以我们可以完全删除上面的注释。

```
// @JS('log') // remove
external void log(dynamic str); 
```

Enter fullscreen mode Exit fullscreen mode

***请注意:如果您只需要访问`window`上的内置属性，则不需要这个互操作包。使用 Dart 的`dart:html`库来做这件事。上面的代码片段只是为了举例说明，因此`js`包在使用外部库时发挥作用。***

## 用 jQuery 的真实例子

为了使用 jQuery，让我们在脚本标签请求`main.dart.js` :
之前的`web/index.html`中导入它

```
<script defer src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

现在，创建一个名为`web/jquery.dart`的文件，包含下面的代码片段:

```
@JS()
library jquery;

import 'package:js/js.dart';

// new jQuery() invokes JavaScript `new jQuery()`
@JS()
class jQuery {
  external factory jQuery(String selector);
  external int get length; // We get this from the jQuery instance
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们重构`main.dart`来测试这个:

```
import './jquery.dart';

void main() {
  print(jQuery('#output')); // [object Object]
  print(jQuery('#output').length); // 1
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们通过使用`css()`和`animate()`方法来做一些更有趣的事情:

```
@JS()
class jQuery {
  external factory jQuery(String selector);
  external int get length;
  external jQuery css(Map options);
  external jQuery animate(Map options);
} 
```

Enter fullscreen mode Exit fullscreen mode

调用这两个方法将返回 jQuery 实例，就像基于 JS 的 API 一样。

现在这不会像预期的那样工作，因为`options`参数需要一个`Map`类型。我们不能传递 Dart `Map`对象，因为它们在 JavaScript 中是“不透明”的。换句话说，你将得到一个不包含你期望它包含的内容的对象。

为了让它工作，我们需要用我们需要的键来定义一个工厂构造函数:

```
@JS()
@anonymous // This annotation is needed along with the unnamed factory constructor
class CssOptions {
  external factory CssOptions({ backgroundColor, height, position, width });
  external String get backgroundColor;
  external String get position;
  external num get height;
  external num get width;
} 
```

Enter fullscreen mode Exit fullscreen mode

并将`css()`外部方法声明修改如下:

```
external jQuery css(CssOptions options); 
```

Enter fullscreen mode Exit fullscreen mode

让我们对 animate 方法做同样的事情:

```
@JS()
@anonymous
class AnimateOptions {
  external factory AnimateOptions({left, top});
  external dynamic get left;
  external dynamic get top;
} 
```

Enter fullscreen mode Exit fullscreen mode

并将`animate()`外部方法声明修改如下:

```
external jQuery animate(AnimateOptions options); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像这样调用`web/main.dart`中的方法:

```
import './jquery.dart';

void main() {
  jQuery('#output')
      .css(CssOptions(
          backgroundColor: 'green',
          height: 100,
          position: 'relative',
          width: 100))
      .animate(AnimateOptions(left: 100, top: 100));
} 
```

Enter fullscreen mode Exit fullscreen mode

并期待下面的结果:

[![Recording of Dart+jQuery interop example](img/2069e1b428acd50a856532954a28674b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2SJji_le--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/351by2eur2g4jsmse2d3.gif)

## 结论

知道您可以作为 Dart 开发人员保持生产力，同时保持对 JavaScript 库生态系统的访问，事情会变得更好，因为任何新出现的库都在您的掌握之中。

这个解决方案适用于任何在`window`对象下有名称空间的 JavaScript 库，涵盖了 *99%的情况*。

一如既往，我希望这是有见地的，你今天学到了一些新的东西。这里是包含完整解决方案的要点。

以下是 Vue.js 的扩展示例:

[![graphicbeacon image](img/ebd69e31cdafde0c3cc551828feae27a.png)](/graphicbeacon) [## vue . js–Dart 入门(第 1 部分)

### 杰梅因奥彭 11 月 5 日 185 分钟阅读

#dart #javascript #showdev #vue](/graphicbeacon/vuejsgetting-started-in-dart-part-1-4i3o)

## 进一步阅读

1.  [**js 包**上 Pub](https://pub.dartlang.org/packages/js)
2.  [egghead . io 上的免费飞镖截屏](https://egghead.io/instructors/jermaine-oppong)

* * *

## 分享是关爱🤗

如果你喜欢读这篇文章，请通过各种社交渠道分享。此外，检查并 [**订阅我的 YouTube 频道**](https://youtube.com/c/CreativeBracket) (也点击铃铛图标)上的 Dart 视频。

[**订阅我的电子邮件简讯**](http://eepurl.com/gipQBX) 下载我的免费 35 页【Dart 入门电子书，并在新内容发布时得到通知。

**喜欢、分享和[关注我](https://twitter.com/creativ_bracket)T3】😍有关 Dart 的更多内容。**
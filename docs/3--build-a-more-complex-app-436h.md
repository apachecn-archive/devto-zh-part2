# #3 —构建更复杂的应用程序

> 原文：<https://dev.to/nitya/3--build-a-more-complex-app-436h>

这是我为 2018 年 FlutterCamp 整理的一系列文章。更多信息请阅读 [*系列第一篇*](https://dev.to/devfestnyc/flutter-camp-2018-2c1k-temp-slug-4314172) *。*

呜！此时，您已经有了您的[开发环境设置](https://dev.to/devfestnyc/2--getting-started-462h-temp-slug-2419028)——您通过在真实或虚拟设备上创建并运行您的第一个应用程序来验证它。我们继续吧。

接下来，我们探索[简单的启动命名应用教程](https://flutter.io/get-started/codelab/)来从脚手架构建一个定制应用。我们将学习一些关于 Flutter 应用程序是如何构建的，关于小部件的重要性(和力量)。

*别管* [*镖*](https://dartlang.org) *的语法了。我们将很快对此进行深入研究。现在，如果你有 Java、JavaScript 或 Swift 的经验，你可以立即搜索代码，了解开发模式。这就是我们在现阶段想要实现的全部目标。*

### 第一步:目标=启动命名应用

这是一个为新创业公司生成潜在名称的应用程序。我们可以利用这一点来学习以下关键概念:

*   ***视图&布局****——我们将在* [中构建一个此图像](https://flutter.io/get-started/codelab/images/startup-namer-app.gif)
*   ***交互事件&处理*** *—用户可以选择/取消选择姓名*
*   ***无限滚动，懒懒加载列表****——在滚动 ups 上一次添加 10 个*
*   ***主题化*** (如何改变 UI 的观感)
*   ***封装*** (并使用它们来扩展核心颤振功能)

*NN >此外，还有一点关于 Flutter 应用程序是如何构建的，窗口小部件的作用，状态管理和有状态热重装。*

### 第二步:起点=脚手架 App

像以前一样，浏览创建一个新的 Flutter 应用程序的基本步骤，但将其命名为 *startup_namer。*打开 lib/main.dart，删除现有代码，并按照第 1 步中的[所述用此代码替换。](https://flutter.io/get-started/codelab/#step-1-create-the-starting-flutter-app) 

```
import 'package:flutter/material.dart';

void **main()** => runApp(new MyApp());

class MyApp extends **StatelessWidget** {
  @override
**Widget build** (BuildContext context) {
    return new **MaterialApp** (
      title: 'Welcome to Flutter',
      home: new Scaffold(
        appBar: new AppBar(
          title: new Text('Welcome to Flutter'),
        ),
        body: new Center(
          child: new Text('Hello World'),
        ),
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这只需要删除-保存-重新加载。我没问题。然而一些观察…

#### 观察:

1.  **函数 vs .类:**注意，同一个文件既包含顶级函数，也包含顶级类。*。*
2.  **Line 1**:` import ' package:flutter/material . dart '将相关的 Dart 包带入你的代码库。在这种情况下，我们正在导入[材料包](https://flutter.io/widgets/material/)，它提供了实现 [Google 的 Flutter 材料设计](https://material.io/design/introduction/)的所有类(视觉、行为和运动丰富的小部件)。
3.  **第 2 行:**` void main()=>runApp(new MyApp())；是一个 Flutter app 的强制入口。它调用 [runApp 函数](https://docs.flutter.io/flutter/widgets/runApp.html)，该函数放大所提供的小部件(这里是 MyApp 的一个实例)并将其附加到屏幕上，同时使用约束条件强制其*填充整个屏幕。*要让自定义小工具不填满屏幕，将其居中或对齐小工具(然后填满它)
4.  **Line:** MyApp 扩展无状态 Widget 显示你的自定义 widget 是无状态的；稍后我们将了解无状态&有状态窗口小部件。
5.  **Flutter 中的一切都是 widget 一切。**样式(对齐、填充等。)、布局、行为(手势、触摸等。)都是 widgets。UI 开发完全是关于小部件的组合。关键是**构建**函数，它描述了如何组装该小部件的子树(从较低级别的小部件)。*要了解 Flutter 如何将一个窗口小部件树转换成屏幕上的像素，* [*观看这个演讲*](https://www.youtube.com/watch?v=UUfXWzp0-DU) *。*
6.  **MaterialApp，脚手架& AppBar。** [MaterialApp](https://docs.flutter.io/flutter/material/MaterialApp-class.html') 是一个有状态的便利小部件，它封装了许多材料设计通常需要的小部件。它还可以与顶级导航器一起正确绘制路线。

### 第 3 步:依赖关系=添加外部包

许多第三方飞镖包存在于[pub.dartlang.org](https://pub.dartlang.org/flutter/)上。按照[步骤 2 的指示](https://flutter.io/get-started/codelab/#step-2-use-an-external-package)添加*英语单词*包，并使用它随机选择一个新的英语单词显示在屏幕消息中。

**步骤:**

1.  在 **pubspec.yaml
    2 中添加 english_words 依赖。**进入该文件后，点击*获取包图标*(右上)获取包
2.  添加导入“package:English _ words/English _ words . dart”；在 main.dart 中
3.  然后使用它例如:final word pair = new word pair . random()；

对我来说，这是一个相当无痛的过程。没问题。要探索的事情:当导入的包有自己的依赖项时会发生什么？

### 步骤 3:有状态小部件=添加新部件

按照步骤 3 的指导完成[来构建第一个有状态的小部件。](https://flutter.io/get-started/codelab/#step-3-add-a-stateful-widget)

NN >这也是一个直截了当的步骤。但是让我们在这里花几分钟谈谈一些有趣的方面。

#### 观察:

1.  您可以在同一个源文件中定义多个类和函数。
2.  StatefulWidget 除了创建其关联的状态实例之外，没有做更多的事情；在这里，是后者承担了创建小部件的重任。
3.  StatelessWidget 和 StatefulWidget *类*都是不可变的；它们的属性一旦创建就不能更改。然而，后者有一个关联的可变状态对象，该对象在小部件的整个生命周期中都存在。
4.  您可以看到如何通过将较低级别的有状态和无状态小部件组合成 UI 来迭代设计和构建 UI。稍后我们将讨论状态管理。

### 第四步:列表视图=无限滚动

完成教程的第 4 步，创建一个用户可以无限滚动的名字列表。

NN >这也是一个直截了当的步骤。但是 ListView、ListView.builder 和助手“_build”函数之间的交互可能有点复杂。

#### 观察:

1.  **私有变量。**在 Dart 中，在变量前面加上下划线(_)会自动使该变量成为私有变量。所以在这里，许多已定义的类变量和函数只在类/实例中可见。
2.  一切都是小部件。widgets 可以由其他 widgets 组成，创建自定义的复杂视图。依此类推，直到你到达最底层(节点),在那里你可能会使用标准的 Flutter 小部件作为核心[。因此，开发 UI 的一种方法是从 build 方法返回一个核心小部件开始，然后重构该方法，将核心小部件包装在另一个提供附加功能的小部件中；例如，这可能是一个容器小部件，它现在允许您返回一个复杂的或托管的布局，而不是单个小部件。](https://flutter.io/widgets/)
3.  [**ListView**](https://docs.flutter.io/flutter/widgets/ListView-class.html) 是一个小部件，表示线性排列的一组可滚动的小部件——当使用 builder (itemBuilder)时，它需要一个上下文和一个单调增加的索引。如果可以创建一个 ListTile 小部件，只需返回该索引的 list tile 小部件即可——因此，如果确保总是为每个索引返回一些内容，就可以创建一个无限滚动的列表。

这里要看和理解的主要事情是，代码看起来比实际更复杂，因为它们都被内联了。稍后，我们可以看到如何通过简单地将代码抽象成私有函数来使代码更清晰易读。

### 第五步:交互性=事件处理

完成教程的[步骤 5。](https://flutter.io/get-started/codelab/#step-5-add-interactivity)

NN >这也是一个直截了当的步骤。这里要理解的关键是使用 setState()来触发状态更改，这反过来会导致使用更新后的状态重新计算和重新渲染渲染树。在这种情况下，这意味着在每次点击后，我们从显示一个透明的心变成一个彩色的心(反之亦然)。

### 第六步:导航=路线处理

完成教程的[步骤 6](https://flutter.io/get-started/codelab/#step-6-navigate-to-a-new-screen)

*NN >这是值得停下来思考的有趣一步，因为这是导航首次进入等式的地方。让我们先来谈谈 Navigator、MaterialApp 和 MaterialPageRoute，然后看看代码。*

**[**导航器**](https://docs.flutter.io/flutter/widgets/Navigator-class.html) **:** 是一个 Flutter 核心部件，它使用[覆盖](https://docs.flutter.io/flutter/widgets/Overlay-class.html)模式将一组子部件作为“堆栈”来管理。覆盖允许一个小部件浮动在另一个之上，以展示相对重要性或提供视觉过渡效果。如果小部件是全屏的，那么 Navigator 通过在覆盖图中上下浮动小部件，有效地创建了从一个屏幕移动到另一个屏幕(页面)的错觉。**

 **这就是处理路由的方式(将小部件映射到路由)。这种行为对于 web 开发人员来说是非常熟悉的，特别是在给定命名路由的情况下, ***命名路由使用 path/a/b/c 结构。***

[**MaterialApp:**](https://docs.flutter.io/flutter/material/MaterialApp-class.html) 你可以为你的应用创建一个导航器，或者使用 MaterialApp 或 WidgetsApp 中的一个(如果他们正在管理你的 widget 树的根)。使用 Navigator.push(context，new MaterialPageRoute(...))按需将新路由推送到堆栈上；MaterialPageRoute 采用一个生成器函数，该函数可以创建要显示的页面(小部件),还可以处理 Navigator.pop(上下文)行为以“返回”到上一个屏幕。

*如果你将 MaterialApp 与 Scaffold 一起使用，它会自动在 AppBar 中添加一个“后退”按钮，在任何上下文中为你弹出导航栈。*

[**路由可以返回值。**这是凉 T3。它类似于 Android 中的 *startActivityFor Result* ，当你触发新的屏幕(Navigator.push)时，你这样做的目的是将用户带到一个新的屏幕，当他是 don (Navigator.pop)时，你期望从这个屏幕返回一个结果。](https://docs.flutter.io/flutter/widgets/Navigator-class.html)

还支持弹出路线(模态部分屏幕覆盖)和自定义路线(控制该路线的过渡动画)。

### 第七步:主题化=改变外观&感觉

完成教程的[步骤 7](https://flutter.io/get-started/codelab/#step-7-change-the-ui-using-themes)

*NN >主题控件看起来&感觉上的应用。更改应用程序的主题就像在顶级小部件(例如 MaterialApp)上配置 ThemeData 类一样简单。*

**练习**:检查一下[主题数据](https://docs.flutter.io/flutter/material/ThemeData-class.html)的功能，并做一些其他的改变，看看如何更严格地定制主题。以下是可以更改的属性列表:

```
[ThemeData](https://docs.flutter.io/flutter/material/ThemeData/ThemeData.html)({

[Brightness](https://docs.flutter.io/flutter/services/Brightness-class.html) brightness, 
[MaterialColor](https://docs.flutter.io/flutter/material/MaterialColor-class.html) primarySwatch, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) primaryColor,
[Brightness](https://docs.flutter.io/flutter/services/Brightness-class.html) primaryColorBrightness, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) primaryColorLight, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) primaryColorDark, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) accentColor, 
[Brightness](https://docs.flutter.io/flutter/services/Brightness-class.html) accentColorBrightness, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) canvasColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) scaffoldBackgroundColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) bottomAppBarColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) cardColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) dividerColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) highlightColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) splashColor, 
[InteractiveInkFeatureFactory](https://docs.flutter.io/flutter/material/InteractiveInkFeatureFactory-class.html) splashFactory, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) selectedRowColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) unselectedWidgetColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) disabledColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) buttonColor, 
[ButtonThemeData](https://docs.flutter.io/flutter/material/ButtonThemeData-class.html) buttonTheme, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) secondaryHeaderColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) textSelectionColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) textSelectionHandleColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) backgroundColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) dialogBackgroundColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) indicatorColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) hintColor, 
[Color](https://docs.flutter.io/flutter/dart-ui/Color-class.html) errorColor, 
[String](https://docs.flutter.io/flutter/dart-core/String-class.html) fontFamily, 
[TextTheme](https://docs.flutter.io/flutter/material/TextTheme-class.html) textTheme, 
[TextTheme](https://docs.flutter.io/flutter/material/TextTheme-class.html) primaryTextTheme, 
[TextTheme](https://docs.flutter.io/flutter/material/TextTheme-class.html) accentTextTheme, 
[InputDecorationTheme](https://docs.flutter.io/flutter/material/InputDecorationTheme-class.html) inputDecorationTheme, 
[IconThemeData](https://docs.flutter.io/flutter/widgets/IconThemeData-class.html) iconTheme, 
[IconThemeData](https://docs.flutter.io/flutter/widgets/IconThemeData-class.html) primaryIconTheme, 
[IconThemeData](https://docs.flutter.io/flutter/widgets/IconThemeData-class.html) accentIconTheme, 
[SliderThemeData](https://docs.flutter.io/flutter/material/SliderThemeData-class.html) sliderTheme, 
[ChipThemeData](https://docs.flutter.io/flutter/material/ChipThemeData-class.html) chipTheme, 
[TargetPlatform](https://docs.flutter.io/flutter/foundation/TargetPlatform-class.html) platform 

}) 
```

Enter fullscreen mode Exit fullscreen mode

### 错误:怎么知道不对劲？

颤振开发的牛逼之处在于模拟器/仿真器通过*死亡红屏即时提供反馈。*这些描述非常详细，让您深入了解问题所在。

<figure>[![](../Images/f65ec9caacdc32349a433d5eb689a9fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ENGY8xBb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/974/1%2AxwP6l8FtAOUAIPT9-d84lQ.png) 

<figcaption>死亡的红幕</figcaption>

</figure>

你可以通过撤销最近的修改、注释掉和修改已识别的部分来快速探索想法(例如，如果有问题的部件是随机单词，就从那里开始)..).热重载使得相对快速地跟踪和修复至少与 UI 相关的错误成为可能。

### EXPLORATION IDEAS

*尝试一下这些东西，以便更好地了解开发环境:*

1.  **改变主题数据**的属性，看看会有什么影响
2.  将 ListView 构建器更改为使用不同种类的项目小部件。看看这是如何影响滚动和外观的。
3.  **探索路由选项**。尝试创建一个新的路由和相关的小部件，看看是否得到默认的返回行为。

### 未决问题

我将利用这一部分来记录与会者在研讨会上遇到的任何问题。特别是，我很想知道 Windows 或 Linux 开发环境中的参与者的过程/体验是否同样轻松。

* * ***
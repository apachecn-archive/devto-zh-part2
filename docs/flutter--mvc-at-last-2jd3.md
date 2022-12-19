# 终于扑+ MVC 了！

> 原文：<https://dev.to/andrious/flutter--mvc-at-last-2jd3>

颤振框架的“吻”。

为了与[“KISS 原则”](https://en.wikipedia.org/wiki/KISS_principle)保持一致，这是一次以直观的方式向 Flutter 社区提供 MVC 设计模式的尝试，其中包含了 Flutter 框架本身的大部分内容。所有这些都包含在一个单独的颤振包中:

> [***MVC _ pattern***T5】](https://pub.dartlang.org/packages/mvc_pattern)

### 一些 Github 的例子！

右键单击并将这些示例保存为书签。您可能希望以后尝试这些示例。他们使用这种 MVC 实现。

> [计数器应用示例](https://github.com/AndriousSolutions/mvc_pattern/tree/master/test) —计数器应用首先使用新的颤振项目创建。然而，这一个将使用 MVC 包。
> 
> [Startup_namer 示例](https://github.com/AndriousSolutions/mvc_pattern/blob/master/example/lib/main.dart)——[你的第一个 Flutter App](https://flutter.io/get-started/codelab/) 但是有了这个包。

### 忘记我告诉你的一切！

顺便说一句，如果你一直在关注我关于 Medium.com 的报道，那就忘了我写的另外两篇文章吧！不要读它们！(好吧，也许是第一点[一个 MVC 方法颤振](https://proandroiddev.com/mvc-in-flutter-ebfba2a78842)。)从那以后，我学到了很多东西，并尝试如何将 MVC 与 Flutter 结合起来。我做了一个 Flutter 框架，现在我把它用于我所有的应用程序！我现在很满意。所以，我把它放在这个包里，作为我所有应用程序的基础。

[![](../Images/0788ee963d17f8505003391e17c16ea2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hhibSq21--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ak9sW9V5b8gBDhTYSC_zxxQ.png)T3】

像许多设计模式一样，MVC 旨在分离“工作区域”这意味着在开发软件时要划分“职责范围”。对于 MVC，这意味着将组成应用程序的“接口”和“功能”以及“数据”分离开来。将软件中的这三个主要区域解耦，可以实现高效的模块化编码、代码重用和并行开发:

*   模型—数据的来源。在一些实现中，它包含业务逻辑。
*   视图-关注数据(不一定是模型中的数据)在用户界面中的显示方式。
*   控制器—控制显示什么数据，响应系统或用户事件，并且在某些实现中，包含业务逻辑。

> “模型是。视图显示(模型是什么)。控制器改变(模型是什么或者视图显示什么)。”— [乔恩·波弟](https://stackoverflow.com/questions/2626803/mvc-model-view-controller-can-it-be-explained-in-simple-terms#comment-7321702)2011 年 6 月 8 日 1 点 51 分

这个实现中传达的 MVC 版本实际上更像是一个 [PAC(表示-抽象-控制)](https://softwareengineering.stackexchange.com/questions/207620/what-are-the-downfalls-of-mvc#answer-207672)模式，而不是经典的 MVC。在 PAC 模式中，控制器包含业务层。理想情况下,“视图”中的代码不会直接影响或干扰“模型”中的代码，反之亦然。而“控制器”在应用程序的生命周期中控制其“状态”。视图可以与控制器“对话”;控制器可以与模型“对话”,但是视图不知道模型，反之亦然。

[![](../Images/c8db9b767118e2aee6da56795ae989c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E7svzpi5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/429/1%2Aiwv5esMii2XduJAdm02gQA.jpeg)

然而，这个特殊的 MVC 实现被设计成允许实现更经典的模式。一个允许视图和控制器互相“交谈”，以及“交谈”(调用公共函数和公共属性)模型。

[![](../Images/b1de283a7bbd7a7fab4de61b194fc9bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6f8DmWbj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/259/1%2AMFExhtGY3GYvGdiK8IvGUQ.jpeg)

### 但是，没有模型

请注意，我特意将模型的任何实现排除在这个版本的 MVC 之外。你不会在这个包里找到模型的概念。有“视图类”，有“控制器类”，但没有“模型类”。如果以及如何实现一个模型，我留给开发人员。

当谈到模型时，您甚至可以选择实现某种混合(以及所有需要的)。例如，一个 Dart 文件可以包含被定义为控制器和模型的内容。

[![](../Images/9019d03caf419cac664c159245573295.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G8CHIkxA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/304/1%2A5mN__kfpdnD279srOZS6dA.jpeg)

可能你的 app 没有数据！可能你的 app 有很多数据吧！(即任意数量的模型)。谁知道呢！这个方案会考虑到这一点……坦率地说，不关心任何一方。

[![](../Images/8a4f4ee41ef791300064aaeb5314bdc9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--amHKJrMy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/669/1%2AoUoXBfcijZlmTIWO6omXpA.jpeg)

最后一幅图展示了模型的另一个可能用途。有时你的应用程序可能会使用另一个应用程序的数据源。您的模型用于将其他应用程序的数据“转换”为适合您的应用程序的格式。这是大量应用程序使用的数据。你的只是一个。

这个库包对[颤振体系结构样本项目](http://fluttersamples.com/)的贡献证明了这一点。你会发现在 [MVC 例子中的模型](https://github.com/brianegan/flutter_architecture_samples/tree/master/example/mvc)仅仅是将数据请求‘中继’给其他架构设计模式也使用的模型。在这种情况下，作用域模型。

### **规矩还是有的！**

从一开始，这个包的意图不仅是允许开发人员自由地编写他们的代码——以最好的方式满足他们的需求，而且以最好的方式利用底层的 Flutter 框架。

然而，不管你喜不喜欢，还是有一些规则要遵守的。我将在后续文章中讨论所有这些问题，但是现在，有一个重要的规则您必须知道。有一个规则，一个规定，如果你决定在构建你的应用时使用这个包，你必须遵守:**一个控制器只能有一个视图。**一个控制器不能分配给多个视图。如果你想让你的“业务逻辑”访问多个视图，你必须把你的代码分解成独立的控制器。如果必要的话，你可以让这些控制器互相“交谈”。

[![](../Images/632ae69215af1bee1328828bb4a790dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u6c73p6K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/242/1%2AHWyOoQ0glOfGaGSRPjhaEg.jpeg)

此外，这是一个小手机的应用程序。它不适合像脸书这样的大型网站。你可能会有“一个视图”和“一个控制器”除此之外，这就是设计模式、框架、架构或者任何你想称之为它们的东西的用途。他们不仅组织代码，还强加一些结构，一些符合性和一致性。

这样做的想法是，让开发团队中熟悉这个包或者至少熟悉 MVC 设计模式的新开发人员走进去，就已经知道所有代码在哪里了。在经理的指导下，开发人员可以“立即投入工作”,直接进入应用程序，知道代码是如何安排的，样板文件是如何工作的——在这种情况下，样板文件很少。

然而，就像任何好的 MVC 设计应该做的那样，你可以根据自己的喜好创建任意多的控制器来访问一个视图。每个人都准备好对来自该视图的任何事件或过程作出反应。哦，对了，还有事件处理！

[![](../Images/1e83a8cc5c2e8fb3b20c0b1933ec3ee3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vrfV_ZOb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/240/1%2AYdbMoOhIp9JnyVadL-md3A.jpeg)

同样，对于大多数简单的应用程序，通常只有一个视图和一个控制器。然而，如果有许多控制器被分配给一个视图，并且当一个“事件”在该视图中发生时，控制器将按照它们被分配的顺序依次触发。

### **所以呢？你如何使用它？**

那么，这个包怎么用呢？好吧，让我们回到每次创建新的 Flutter 项目时首先向您介绍的 ol ' '计数器应用程序'。为了利用计数器应用程序中的包，我改变了三件事。我用类 StateMVC 扩展了类 _MyHomePageState，我引入了一个扩展 ControllerMVC 的“控制器”类，然后我向 **build()** 函数引入了该控制器类的一个静态实例。搞定了。

[![](../Images/727ef072ebaeed23dbce9b55e4dd3c08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rMY1LmnR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcQyh4Pxy1so2UIEIRTi0Sw.jpeg)

<figcaption>[my home page . dart](https://gist.github.com/Andrious/ffb953a1e7d4978acaedbd7fd24647f7#file-mvccounterapp-dart)要点上</figcaption>

现在，当然，这里有很多事情正在发生，但是你没有看到。就像任何好的库包应该做的那样，很多东西都在后台保存。然而，一眼看去，你可以看到现在“接口”和“数据”是分离的 **build()** 函数(视图)只关心应用程序界面的“观感”——事物是如何显示的。在这种情况下，控制器关心的是显示什么。

视图显示什么数据？它不知道也不关心。而是与控制器“对话”。同样，是控制器决定视图显示“什么”数据。在这种情况下，它是应用程序的标题和计数器。即使按下按钮，视图也会转向控制器，通过调用控制器的一个公共函数 **incrementCounter()** 来处理事件。

### 但是 StatefulWidget 呢？

注意，在这种配置下，有状态小部件几乎是空的。除了传递应用程序的标题(这是一个可以通过其他方式完成的琐碎任务)，它的作用只是创建状态对象。

现在它的作用仅限于此，StateMVC 类可以简单地扩展***State<stateful widget>*T3】。换句话说，任何曾经适用于 StatefulWidget，MyHomePage 的代码，现在都有可能进入控制器。**

### 事物如何关联？

请注意，在这种安排中，控制器通过调用视图的函数 **setState()** 告诉视图“重建”，从而与视图“对话”

[![](../Images/bd18bf551c4425a80d953c7c82927dfc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XU8__5Dh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/259/1%2AzmbmhpruRfW9SIPRAAT-3g.jpeg)

也许你不想那样。也许您希望视图只关注界面，并且它单独决定何时重建或不重建。很简单的改变。

[![](../Images/245452ae3a1e2b7c76873bc1df182fa5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ya4VdmhZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/259/1%2AVtDolI6PlrQriTrewGO11A.jpeg)

[![](../Images/6c00a1ed002254fdecf921d3322e40c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tsvP-Y6f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AX5nd0kB2fjqS8rAzowf-3Q.jpeg)

<figcaption>[myapp . dart](https://gist.github.com/Andrious/ffb953a1e7d4978acaedbd7fd24647f7#file-mvccounterapp-dart)和[my home page . dart](https://gist.github.com/Andrious/ffb953a1e7d4978acaedbd7fd24647f7#file-myhomepage-dart)on Gist</figcaption>

您会看到函数 **setState()** 现在在“视图”(在构建函数中)中被调用，而不是在控制器中。这样做确实把“责任角色”分开了一点，不是吗？毕竟，它是与界面相关的视图。它可能最知道何时重建，不是吗？不管怎样，有了这个包，这些事情就留给了开发者。另外，注意到我对应用程序的标题做了什么吗？我在 MyApp 类中创建了一个名为 title 的静态字符串字段。毕竟是 app。它应该知道自己的标题。将 StatefulWidget 对象留给它唯一的任务—创建状态对象。

### **模特怎么样？**

目前，在这个例子中，是控制器包含了应用程序的所有“业务逻辑”。在一些 MVC 实现中，它是包含应用程序业务规则的模型。那会是什么样子呢？嗯，也许它看起来像这样:

[![](../Images/d2e29175504dbf694c562b1c9b5a1180.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--voWk2NjW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARd5TsdOzNqs4PtNPZWAWsQ.jpeg) 

<figcaption>[控制器.镖](https://gist.github.com/Andrious/ffb953a1e7d4978acaedbd7fd24647f7#file-controller-dart)和[模型.镖](https://gist.github.com/Andrious/ffb953a1e7d4978acaedbd7fd24647f7#file-model-dart)依据要点</figcaption>

我决定通过使用静态成员使模型的 API 更加简洁。如您所见，更改只是在控制器中进行的。视图甚至不知道模型的存在。它不需要。它仍然与控制器“对话”，但现在它是拥有所有“大脑”的模型

[![](../Images/c8db9b767118e2aee6da56795ae989c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E7svzpi5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/429/1%2Aiwv5esMii2XduJAdm02gQA.jpeg)

但是，如果您希望视图与模型对话，该怎么办呢？也许是因为模型有无数的函数，而你不希望控制器只是将模型的函数和属性“传递”给视图。您可以简单地向视图提供模型。然后视图直接调用模型的属性和函数。

[![](../Images/a05da13c932bf47f23463910c95bd2d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1w7aHkMd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/259/1%2AR0RRepumSX6VVXNsNEqGew.jpeg)

[![](../Images/8a45d82dbddab8b557c9e1ca2aaccb76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iJeQgssV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzUxbwrR-IQhqck5WaeHzUw.jpeg)

<figcaption>[myapp . dart](https://gist.github.com/Andrious/ffb953a1e7d4978acaedbd7fd24647f7#file-mvccounterapp-dart)， [MyHomePage.dart](https://gist.github.com/Andrious/ffb953a1e7d4978acaedbd7fd24647f7#file-myhomepage-dart) ， [Controller.dart](https://gist.github.com/Andrious/ffb953a1e7d4978acaedbd7fd24647f7#file-controller-dart) 和 [Model.dart](https://gist.github.com/Andrious/ffb953a1e7d4978acaedbd7fd24647f7#file-model-dart) on Gist</figcaption>

不是特别漂亮。我将只保留模型中的静态成员，并让视图调用它们(或者坦白地说根本不这样做)，但我只是演示了可能性。有了这个 MVC 实现，您就有了选择，开发人员喜欢选择。

### **怎么回事？**

当使用 is MVC 实现时，通常重写两个类:控制器(类 ControllerMVC)和状态视图(类 StateMVC)。下面是覆盖控制器的典型方法。

[![](../Images/7f0b5adead49015a608d1b1792a4f76b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3T7s21BF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZdrq4VRKRq_k-CvY0shLhA.jpeg)

控制器可以“直接访问”视图(也称为。州视图，又名。类 StateMVC)。这由 ControllerMVC 类中的属性 stateView 表示。在本例中，是对视图的“静态”引用。在构造函数中，还对控制器本身进行了“静态”引用。这个例子传达了一个处理应用程序控制器的好方法，因为这样你就可以在整个应用程序中轻松访问你的控制器。它现在位于一个名为 **con** 的静态字段中。在下面的另一个 Dart 文件中查看如何轻松访问它:

[![](../Images/a2148c920895ab8cd6ed784d0a176264.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hRMj4GK---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADlHhP_dl9SLW35wK-3Dytw.jpeg)

现在，在任何视图或任何小部件中，您都可以通过它的控制器轻松、干净地访问应用程序的数据或业务逻辑。例如，您可以使用字段 **con** 在 **build()** 函数中引用控制器。

另一个类(StateMVC)调用了“StateView”，因为在这个 MVC 实现中，“View”是一个状态对象。惊喜！

[![](../Images/8aa02567c652f14acfa77d4cc4aeafe5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t8R9eFNh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYhQFIB9GuVW9VexW8aMXMw.jpeg)

注意，StateView 像 State 类一样是抽象的，必须扩展才能实现它的 **build()** 函数。所以，事实上，在这个 MVC 实现中，“视图”只是状态对象的 **build()** 函数！惊喜！

[![](../Images/022a1670e8161b9558857352137fecfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DJQ6NoQ3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYMEBaPBN5DiCBSiwtqYO7A.jpeg) 

<figcaption>[类 StateMVC](https://github.com/AndriousSolutions/mvc_pattern/blob/74a267e6b3e4f1781eb49078efe7de4937971347/lib/mvc_pattern.dart#L307) 上 Github</figcaption>

### **控制器**

注意，控制器类(ControllerMVC)乍一看很薄，但这是设计使然。这只是给你更多的空间来编写你的应用程序！所有的“跑腿”工作都在类 _StateView 中完成。“控制器”父类的名字很奇怪，不是吗？接下来你会明白为什么。

[![](../Images/e0cd108fbabe40436796668651d2a9b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nv5ixn2G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AESBJsWSrMPi_G_UD1AesUA.jpeg)

<figcaption>[Github](https://github.com/AndriousSolutions/mvc_pattern/blob/74a267e6b3e4f1781eb49078efe7de4937971347/lib/mvc_pattern.dart#L34)</figcaption>

上的类控制器 VC

### **设置状态**

当然,‘状态视图’有 ***setState()*** 功能。毕竟是国家对象。但是，使用这个 MVC 实现，您的控制器中也有了 ***setState()*** 函数！两个类都可以调用。那是非常重要的。你所有的控制器都可以像一个状态对象一样工作！惊喜！那是因为控制器类(ControllerMVC)扩展了 _StateView。

[![](../Images/11ef9c78cfc1b35e28557e8bbdf762c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KYU6aisX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAJcmzIMsv2oJp-5NUeMShQ.jpeg)

<figcaption>[【void setstate(fn)](https://github.com/AndriousSolutions/mvc_pattern/blob/74a267e6b3e4f1781eb49078efe7de4937971347/lib/mvc_pattern.dart#L71)on github</figcaption>

这两个类，也都有一个 **refresh()** 函数。“刷新”或“重建”用户界面(视图)。这是个小把戏。这只是用空闭包调用的函数，**【setState()**。可爱。

[![](../Images/be4eb58ce465fb2d36b0715a6028c429.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qDnDvTXz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Alx8UI5Hb7nvx82OcM5Hd9A.jpeg) 

<figcaption>[刷新()](https://github.com/AndriousSolutions/mvc_pattern/blob/74a267e6b3e4f1781eb49078efe7de4937971347/lib/mvc_pattern.dart#L75)上 Github</figcaption>

### **听着！有听众！**

注意，“控制器”类有一个名为 StateEvents 的父类(class _StateView 扩展了 StateEvents)。正是这个类允许控制器响应由电话或(间接)由用户产生的事件。从这个版本开始，大约有 12 个独立的事件可供该类使用。注意，您也可以使用这个 StateEvents 类！扩展这个类，你就有了一个可以在你的应用中使用的“事件监听器”——与分配给特定视图的控制器一起触发的监听器。很有趣，不是吗？

[![](../Images/34f6cbba8f20cd2d5ccd9ba808b5e4ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Knlb9CFh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_6NCiqEAQdH6qIShaL9lHw.jpeg) 

<figcaption>[类事件](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L209)在 Github</figcaption>

请注意,“StateEvents 侦听器”不能访问“StateView”对象，而只能访问其“State”对象。如果你想更多地访问“视图”，那么你的类应该扩展类 ControllerMVC。稍后我将为这些听众专门撰写一篇文章。

现在，下面是控制器(以及“StateView ”)用来注册这些侦听器对象的三个函数。使用这样的函数，控制器(类 ControllerMVC)可以将侦听器分配给它自己被分配到的视图，而“StateView”(类 StateMVC)可以直接分配侦听器。

[![](../Images/6271c3837cc76f3671ad6e83982c2b7f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f-tW7eC1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AivYbhSiGsrx6ak2S1NCo2Q.jpeg)

[![](../Images/946dda1d0fbe39e40ff048be82bfa1e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HOXRsFrk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOZ1yemMvAH8euCZ1bQKKgA.jpeg)

Github 上的[![](../Images/ddd76dc706cd0028991ec841455163a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UYnkMjGM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AreUgM8XPjf67W3uECPzJ_A.jpeg)

<figcaption>[addBeforeListener](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L173)、[addafelistener](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L179)和 [addListener](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L186)</figcaption>

当然，如果必要的话，您有办法删除侦听器。

[![](../Images/8dbc9a4d27c011783845001a38dce490.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g6AGb_An--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A52-RtfqZvSaJRq04ji0DdQ.jpeg) 

<figcaption>[【删除监听器】](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L193)上的 Github</figcaption>

### **前后**

注意，函数 **addListener()** 与函数 **addAfterListener()** 做同样的事情。“之前”和“之后”引用意味着您可以分配将在控制器触发之前或之后触发的侦听器。为什么？我不知道！这是你的应用！；)

### **每个事件的一个函数**

下面，您将看到可供您参加的 12 项活动。详细内容请参见[***WidgetsBindingObserver 类***](https://docs.flutter.io/flutter/widgets/WidgetsBindingObserver-class.html) 。坦率地说，您可能永远也不会用到所有这些事件。有些你每次都会用到，但是大多数你都不需要用到。无论如何，如果您这样做，这个框架允许轻松实现。

[![](../Images/982a44fad7f0695ace521a9135e3dead.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W-Ds71bv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AaOM8H5aJLF_A4Qtye3br1Q.jpeg)

[![](../Images/1bf3f2742bdff0cb66237c7207f12312.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aGWIMTnT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AiAAqfdeUek-5xGX3sIw9nw.jpeg)

[![](../Images/ed4ecc4820114b1f2f28fb82c261c8f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nXCaTDdp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhnqRnZ9rxxHK_RFwPi-ubg.jpeg)

[![](../Images/2398f8fe8006798b4ba440309f90ee65.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hrDrwfy3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Amkhiee2awDTFsITMa5_etw.jpeg)

[![](../Images/68d941da0d934a7ad9b2ba25003b9e1a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wH0nt8Hm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AG4vw6NUJnFF5cuEKZ0u0BA.jpeg)

[![](../Images/cdc26966ef80a8e1f31f07d1a77e72d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eTHhDjQo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXTOIic1h0Gp6oCvzXWWyFg.jpeg)

[![](../Images/a37382488739c2f2bf7e61befdddc0d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OEW1JHTI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJ3leEkYpP85JXyZUnd7XYQ.jpeg)

[![](../Images/1b1b8d179805a1880ad3971760465c3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QuLoSkk2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApiXvkfkQvjEanyKwvkLgaw.jpeg)

[![](../Images/2019f99e6f7e00522265d7808e591436.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cO2ZE7OD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AEX6_eNyDO09L6qJGzw_Qug.jpeg)

[![](../Images/4a9607d68457730129dc158ba267f58d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CoHkv6Za--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6tofqXlIV1rHlxOFqPGM5A.jpeg)

[![](../Images/e86af840de3ce3c8e2379581519ac39c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vS0whjUA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATN8_E09G3w5IyidnSvOivA.jpeg)

![](../Images/7820d500d73d40644789e8355a930c5b.png)

<figcaption>[【init state】](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L244)，[规定](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L267)，[【deactivate】](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L255)，[【diupdatewidget】](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L280)，[改变依赖关系】](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L359)</figcaption>

### **那个又统治了！**

适用于视图及其控制器的规则同样适用于它的侦听器:**一个侦听器只能被分配给一个视图。**随着时间的推移，事情可能会发生变化，允许控制者“作为控制者”访问一个视图，但“作为听众”访问任意数量的视图。毕竟，侦听器只是在特定事件被触发时被调用。我们走着瞧。也许在 2.0 版本？

[![](../Images/f7acf20fe943d214657d3ca32b8aa14b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--olB4FBmp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/242/1%2ANOiQdMSTkcZo1-1HAdOmMw.jpeg)

现在，只有当您希望某个特定的代码为某个特定视图中的某个特定事件触发时，才使用侦听器(即扩展 StateEvents 的类)。同样，像控制器一样，您可以将任意数量的侦听器分配给一个视图。

[![](../Images/d32c9648e6b5a3ade6db6f8d00ebee87.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4Zy3bDHA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/244/1%2AGVA7qqMk0Kv0FHmAOrWgpA.jpeg)

### **如何启动你的 App**

下面是使用这个包启动一个应用程序的典型方法。这是通过使用 MVC 模式包中的另一个类来完成的。叫做 AppMVC。

[![](../Images/126c3114c4c20c70e73aebd3bdee070c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FtXNQTGq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0OSZCMd-xc--09ZXHadmPg.jpeg)

下面是 AppMVC 类的“概念表示”,为了简洁起见，去掉了它的逻辑。然而，剩下的东西会让你意识到你可以用这门课做些什么，这是很多的。同样，这里没有列出一些事件，但是上面提到的所有 12 个事件都被提到了，如果开发者愿意的话，可以在“应用程序级别”使用。

[![](../Images/a8fea05683fd8b6c57f953efe2deac8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yygbVHxW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Atz3CkiH2T0WPEuRvtc42mA.jpeg)

<figcaption>[Github](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L1329)</figcaption>

中的 AppMVC 类

两个“新”函数只存在于“应用程序类”中: **initApp()** 和 **init()** 。很多东西在启动一个 App 的时候都要‘启动’，这两个功能为它们提供了场所。函数 **initApp()** 用于“快速”同步进程，而 **init()** 函数返回一个未来值，对于异步操作也是如此。在这种情况下，通常会调用 **FutureBuilder()** 函数。你看，一个人必须在应用程序开始时等待这种“异步的东西”完成，所以在我们等待时会显示一个“加载屏幕”。

[![](../Images/b909923ea3434fd291631732a9c49f00.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V5gOWe2E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHwewm4taKqpEOewgASAy_Q.jpeg)

### 有一个错误处理程序

你注意到 App 类中的最后一个函数了吗？见下文。没错。这是一个错误处理器。错误处理很重要。但是就像写文档一样，我们程序员倾向于忽略它。(这提醒了我，我必须在软件包的在线文档上工作。太恐怖了！呵呵)

[![](../Images/8843cc4bd287de92e66895381ce3ab08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PzyN5HrU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0UDnnIqLR15toDpfKUMWqQ.jpeg)

<figcaption>[onError()](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L1175)在 Github</figcaption>

我说到哪了？！哦是的！如果放任不管，一旦出现错误，这个框架的默认反应就是通常的“红屏死机”。然而，我们鼓励开发人员不仅要在代码中明智地使用他们的 ***try…catch*** 语句，还要覆盖 **onError()** 函数来“捕捉”任何意外错误。面对现实吧。你的应用总有一天会失败。然而，从这个框架的基础开始，我们鼓励您解决错误处理。让你的应用程序以一种更优雅的方式失败…甚至可以在某个地方通过电子邮件发送错误报告。

### 有一个错误处理程序。到处都是！

同样，在这个 MVC 实现中，错误处理被认为是最重要的。“App”类有自己的错误处理程序，但其他所有的都有！这个包在你做的所有事情中都放置了一个错误处理程序:在每个控制器、每个监听器、每个“状态视图”中，在某些情况下，甚至就在 **build()** 函数中！

还记得 StateEvents 类吗？允许控制器和监听器响应事件的那个？它有一个错误处理器。这意味着您的控制器和您可能创建的任意数量的侦听器也是如此。

[![](../Images/83f3105c3ec452f72447f1b8a06a3128.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FDznA99m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3_hjhINOn0YG6fWhycvelQ.jpeg)

<figcaption>[onError()](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L387)在 Github</figcaption>

### 是你的责任。处理它！

我反复考虑在这个框架中“强加”我自己的错误处理程序的想法，但是，最终，我决定反对它。这将违背这个框架背后的理念:开发者有选择权。此外，对于如何处理特定应用程序中的意外错误，开发者会有更好的主意！默认情况下，错误响应(死亡的红屏)被保留。实现错误处理是开发人员的责任。这个框架，至少，随时提供了一种手段……无处不在。

### state view 错误处理程序

注意，甚至我们的 state view(state MVC 类)也有一个错误处理程序。如果分配给它的所有控制器都懒得创建自己的错误处理，开发人员可以在“状态级别”创建一个错误处理程序，它至少会在出错时尝试处理事情。

[![](../Images/a00b276d705f1ebd03b8a726a5d96f99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rm7zMyF8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIv8Ne1oY3okIEsueXAN4FQ.jpeg)

<figcaption>[onError()](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L848)在 Github</figcaption>

这不是一个好的做法，但也许你没有被分配为你的应用程序创建控制器。也许你已经被分配了“状态级别”的东西，并且你正在扩展类 StateMVC 的类中工作。至少，如果其中一个控制器出错，您可以确保您的状态对象保持运行。同样，模块化编码是使用设计模式的好处之一。

### 另一种观点

错误处理被认为是如此重要，还有一个额外的状态对象类叫做 StateViewMVC。它扩展了 StateMVC，但是有自己的错误处理——就在它的 **build()** 函数中。

这确实意味着“突破”了一点，因为你现在必须扩展三个类，而不是两个。还记得吗，我说过你一般会重写两个类:Controller(类 ControllerMVC)和 StateView(类 StateMVC)？嗯，有…等等…另一个选择。爱情选项！

还有一种“另一种观点”，它的名字更适合于这种冒险，叫做 ViewMVC。这个“视图”不是一个状态对象，但是你仍然扩展它来为你的接口实现它的 **build()** 函数！注意，这个“视图”扩展了与控制器相同的类 _StateView！？这是什么意思？！

[![](../Images/d5a9ae7e1170b91ba3fda1470c8debd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_mVLML1o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcyMXwa0Z2BgzCbHpqEMOPg.jpeg) 

<figcaption>[控制器 MVC](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L41) 和[视图 MVC](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L45) 在 Github 上</figcaption>

因为控制器和这个“视图类”都扩展了同一个父类，它们都可以处理事件，并且它们的行为都像状态对象。这是一个游戏改变者！

### 其他 StateView 类

因此，除了 StateMVC 类，还有另一个“StateView”类。然而，另一个类将其“视图”作为其构造函数的参数。注意，参数 view 将其“控制器”传递给超类 StateMVC。

[![](../Images/d0e07f723d5026d92e2169180c720dc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_aLc9G80--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvgXRV0q1Xcu2mdjm8F2D9Q.jpeg)

<figcaption>[【state view MVC】](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L392)在 Github</figcaption>

上运行

### 这个状态视图处理错误

与它的父类 StateMVC 不同，这个类在其 **build()** 函数中有一个错误处理程序！

[![](../Images/b39cd996ca56843c55c15e45543a2ea1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SQZ-DMZT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/360/1%2AjzbDKOO5UIy8zWEywJfqzg.jpeg) 

<figcaption>StateViewMVC</figcaption>

这意味着，当“重建您的部件树”时，所有这些控制器和监听器依次运行，您可以放心，如果其中一个行为不当并发生错误，您实现的错误处理程序将尽一切努力优雅地失败，或者，如果可能，恢复并继续！在接下来的几周里，我将会写一篇关于这种方法的文章。

[![](../Images/d93e6f7b3079d996f3750de1e0936837.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3HdwZ1ea--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0M-72Dn9ruUrveM66Xem7A.jpeg)

<figcaption>[StateViewMVC 在 Github 上构建](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L416)</figcaption>

### 什么来了

事实上，随着第一篇文章的发布，还会有更多的东西出现。事实上，这只是“宣布”这个包的正式发布。这里还有更多的东西需要提及。比如还有一个类叫 StatelessWidgetMVC！这是怎么回事？！

我甚至没有提到我称为 StatedWidget 的类。我没有创建 StatefulWidget 和它的伴随状态<t extends="" statefulwidget="">，而是使用了这个类。它已经在框架中用于 AppMVC 类。最后，我们刚刚触及了错误处理的表面！更多即将到来。</t>

[![](../Images/791d7619abf678bce82cffd3f4353910.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7yruQtvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACFEfZv6suXYm0VsrYyLd1Q.jpeg)

<figcaption>[Github](https://github.com/AndriousSolutions/mvc_pattern/blob/a6fce58/lib/mvc_pattern.dart#L1329)</figcaption>

中的 AppMVC

### 终于自由地

我把这个包免费送给我们羽翼未丰的 Flutter 社区。我看到了需求。我觉得这将是一个很好的选择，当你开始下一个移动应用程序。有选择总是好的，对吧？

干杯。

* * *
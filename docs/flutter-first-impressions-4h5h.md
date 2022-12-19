# 飘动的第一印象

> 原文：<https://dev.to/belchii/flutter-first-impressions-4h5h>

# 旋舞的第一印象

在听到这么多关于 [Flutter](https://flutter.io) 平台的宣传后，我决定试一试，并写下我对开发者体验的第一印象。我是一名 Android 开发人员，为 Android 和 iOS 编写相同代码的想法非常令人兴奋。

由于我没有任何借口在我的公司或个人项目上使用 Fluter，我搜索了几个公共 API，并决定围绕它编写最简单的应用程序。我找到了 Tronald Dump T1，这是一个 API &网站档案，里面有唐纳德·川普说过的最愚蠢的话。*(我想档案不会再更新了，但还是可以的)*

这是我写的简单应用程序:

[https://www.youtube.com/embed/ul7ygXDXn3Y](https://www.youtube.com/embed/ul7ygXDXn3Y)
(iOS 版；安卓版在这里[https://youtu.be/aS14Eobn738](https://youtu.be/aS14Eobn738)

# 我喜欢什么:

*   **文档**:官方文档很有帮助，涵盖了你需要开始的大多数场景:小部件、网络、持久性、测试等。

*   **社区**:已经有大量关于 Dart 和 Flutter 的教程，这些都是官方文档没有涉及的内容。例如，我使用了由 [Chema Rubio](https://medium.com/@develodroid) 在他的[博客文章](https://medium.com/@develodroid/flutter-iv-mvp-architecture-e4a979d9f47e)中描述的 MVP 架构和依赖注入。

*   **热重装**:和大家说的一样好。的确，你不时需要重新构建应用程序，但对于调整用户界面来说，这是一个很好的体验。我觉得它能让你学得更快，因为你能很快看出什么有用，什么没用。

*   **Dart** :来自 Java 世界，习惯起来并不难。这个[异步/等待](https://www.dartlang.org/tutorials/language/futures)让我想起了[科特林的协程](https://kotlinlang.org/docs/reference/coroutines-overview.html)，那很好。它为函数式编程和一个[流 API](https://www.dartlang.org/tutorials/language/streams) 提供了构造。或者你来自 RxJava 世界，离不开它，还有 [RxDart](https://github.com/ReactiveX/rxdart) 。

*   **测试**:颤振自动测试有现成的支持。我分别尝试了每种类型([单元](https://github.com/RicardoBelchior/TronaldDump/blob/master/test/quote_test.dart)、[小部件](https://github.com/RicardoBelchior/TronaldDump/blob/master/test/quote_widget_test.dart)、[集成](https://github.com/RicardoBelchior/TronaldDump/blob/master/test_driver/tag_screen_test.dart))，与 Android 相比，体验非常自然，我没有发现任何问题，只是遵循了官方文件。尽管我的测试很简单，但我相信它可以轻松扩展。

# 我不喜欢什么:

*   **分号(；)** : Java 需要分号。科特林不需要分号。Dart 需要分号。Javascript 没有。grrrrrrrrrrrrr……([将科特林与颤振](https://discuss.kotlinlang.org/t/kotlin-language-feature-request-support-flutter/8134)一起使用会很酷，尽管这不会很快发生)

*   **启动时间**:启动时间不算长，但是比你在 Android 上的标准 hello-world 启动时间要长得多。我想知道这将产生多大的影响，当你必须初始化一系列商业应用程序通常需要的依赖项(崩溃报告工具、网络服务、广告服务、分析工具等)时。)

*   **架构**:或者说缺乏架构。Flutter 在编写应用程序的方式上非常灵活。太棒了。然而，Flutter 似乎处于 Android 不久前所处的位置，因为没有关于如何构建应用程序的官方建议。这导致太多的应用程序变成了[的泥巴大球](https://en.wikipedia.org/wiki/Big_ball_of_mud)——没有分离关注点，很少测试，难以维护。

我知道这可能不是颤振团队的优先事项，但我担心这些问题很容易发生。在 Android 上，相当一部分 UI 是用 XML 文件编写的，这迫使程序员将他们的 UI 和业务逻辑分开(至少一点点)。对于 Flutter，由于所有东西都是用 Dart 编码的，我认为我们需要更加小心，不要违反这些关注点的分离。

另一个例子:我发现有一个单独的字符串类文件，一个颜色类文件，一个路由类文件非常有用。但那是我的选择。我可能很懒，没有那样做。这很容易成为维护和重构的痛苦。

同样关于架构，已经有几种非官方的模式:[有状态/无状态小部件、继承小部件、流/接收器、Redux 等。](https://www.youtube.com/watch?v=RS36gBEp8OI)我不想要这么多选择…我该挑哪一个？=)

# 总结

我写了一个非常简单的应用程序，有许多主题我没有涉及，但对生产就绪的应用程序仍然很重要:本地数据库、崩溃报告工具、分析、广告等。

总的来说，我对这次学习经历很满意。你可以在这里找到源代码[https://github.com/RicardoBelchior/TronaldDump/](https://github.com/RicardoBelchior/TronaldDump/)

希望你喜欢，请留下一些评论或在推特上联系我[https://twitter.com/belchii](https://twitter.com/belchii)
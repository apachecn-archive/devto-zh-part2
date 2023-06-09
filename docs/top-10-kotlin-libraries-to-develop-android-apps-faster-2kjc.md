# 十大 Kotlin 库加速开发 Android 应用

> 原文：<https://dev.to/yuvrajv5/top-10-kotlin-libraries-to-develop-android-apps-faster-2kjc>

你已经开始在你的 Android 应用开发项目中使用 Kotlin 了吗？

作为一名 Android 开发者，你可能已经尝试过 JetBrains(IntelliJ IDEA 的开发者)开发的这种新的编程语言。

毫无疑问，科特林社区正在急剧增长，每个人都在谈论它。甚至，许多开发人员已经开始学习不同的 Kotlin 库，这些库可以帮助他们开发更强大的 Android 应用程序。

说到 Kotlin 库，有各种各样的库。但是，只选择那些提高生产率并使整个开发过程更简单的库是非常重要的。

如果你已经开始使用 Kotlin 或思考开始使用这种新的编程语言来开发 Android 应用程序，你必须检查这 10 个 Kotlin 库，它们在你的下一个 Android 应用程序开发项目中对你有帮助。甚至，这篇文章对那些想[雇佣 Kotlin 开发者](https://www.spaceotechnologies.com/custom-kotlin-app-development-services/)的人来说也是有用的，你可以用它作为一个交叉问题来了解技术专长。

## 1。安科

Anko 是一个 Kotlin 库，使 Android 应用程序开发更快更容易。由 JetBrains 维护，这个库的主要目的是使代码干净易读。它允许开发者忘记 Android SDK for Java 的粗糙边缘。

这个库有四个主要组件，包括 Commons、Layouts、SQLite 和 Coroutines。主模块有几个助手功能和特性。而布局允许用户使用称为 Anko DSL 的特性从 Kotlin 代码创建 UI。

SQLite 使得与 SQLite 数据库的交互更加简单。最后，协程为 Kotlin 1.1 之一提供了一些辅助函数。此外，Anko 库有一个元依赖，可以将不同的可用特性，包括 Commons、Layouts、SQLite 一次插入到您的项目中。

GitHub-[https://github.com/Kotlin/anko](https://github.com/Kotlin/anko)

## 2。RxKotlin

RXKotlin 是一个轻量级的 Kotlin 库，它为 RxJava 添加了合适的扩展函数，允许开发人员在例外情况下将 RxJava 与 Kotlin 一起使用。然而，Kotlin 具有像扩展函数这样的语言特性，以便更好地简化 RxJava 的使用。

这个库的主要目的是按照惯例将这些便利收集到一个集中的库中，并标准化在 Kotlin 中使用 RxJava 的约定。这个库是基于反应的，其中代码直到它知道它的最终含义才被激活。此外，如果想了解更多关于 RxKotlin 库的知识，学习 RxJava 是最好的书，第 12 章涵盖了 RxKotlin 和 Kotlin 与 RxJava 的习惯用法。

GitHub-[https://github.com/ReactiveX/RxKotlin](https://github.com/ReactiveX/RxKotlin)

## 3。FlexboxLayout

FlexBoxLayout 是一个 Kotlin 库，用于将 CSS 灵活布局模块的相同功能引入 Android。FlexboxLayout 可以被解释为高级 LinerLayout，因为两种布局都按顺序对齐其子视图。

LinearLayout 和 FlexboxLayout 的一个主要区别是 FlexboxLayout 有一个包装特性。通常，有两种不同的方式在布局中使用 Flexbox。

第一个是 FlexboxLayout，它像 LinearLayout 和 RelativeLayout 一样扩展了 ViewGroup。但是，也可以从布局 XML 中指定属性。第二个是在 RecyclerView 中使用的 FlexboxLayoutManager。

GitHub-[https://github.com/google/flexbox-layout](https://github.com/google/flexbox-layout)

## 4。科特林语/本土语

谈到 Kotlin/Native library，它是一个 LLVM 后端，用于 Kotlin 编译器、运行时实现和借助 LLVM 工具链的本机代码生成工具。主要地，这个库被设计成允许平台的编译，在这些平台上，虚拟机是不期望的或不可能的，或者开发者寻求产生合理大小的自包含程序而不需要运送额外的执行运行时。

Kolin/本地库的一些基础知识:

确保为您的平台安装 JDK，而不是 JRE。该构建需要不包含在 JRE 中的 tools.jar。
在 macOS 上安装 Xcode 9.3
在 Fedora 26+ yum 上安装 ncurses–可能需要 compat-libs。

GitHub:[https://github.com/JetBrains/kotlin-native](https://github.com/JetBrains/kotlin-native)

## 5。k 绑定

KBinding 是另一个顶级的 Kotlin 数据绑定库，它被 Anko 广泛使用，以流畅且易于理解的语法实现数据绑定。KBinding 就是这样一个 Kotlin 库，它允许用户以非常灵活的方式将模型数据直接绑定到 xml 视图。

有四种不同的绑定模式，包括:

单向:从模型到视图的绑定
双向:从模型到视图和视图到模型的绑定
单向:从视图到模型的绑定
一次:从模型到视图的绑定，第一次发出后自动释放

GitHub:[https://github.com/BennyWang/KBinding](https://github.com/BennyWang/KBinding)

## 6。格雷尔·科特林 DSL

Gradle Kotlin DSL 是一个 Kotlin 库，支持使用 JetBrains 的 Kotlin 语言编写 Gradle 构建脚本。该库的主要目的是为 Gradle 用户提供一种灵活、丰富和静态类型的方法来开发逻辑，并尽可能提供最好的 ide 和工具体验。

启动并运行基于 Kotlin 的 Gradle 构建的最快方法之一是使用其中一个示例。您可以在自述文件中找到完整的说明。当您的构建逻辑变得更加复杂时，有许多有用的入门说明。

GitHub-[https://github.com/gradle/kotlin-dsl](https://github.com/gradle/kotlin-dsl)

## 7。杀手包

KillerTask 是一个 Kotlin Android 库，用于开发异步后台任务。一般是受 TiinyTask 的启发，但更美观易用，适合 Kotlin Android 开发。正如你所知道的，Android 的 AsyncTasks 被认为是糟糕的、过时的和不可靠的。

然而，有一个更好的选择，那就是在后台快速简单地运行一些东西。这个库用 Kotlin 语言编写，是 Android 程序员构建异步后台任务的完美选择。

GitHub-[https://github.com/inaka/KillerTask](https://github.com/inaka/KillerTask)

## 8。锦鲤

Koi 是一个轻量级的 Android Kotlin 库，附带了很多有用的扩展和功能。这些扩展和功能可以帮助减少 Android 应用程序中的样板代码。

这个库主要附带了一个强大的扩展，叫做 asyncSafe。除此之外，这个 Kotlin 库包含许多有用的扩展函数，允许开发人员保存他们的击键。

GitHub-[https://github.com/mcxiaoke/kotlin-koi](https://github.com/mcxiaoke/kotlin-koi)

## 9。科特森

Kotson 允许开发人员借助更简洁、更容易的语法，用 Google 的 Gson 解析和编写 JSON。作为一组扩展函数，Kotson 库为 Kotlin 中的 Gson 添加了实用函数和语法糖。

这个库既没有给 Gson 增加新的特性，也没有开发新的类型。它对任何 Gson 对象都很有用，不管是从 Java 还是 Kotlin 的源代码或库中创建的。此外，推荐使用这个 Kotlin 库，因为它是基于 Gson 的序列化、反序列化方法。

GitHub-[https://github.com/SalomonBrys/Kotson](https://github.com/SalomonBrys/Kotson)

## 10。坎多罗

KAndroid 是一个用于 Android 应用程序开发的 Kotlin 库。该库提供了一些有用的扩展，以消除 Android SDK 中的样板代码，并专注于生产力。

KAndroid 库是一个很好的选择，因此您可以避免为 SearchView 查询文本更改、ViewPager 实现和 Handler 实现等常见功能编写大量代码。它与 Kotlin 1.2.30 版本兼容。要了解该库的更多信息，请访问此链接。

GitHub-[https://github.com/pawegio/KAndroid](https://github.com/pawegio/KAndroid)

总结一下

所以，这些是十大 Android Kotlin 库，允许 Android 开发者更容易、更快速地开发更强大的 Android 应用程序。除了这些库，如果你认为任何其他 Kotlin 库应该在这个列表中，请在下面评论来分享它。
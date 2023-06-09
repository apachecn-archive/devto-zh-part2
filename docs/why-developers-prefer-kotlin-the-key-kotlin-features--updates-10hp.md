# 为什么开发者更喜欢 Kotlin？Kotlin 的主要功能和更新

> 原文：<https://dev.to/blak_it/why-developers-prefer-kotlin-the-key-kotlin-features--updates-10hp>

Kotlin 是一种静态类型的编程语言，由位于俄罗斯的 JetBrains 公司创建。与 Java 完全兼容，它运行在 Java 虚拟机上，具有可读的语法，并且可以编译成 JavaScript 源代码。

根据[Stack Overflow Developer Survey 2018](https://insights.stackoverflow.com/survey/2018/)，Kotlin 成为全球开发者中第二受欢迎的语言，第四受欢迎的语言。TechBeacon 的记者将 Kotlin 列入了 [5 种前景光明的新兴语言的名单](https://techbeacon.com/5-emerging-programming-languages-bright-future)。

虽然 Kotlin 很年轻，但许多著名公司已经在它上面写了他们的申请。流行的图片分享社交服务 Pinterest 几乎在 Kotlin 第一版发布后就决定离开 Java，并在 2016 年提出了使用它的利弊，甚至在谷歌宣布官方支持 Kotlin 之前。

提供著名国际大学在线课程的教育公司 Coursera 也选择了 Kotlin。在著名产品的创作中应用它的其他例子有优步、Trello、Basecamp 3、Shadowsocks、Evernote、Twidere for Twitter 等。

### 科特林的创作

发明 Kotlin 是为了解决 JetBrains 的开发者挑战。他们需要一个比 Java 更简单的工具来使用他们的主要产品，叫做 IntelliJ IDEA，完全用它来编写。这些人调查了替代方案，仍然不满意，并决定发明他们自己的语言。

我们的目标是获得一个有效的工具，可以在定制的 Android 应用程序开发中与 Java 一起使用，并且可以在 Java 工作的任何地方工作。

Kotlin 的开发始于 2010 年。六年后的 2016 年 2 月，我们看到了第一个 Kotlin 版本的出现——1.0。2017 年，在谷歌 I/O 年会上，Kotlin 被宣布成为 Android 开发的官方语言。

### 科特林的主要特征

Kotlin 提供了许多使用/学习 it 的优势。Java 中也有很多遗漏的东西在它身上实现了:Lambda 表达式+内联函数、智能强制转换、扩展函数、伴随对象、主构造函数、属性等等。

同时，Kotlin 程序员可以访问所有 Java 框架和库，同时有能力编写更清晰简洁的代码。他们可以在构建移动解决方案时使用这两种语言，并在需要时轻松迁移他们的项目。

BLAKIT Android 开发团队的负责人 Artem Korolchuk 命名了 Kotlin 的主要功能:

*   空安全(Null-safety)–摆脱常量 NullPointerException 是 Kotlin 的一大优点。

*   **扩展功能**——这是一个有用的东西，允许程序员给已经存在的类添加功能。它还能使代码更具可读性和逻辑性，摆脱静态的 Utils 类，并编写自己的 DSL。

*   **协同程序**–并发编程变得越来越简单。似乎 Rx 和简单地在流之间切换的能力最近才出现在我们面前。但是在 Kotlin 中，协程使它变得更加容易。希望，在他们的帮助下，开发人员将不再忽视在后台流中执行相对“繁重”的任务，将主要任务留给执行最初分配给它的功能，即处理、更新和与 UI 的交互。

*   还有许多其他有用的东西，包括函数式编程(尤其是关于集合的工作)、委托、函数的默认和命名参数、内联函数……这个列表很长。)

值得注意的是，Kotlin 正在快速发展和改进:更新，或大或小，经常发布。例如，在 **[版本 1.2](https://kotlinlang.org/docs/reference/whatsnew12.html)** 中实现了一些重要的更新。Artem Korolchuk 强调了所做的主要更改:

*   批注@CacheConfig 中的数组文字(cacheNames = ["books "，" default"])
*   检查 lateinit 变量是否已初始化 this::late init var . is 来自显式强制转换的初始化信息用于类型推断 val Button = findViewById(r . id . Button)as Button
*   智能演员改进
*   添加了一组扩展函数，用于操纵可变列表的列表 fill、replaceAll 和 shuffle，以及只读列表的 shuffle

最近，一个提供实质性更新的新版本——kot Lin 1.3——已经发布。你可以在这篇文章中看到[有什么增加和改变。](https://kotlinlang.org/docs/reference/whatsnew13.html)

此外，了解 Kotlin 的优势是什么，以及我们为什么从 Java 迁移到 Kotlin。
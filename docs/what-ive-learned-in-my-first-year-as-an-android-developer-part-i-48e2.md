# 我作为 Android 开发者的第一年学到了什么。第一部分

> 原文：<https://dev.to/miguelrodoma95/what-ive-learned-in-my-first-year-as-an-android-developer-part-i-48e2>

我是一名自学成才的开发人员，我还没有太多的经验，但自从我开始从事软件开发，特别是 Android 应用程序开发以来，去年真的改变了我的生活。

尽管如此，我还是想在这个社区分享我作为开发人员的第一年教给我的东西，并写下我所学到的东西以及我认为在开始 Android 开发时应该考虑的一些重要方面。

我将附上一些对我有帮助的有用资源。

在这篇文章中，我将介绍:

*   入门资源。
*   考虑到不同的屏幕尺寸。
*   编程语言(Java/Kotlin)。
*   布局资源文件夹和类包的命名约定。

如果你是一个更有经验的开发者，或者对 Android 开发和它的最佳实践有更全面的了解，请随意补充和/或分享你如何同意或不同意我在这篇文章中包含的内容。欢迎任何对我或阅读这篇文章的人有帮助的评论！

# 有许多网上资源可供使用

当我决定要为 Android 开发移动应用时，我做的第一件事就是寻找在线课程。这种方法是可行的，你可以按照自己的步调进行，如果你当时不想学，你甚至可以跳过一些课程。我被推荐了 Udemy 的[完整的安卓奥利奥开发者课程](https://www.udemy.com/the-complete-android-oreo-developer-course/)。课程很棒，是针对零编码经验的人(所以当时对我来说很完美)，即使你有经验，也是对 Android 环境很棒的介绍。在我学习这门课程的同时，我也在做一个兼职项目，所以它对我把我学到的概念应用到我的实际应用想法中帮助很大。

我在看这个课程内容的同时，也在深入的学习 [Java:成为一个完整的 Java 工程师！](https://dev.toJava%20In-Depth:%20Become%20a%20Complete%20Java%20Engineer!)这帮助我获得了更深层次的语言知识，而不是仅仅学习 Android 所需的基础知识。

我可以推荐的另一门课程是谷歌免费的用 kot Lin 开发 Android 应用，可以在 Udacity 上找到。这需要一些以前的编程经验和 Android Studio 知识，但它非常简单，涵盖了入门和开发应用程序的基本主题。

YouTube 视频和教程也是学习具体特性和实现的很好资源。几乎总是有一个视频解释如何为你的移动应用程序做你想做的事情。

陷入困境并且很难理解一些事情是这个过程的一部分。我了解到你 95%的问题都已经在 Stack Overflow、GitHub 等网站或者在[官方 Android 开发者](https://developer.android.com/)文档中被问过和回答过了。

# 别忘了考虑不同的屏幕尺寸...

你可能知道有成千上万不同的手机使用 Android 操作系统。即使你意识到了这一点，当你刚刚开始学习和构建应用程序时，这也可能不是你首先考虑的事情。

我制作的第一个应用程序有一个复杂的网格和一些屏幕上的图像，当我制作它时，我在我的手机上测试它，所以我当然是在设计它，所以它在我的手机上看起来很好。
当我在朋友手机里上传的时候，惊喜！我意识到它在其他设备上看起来有多可怕，网格和图像甚至不适合屏幕，这有点令人尴尬。那是我第一次纠结屏幕大小，并意识到这是为 Android 开发应用时需要考虑的一个非常重要的因素。确保选择正确的视图组，并针对不同的设备进行设计。

### 明智地选择你的视图组

一个[视图组](http://tutorials.jenkov.com/android/view-viewgroup.html)是元素(按钮、文本、文本输入、图像等)的容器。)在你的屏幕上，根据你的内容如何组织来选择一个是很重要的。混合大量的视图组或者不正确地使用它们会使你的应用程序在其他设备的屏幕上看起来不一样(有时很糟糕)，这可能是你在设计 UI 时没有考虑到的。

一些最受欢迎的视图组是:

*   线性布局
*   相对布局
*   约束布局
*   框架布局

还有很多，用于其他具体情况。请务必了解它们，并选择适合您的布局。

在 Android Studio 中，你可以选择几个不同的设备来了解你的 UI 在这些设备上会是什么样子。

[![screen_sizes](img/d44c5ecdb6dd4a50eade327b0323b6aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_QIxJ6vj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qyeves3bwodbjrwkpyxv.PNG)

### 为不同的屏幕密度创建 layout.xml 文件

解决这个问题的另一种方法是生成多达 4 或 5 个。相同布局的 xml 文件到[支持不同的屏幕尺寸](https://developer.android.com/training/multiscreen/screensizes)。
比如你有一个 main_activity_layout.xml，你可能要创建的文件有:main_activity_layout.xml(mdpi)、main_activity_layout.xml(hdpi)、main_activity_layout.xml(xhdpi)等。

这使 layout.xml(hdpi)的视图(如按钮)的大小不同于 layout.xml(xxhdpi)中相同按钮的大小。该应用程序将识别设备的密度，并选择所述密度的布局文件。

以下是布局文件在 res/layout 文件夹中的外观示例:

[![This is how your layout files would look like](img/4a1bf64735315030a644ffbc76629f73.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6DO83KJB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/taizym8mz1mntj627g9g.PNG)

# Java 还是 Kotlin？

两者都有利于 Android 开发，我和他们都合作过，各有利弊。然而，如果你是一个编程和/或 Android 开发的初学者，这是我根据我的短期经验推荐的:

### Java 入门

我开始用 Java 编写应用程序，我建议如果你是初学者(像我一样),你也应该这样做。

最主要的原因是 Android 开发还是大量使用 Java，大量的资源还是记录在里面。它帮我用 Java 找到了 Android 开发主题的文档、开源资源和堆栈溢出答案。所以我认为，当你在你使用的语言中找到对你的疑问或文档的支持时，你会更容易熟悉整个 Android 环境。

### 学科特林

当你对 Android 的基本功能和实现感到舒适时，我建议学习和使用 Kotlin，例如处理按钮、侦听器、使用方法、创建类和函数等。

在我看来，Kotlin 是 Android 开发的现在和未来。大多数新文档和工具都是用 Kotlin 编写的。如果你看过最近的 Android 开发峰会，几乎所有的内容都是用 Kotlin 解释的，而且大部分文档都已经更新到 Kotlin 了。

我看到的很多工作职位都在寻找与 Kotlin 一起工作过或者至少有兴趣学习它的开发人员。

Kotlin 已经被证明是一种强大的语言，可以使 Android 应用程序的编码更快、更干净。

# 命名你的类包和布局资源

有一个有组织的项目可能不是 100%直观的。如果你正在做一个大而复杂的项目，你不希望你的课程一个接一个地被混淆。您的类在项目中有不同的功能，有些可能是简单的活动或片段，有些处理您的 http 请求，有些可能是您的 LisViews 的适配器，等等...

你希望你的文件有条理，我将分享我认为这是一个简单而有效的方法来处理一个有条理的项目。

### 为类:

我个人喜欢[这种](https://blog.smartlogic.io/2013-07-09-organizing-your-android-development-code-structure/)组织，在这种组织中，活动、片段、适配器、用于 http 请求的 API 方法和一些用于 helper 方法的 Util 类都有单独的包。这对我保持一个更有组织的项目，以及知道在项目的不同部分可以重用哪些类帮助很大。

[![Class organization](img/8d706db1cad9e1e0bf45bf5a8e3b8216.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2NG6c5Hv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j44gghc2twgw5rkoq2xq.PNG)

### 为布局资源:

上面的链接也包含了我发现的保持布局文件有条理的好方法。由于您不能像 clases 一样将布局文件分隔在包或文件夹中，因此，如果您有许多布局文件，遵循简单的命名约定 activity _ your _ activity _ name . XML 可以帮助您轻松找到它们。

[![Layout Organization](img/698da7c4ab8702a0c783219ec30c38f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--elChXeD2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/isi0xm8kpqf7x85t7ntw.PNG)

# 结论

我发现编写 Android 应用程序非常有趣和有用！我真的享受到了工作时从未有过的快乐。

现在几乎每个人都有智能手机，为世界上任何类型的人创作的想法都非常令人兴奋，无论是游戏还是组织应用程序，或者是解决社会问题或医疗保健问题的东西。可能性是无限的！

成为一名自学成才的开发人员，或者尝试自学一些东西并不容易，但也不一定很难。了解你如何学得最好，不仅从社区和网上资源中寻找支持，也从朋友或同事那里寻找支持，偶尔不知道和/或挣扎没有什么错。记住，你永远不会是房间里最聪明的人，如果你是，那么你可能进错了房间。

坚持学习，快乐编码！
# iOS 快照测试

> 原文：<https://dev.to/apium_hub/ios-snapshot-testing-2i79>

编写用户界面测试总是有点复杂，许多开发人员最终没有进行测试就离开了*视图*，或者花费了大量的时间和精力来开发测试。苹果对 UI 测试有自己的原生支持，但不支持*基于视图的测试*。这种功能的缺乏使得许多人跳过了编写测试的部分。因此，在 [Apiumhub](/) 中，我们决定写一篇关于 iOS 快照测试的文章。

## iOS 快照测试

### 基于视图测试

基于视图的测试意味着验证用户看到的正是我们(作为开发者)希望他们看到的。多亏了这种测试，我们可以保证在不同的状态或版本中，我们的视图能够如预期的那样显示。

### 快照测试。它是如何工作的？

快照测试捕获一个 *UIView* 或 *CALayer* 并使用 *renderInContext* *方法*捕获视图并将其与存储在我们的存储库中的参考图像进行比较。如果两个图像不匹配，则测试失败，并提供第三个显示差异的捕获。

### 配置

该库由脸书(FBSnapshotTestCase)创建，现已更名为*iosnapshottestcase*，由优步维护。

正如他们在 Github 页面上解释的那样，第一步是将库添加到我们的 Podfile 中。

然后，我们编辑目标的方案，添加保存引用捕获的目录。为此，我们添加了一个带有关键字 *FB_REFERENCE_IMAGE_DIR 的环境变量。*

[![spanshot testing](img/f2757925ae709706b0d3134950215dc7.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/Picture1-3.png)

可选地，我们还可以添加关键字 *IMAGE_DIFF_DIR* 来指示将要生成的差异捕获的目录，如果我们的测试失败，将保存该目录。

### 实现

为了查看实现，我们将测试一个简单的 *viewcontroller* ，它将根据状态改变视觉方面。

[![ios testing implementation](img/6e1be0779ce2b54a0e581abece48f8b6.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/Picture2.png)

两个国家的观点是:

[![snapshot testing configuration](img/6727d74223f5d0b099d66a3afce27ffd.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/Screen-Shot-2018-07-15-at-16.53.50.png)

我们通过使测试类成为 *FBSnapshotTestCase* 而不是 *XCTestCase* 的子类来配置我们的测试类。

我们通过实例化 ViewController、分配状态并调用 *FBSnapshotVerifyView* 方法来定义我们的测试。

[![viewController ios](img/f489447d6bf5d481e5a825637ad485e6.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/Picture3.png)

首先，我们必须在激活*记录模式*的情况下执行测试，以保存参考图像。为此我们引入了 setUp () recordMode = true 的方法。

[![recordmode ios](img/aa0da7e54faaaec1e42f641eeea7e867.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/Picture4.png)

在记录模式激活的情况下执行测试时，Fail 将退出。这是正常的，因为你还没有另一个图像来做比较。

[![recordmode ios test](img/d9c99e0cb51d6e030e701d859f2875c1.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/Picture5.png)

然后我们注释或删除 *recordMode* 行，再次启动测试，看看会发生什么。

从现在开始，如果视图的视觉方面以错误的方式改变，我们的测试将会通知我们这个问题。

例如，如果我们不小心改变了图标的尺寸，测试将会生成一个有变化的差分图像。

[![icon mobile app ios](img/1d3272382a2e87b70f30f01f77c7bea0.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/Picture6.png)

我已经为 20px 测试增加了图标的大小，这种变化可以在图像中看到。

### 以隔离方式运行视图

多亏了快照测试和非耦合视图，我们可以在模拟器中实例化并执行某个 ViewController。这对于加速视图创建过程，从而减少开发时间非常有用。

在我们的*difinishlaunchingwithoptions*中，我们检查我们是否正在运行测试，在这种情况下，我们将扁平且空的 *UIViewController* 分配给 *rootViewController* 。

[![doing ios testing mobile app](img/d305fe4a3582f83b2724987c433acb0b.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/Picture7.png)

在我们的测试类中，我们使用一个具有高超时的 *XCTWaiter* ，并将控制器分配给 *rootViewController* 来执行。

[![rootViewController](img/09e4ead975f4a1e7f62091e08ef7e2cd.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/Picture8.png)

最后，在测试中我们调用了 *debugViewController* 方法。

[![](img/a90db9c9b034623dfadac1df169a2895.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/Picture9.png)

通过这种方式，我们可以以一种简单的方式执行应用程序的长屏幕流中的典型视图。

如果你有兴趣获得更多关于 iOS 快照测试或 movile 开发的技巧，我强烈推荐你订阅我们的每月简讯[这里](http://eepurl.com/cC96MY)。

## 如果你觉得这篇关于 iOS 快照测试的文章很有趣，你可能会喜欢…

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

[安卓纯 MVP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)

[Java vs Kotlin:比较和例子](https://dev.to/apium_hub/java-vs-kotlin-5a33)

iOS 快照测试的帖子[首先出现在](https://apiumhub.com/tech-blog-barcelona/ios-snapshot-testing/) [Apiumhub](https://apiumhub.com) 上。
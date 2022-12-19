# 安卓五大自动化测试工具:2018 年 9 月

> 原文：<https://dev.to/bugfenderapp/top-five-automated-testing-tools-for-android-september-2018-5bcm>

[![](../Images/c033feab3082c169f389b9a5dff2eb83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6hvUkds6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/09/automated-testing-tools-featured.jpg)

*这篇文章最初发表在我们的[博客](https://bugfender.com/blog/best-automated-testing-tools-android/)T3 上。*

开发人员是一群挑剔的人。我们在最微小的细节上下功夫，我们产品的每个方面都必须恰到好处。我们不喜欢把我们珍贵的设计托付给我们核心圈子之外的任何人。

然而，我们越来越多地将关键的质量保证(QA)任务委托给机器人。自动化测试产品的市场预计到 2023 年将达到 200 亿美元——是现在的三倍。软件程序越来越复杂，已经从基本的一系列命令行发展到潜在的无限数量的变量，这迫使我们寻求人工智能的帮助。更重要的是，敏捷和 DevOps 的影响力越来越大，这意味着我们处于一个永久的冲刺阶段，我们需要尽可能地加快 QA 过程。

自动化测试比人工测试要快得多，并能产生关键的分析。它让我们可以全天候运行测试，甚至可以同时运行几个测试。也许最重要的是，它使我们，作为开发人员，能够专注于创造性的东西，把我们从最重要的事情中解放出来。不幸的是，可供选择的自动化移动应用程序测试工具多得令人困惑，每一种工具都有自己的乐观营销资料，告诉你这款产品比其他任何产品都更可靠。考虑到其中令人困惑的大量术语和技术用语，很难将真实与虚假区分开来。

自从我们的母公司 [Mobile Jazz](https://mobilejazz.com) 在 2012 年成立以来，我们已经测试了我们的应用程序数千次，我们已经测试了大量的测试工具。因此，我们想编辑一个我们最喜欢的五个自动化移动测试工具的列表，让其他开发者从我们长期(并且经常是痛苦的)的经历中受益。

简单提醒一下:这个列表的目标是 **Android 应用程序自动化测试工具**。我们将在不久的将来为 iOS 编制一份最佳选项列表，因此 iPhone 专家们应该保持关注！

[![](../Images/07bbe0c8c17b763f9d381993ed804a13.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h7NLk17r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/09/automated-testing-tools-appium.jpg)

## 1) [Appium](http://appium.io/)

**它提供了什么:**

*   Android、iOS、Windows 和 Firefox 操作系统平台
*   开放源码
*   无需重新编译应用程序
*   支持跨平台应用测试

这要花多少钱？

它是免费的。完全自由

**它最大的优点是什么？**

*   跨语言和测试系统的通用性
*   巨大的开源社区提供建议和支持

Appium 工具是世界各地应用测试人员的长期最爱，它完全免费，可以通过 GitHub 获得，因此不需要复杂的安装。产品通过开源提供；事实上，建立它的社区——酱实验室——非常活跃，所以它提供了一个获得大量建议和支持的途径。

我们的 QA 专家喜欢 Appium 自动化测试的另一个原因是它的多功能性。该软件适用于任何测试系统，并支持多种方言，包括 Ruby、Java、PHP、Node 和 Python。它的维护成本也很低:标准的自动化 API 意味着不需要以任何方式重新编译你的应用程序。Appium android 测试工具不期望在这个小工具上引入任何东西，它可以很容易地设置在一个备用阶段上。

另一方面，测试可能需要一段时间，因为它们是由远程 webdriver 进行的，并且报告可能缺乏细节。如果你在本地安装 Appium，你的团队必须下载、安装和配置环境，你需要连接一个本地设备——这可能很麻烦。此外，因为它是社区支持的，所以在获取最新的操作系统开发方面比竞争对手慢。

不过总的来说，我们认为 Appium 移动测试在所有关键标准上都表现强劲，其优势适用于广泛的需求。

[![](../Images/09996934faddfc02b2e87f9fdb60c507.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--32ZWBJ0h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/09/automated-testing-tools-robotium.jpg)

## 2) [机器人](https://github.com/RobotiumTech/robotium)

**它提供了什么:**

*   机器人
*   开放源码
*   支持本地和混合应用

这要花多少钱？

它是免费的

它最大的优势是什么？

*   很快
*   它可以同时做几种不同的测试
*   测试是稳健的

作为移动友好测试领域的巨头，Robotium 以其实验案例的速度而闻名，所以如果你试图在敏捷测试环境中进行 QA，这是非常好的。另一个主要的好处是，它只需要很少的信息就能有效地执行，所以您不需要应用程序的法庭知识。

Robotium android 自动化测试可以在几个不同的设备上同时进行，测试可以在模拟器或真实设备上运行。他们也因极其健壮而闻名于，因为 Robotium 通过内部代码引用来识别 UI 元素；如果你已经使用 Java 构建了你的产品，你可以将 Robotium 与 [JUnit](https://www.3pillarglobal.com/insights/mobile-testing-automated-testing-for-android-with-robotium) 测试框架结合起来，以获得更好的结果。然而，也许最酷的事情是 Robotium 可以模拟普通用户的行为，比如触摸、点击和输入文本。

但是，要注意。尽管测试本身实际上是在你可以说“一切正常”之前就已经完成了，但是由于程序源代码的使用，要准备好测试可能要花很多时间。它不能打开手机或平板电脑，也不提供播放或录音功能。

然而，老实说，这是一个非常非常好的选择，会成为许多人的 android 应用程序开发工具列表的首位。考虑到价格(嗯，没有价格)，这对于囊中羞涩的小型开发团队来说是一笔大买卖。

[![](../Images/7bf63c2363d96835f8e58b6ba3fac3c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2841Z2jo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/09/automated-testing-tools-kobiton.jpg)

## 3) [科比顿](https://kobiton.com/)

**它提供了什么:**

*   通过开源构建的基于云的系统
*   可以运行手动和自动测试
*   移动日志、费用、屏幕截图和元数据都是自动创建的

这要花多少钱？

它是免费的，尽管你可以每月花 50 到 300 美元升级。

**它最大的优点是什么？**

*   它允许您在真实设备上进行测试
*   它可以让你进行手工和自动化测试
*   持续更新意味着用户总是领先于潮流

基于云计算的解决方案由 Appium 的开源系统提供，Kobiton 允许你使用真正的手机使用其令人敬畏的功能。您可以执行移动应用程序测试和移动 web 测试运行，并在手动和自动选项之间切换——因此，如果您感到迫切，您可以随时回到老式的动手方法。您还可以使用 Kobiton 的设备实验室管理功能构建自己定制的[测试云](https://kobiton.com/cloud-automation-testing/)。

移动 android 测试套件会持续更新，因此每次移动设备测试时，您都可以获得最新的设备和操作系统版本，并且您可以在不改变内容的情况下对小工具进行交叉测试。移动日志、费用、屏幕截图和元数据都是自动创建的，因此您将拥有可能需要的所有指标。

与其他 android 测试选项相比，有一些缺点。某些命令可能会出现可怕的超时错误，如果使用 IE 11 和 Edge 浏览器，视频流也不支持。此外，某些屏幕设置不能更改，因为它们可能会影响 Kobiton 的设备。

不过，这些都只是次要的诡辩。作为喜欢对新软件更新极客化的早期采用者，我们是超级粉丝。

[![](../Images/15db4dd672699ea74cea9beac29b7003.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6i0Jz3s1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/09/automated-testing-tools-ranorex.jpg)

## 4) [Ranorex](https://www.ranorex.com/)

**它提供了什么:**

*   Android 和 iOS
*   iOS 和 Android 设备的跨浏览器和跨设备测试
*   与 CI 服务器、问题跟踪工具等的简单集成
*   你也可以把吉拉和 Ranorex 工作室整合在一起
*   Ranorex 记录器、代码编辑器、自动化助手和其他有用的工具

这要花多少钱？

免费演示。从 2290 欧元升级到 3990 欧元。[查看更多](https://www.ranorex.com/prices/)。

**它最大的优点是什么？**

*   使用起来真的很简单——你不需要成为一个经验丰富的测试人员或程序员

在我们列表中的所有自动化测试工具中，没有一个比这个工具更简单或适应性更强。如果你不是编程出身，或者你以前从未做过自动化软件测试，Ranorex 可以让你在没有脚本的情况下运行测试[。它很容易](https://www.utest.com/tools/Ranorex)[将](https://www.g2crowd.com/products/ranorex-studio/reviews)与其他测试工具如 TeamCity 和 nCover 集成，并且它还带有[强大的调试能力](https://www.g2crowd.com/products/ranorex-studio/reviews)。

Ranorex 提供全面的跨设备测试，因此您可以尝试网站和桌面应用程序以及移动应用程序，它还为 Chrome、Firefox、Safari 和 Microsoft Edge 等提供了[跨浏览器测试功能](https://www.ranorex.com/)。

弊端？不像我们列表中的其他自动 android 测试工具，它没有开源功能。要运行测试，您需要通过 Wifi 或 USB 连接您的移动设备，这在某些地方可能会很棘手，并且在运行测试之前，您必须在每个移动设备上安装 Ranorex control 应用程序。最后要注意的一点是，与许多竞争对手的测试工具不同，Ranorex 在记录时没有设备查看器，因此您需要访问移动设备。

总的来说，与竞争对手相比，它有一些局限性，但如果你是一个没有多少专业测试经验的小团队，Ranorex 是一个极好的选择。

[![](../Images/af0ccaec1df48fe1c7ec202d62a93e47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2QSHLa23--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/09/automated-tools-calabash.jpg)

## 5) [葫芦](https://calaba.sh/)

**它提供了什么:**

*   对 Xamarin 测试云的访问
*   黄瓜自动化
*   开源开发

这要花多少钱？

零。空。零(那在中文里是‘0’的意思)。基本上是免费的。

**它最大的优点是什么？**

*   它允许您访问 Xamarin 测试云及其数百个设备
*   它支持大量不同的编程语言
*   您可以使用 Cucumber automation 用简单的英语编写您的测试

如果你以前没有听说过 Xamarin 测试云，那么，它可能是自 Game Boy 以来发明的最好的东西。它让用户能够访问云中的 1000 多种不同设备，允许他们在几乎所有智能手机上试用他们的产品。

由 Xamarin 开发和维护的开源产品[葫芦自动化 android 测试工具利用了这项技术的力量。用户被邀请使用支持基于 Ruby 的框架的 Calabash 库来编写他们自己的定制测试用例。然后，他们可以在 Xamarin 测试云上将其付诸实践。](https://calaba.sh/)

然而，对许多人来说，Calabash 更大的优势是它对 Cucumber 的支持，Cucumber 是 Ruby 的一个分支，它使您能够用简单的英语而不是晦涩难懂的代码编写自动化测试。如果你的团队中没有专业的 QA 人员，黄瓜自动化是一个很大的推动。

当然，也有一些限制。Calabash 只支持 Ruby，所以如果你习惯于使用 Java 或 Python 之类的替代品，它可能不适合你。测试也可能很耗时，并且在命令行方面可能有点笨拙和令人沮丧。

但是这些都是小问题。如果您不太了解 QA 专业知识，并且不介意花一些时间来设置测试，我们肯定会推荐 Qalabash。

## 更多选项

这份清单绝非详尽无遗。有几个潜在的替代品可供选择，包括 [UIAutomator](https://google.github.io/android-testing-support-library/docs/uiautomator/) 、 [MonkeyRunner](https://developer.android.com/studio/test/monkeyrunner/index.html) (我们最喜欢的世界上所有自动化 android 测试工具的名字)和 [Selenium](https://www.seleniumhq.org/) 。他们这次可能没有晋级，但他们仍然是很好的选择。

还有一件事我们想提一下…

[![](../Images/0be3ebefe87df2bb79f74134a52f1a2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SQ7cWNzq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/09/automated-testing-tools-bugfender.jpg)

## [挡泥板](https://bugfender.com/)

抱歉，有点自我推销，但我们认为这不会伤害这里的任何人。

Bugfender 是一个跨平台的工具，它超越了正常的崩溃报告工具；它更像是一个常规的 beta 测试工具，可以帮助你识别任何设备上的任何 bug，并给你一个应用程序和用户群的全景视图。

我们很高兴地承认，它在很大程度上仍是一个开发中的产品。目前它还不适用于网络、Windows 或苹果的 tvOS。但我们为它为移动开发团队提供的功能感到自豪。Bugfender 是专门为解决我们作为开发人员每天都会遇到的问题而设计的——最明显的是 Android 碎片化，新设备的指数级增长，这意味着我们现在在设计应用程序和网站时必须考虑数千种不同的智能手机。

Bugfender 不需要任何物理安装——它可以简单地加载到任何用户的设备上，即使它是一个你从未听说过的模糊模型。它 24-7 记录，所以你不只是得到崩溃报告-你得到一个在你的应用的引擎盖下的法庭视图，甚至当事情顺利运行时，你得到使用你的产品的所有设备的故障，这对客户服务是非常好的。

该产品是免费的，尽管我们也有 49 欧元的升级版。如果你想了解更多，请访问 [Bugfender 的网站](https://bugfender.com/)。

*这篇文章最初发表在我们的[博客](https://bugfender.com/blog/best-automated-testing-tools-android/)T3 上。*
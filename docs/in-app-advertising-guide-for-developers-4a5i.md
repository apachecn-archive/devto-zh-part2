# 面向开发者的应用内广告指南

> 原文：<https://dev.to/anadea/in-app-advertising-guide-for-developers-4a5i>

[![In-app advertising](img/55aab9e801ed6ab7faf4a81450c7805a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ju-C97xe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://anadea.info/uploads/image_attachment/image/1538/In-app-ads.jpg)

广告一直是公司的收入来源。有时广告补充订阅收入，其他时候整个业务几乎完全由广告资助。后一种模式是过去开放电波广播公司赚钱的方式。

纯广告的商业模式在互联网上尤其有用，因为人们需要免费的内容。在全球竞争的背景下，有人总是愿意免费提供产品，只要他们能产生广告收入。

不仅如此，自 2008 年 iPhone 问世以来，移动计算发展迅速。根据皮尤研究中心的数据，超过 50%的美国人拥有平板电脑，近 80%的人拥有智能手机。他们每个月还会花相当多的[时间使用这些应用](https://www.statista.com/statistics/323522/us-user-mobile-app-engagement-age/)。

假设你已经决定为你的应用程序实现至少部分收入的广告，你应该在你的构建中考虑什么？

## 广告投放和类型

首先要考虑的是你将在哪里投放广告。这适用于空间和时间位置。你会使用间隙广告吗，比如在一个游戏的关卡之间？如果你没有过渡，比如一本英韩词典，就没有适合插播广告的地方。在后一种情况下，最好是沿着应用程序的边缘连续显示细横幅广告。

你想让横幅广告在每 5 个单词查询后扩展到全屏吗？还是每 10 个？您想扩展到全屏显示吗？这对高收入的广告商来说可能很有吸引力，因为他们可以接触到固定的受众，但这也可能疏远你的用户。

你选择的广告类型也会影响你的投放决定。视频广告当然是更好的间隙，而推送通知可能是最好的全天定期。你也可以对不同的部分使用不同的样式。如果你的应用是一个带有新闻提要的生产力应用，你可能在个人部分有横幅广告，在新闻提要部分有原生广告。

### 平衡您的需求和客户的舒适度

由于广告太多，很多应用程序几乎无法使用。你需要在你的收入需求和你展示的广告数量之间取得平衡，尤其是如果你使用的广告打断了用户的使用。非侵入式横幅广告可能会持续显示，但你显然不能每隔几秒钟就显示一段视频。对于后一种类型，记住数据上限也特别重要:不是每个客户都有无限的数据，如果一个视频广告吸收了 5 兆的数据，许多低上限用户会立即删除你的应用程序。

也避免太招人烦。没有人希望在他们醒来或完成工作时收到来自应用程序的 20 个通知。如果这些通知中有一半只是广告，你就有疏远客户并失去他们信任的风险。

### 使用正确的工具

这对你的理智很重要。有很多工具可以让你保持理智。就开发而言，平台有多个 SDK，如果你想为最广泛的受众开发，你需要管理所有的 SDK。然后是广告收入管理。和分析管理。和营销管理。拥有错误的工具会让你沮丧，最终导致产品故障和客户失望。

### 测试一切

软件开发人员对测试非常熟悉。非开发人员可能认为大多数开发人员花时间敲打键盘来编写数千行代码。开发人员知道他们花了大部分时间盯着他们两天前写的代码，试图找出为什么它不工作。

一旦基础产品完成，测试并没有消失。你需要测试你的广告计划。开发者经常忽略的一个陷阱是广告时机。如果用户浏览你的应用程序的速度比你预期的更快或更频繁，他们得到的广告会比你预期的多得多。这种简单的疏忽会导致客户不满和负面评价。

你还想确保你的广告能在各种设备上引起兴奋(没有什么比把退出按钮从屏幕上移开的插播广告更能让用户血压升高)。它们应该在每一个显示器上都很好看。有了这么多可用的设备，你需要测试很多产品以避免遗漏一些客户。

## 考虑替代收入来源

应用内广告非常普遍。许多应用程序的收入 100%来自广告。然而，这类似于谚语中的鸡蛋篮子:你不应该完全依赖单一的收入来源。考虑其他收入渠道，比如应用内购买或升级版本，为愿意付费的人完全删除广告。

事实上，[应用收入前景](https://amp.businessinsider.cimg/5984879176084a1d008b5721-640-487.png)表明，应用内购买占了非广告收入的绝大部分，而付费安装和订阅只占很小一部分。记住这一点，仔细考虑你的路线多样化你的收入来源。

如果你是一个更喜欢视觉的人，你可以在一个说明性的信息图[这里](https://s3-us-west-2.amazonaws.com/enh-ig/DevelopersGuideInAppAds.png)中看到所有这些。

* * *

这是尼克·安德鲁·罗哈斯的客座博文。

## 我们来连线

如果你喜欢这篇文章，请访问我们的博客:[https://anadea.info/blog](https://anadea.info/blog)或者在[推特](https://twitter.com/AnadeaInc)上关注我们。
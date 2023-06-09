# Android 中的纯模型-视图-演示者

> 原文：<https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736>

Android 中谈纯模型-视图-呈现者的动机:

在软件开发行业中找到一个 Android 开发中架构方面的标准是相当复杂的。的确，一段时间以来，一个非常基本的 [MVP](https://dev.to/apium_hub/mvp-pattern-in-ios-98f) 已经被提出来打破上帝对象(负责绝对一切的活动)，而且，从不久前开始，一个由 Google 提出的使用其架构组件的 MVVM 正在被采用(ViewModel、LiveData、life cycle observer……)。尽管这些架构(尤其是 MVVM)实现了分离组件并使其可测试和可重用的目的，但我们仍然发现了几个问题，让我们问自己这是否是我们可以使用的最佳架构:

1.在第一种情况下，视图和演示者是已知的，演示者和模型也是已知的。关于 MVVM，虽然视图模型不知道视图，但是视图知道视图模型，这使得重用具有不同视图模型的视图变得复杂。依赖项投资原则(DIP)只在一个方向上得到尊重(视图= >服务，而不是服务= >视图)。这个问题之所以存在，是因为在两个方向上的依赖性投资给了我们更大的内聚性和更少的耦合性，但也增加了复杂性。这种权衡是否值得，要由读者来决定。

| |

向两个方向倾斜

|

MVVM

|
|

复杂性

|

+

|

–

|
|

连接

|

–

|

+

|
|

内聚力

|

+

|

–

|

1.  至于层之间的通信，在经典 MVP 的情况下，我们发现它是通过回调来完成的(这最终会将我们的应用程序变成回调地狱)，而在 MVVM 中，使用了 LiveData，尽管它允许我们[避免回调](https://apiumhub.com/tech-blog-barcelona/callback-hell/)，但它没有为我们提供大量操作符来操作数据(在编写本文时，我们只有 map 和 switchmap)。

这些是导致我们在编写应用程序时试图找到更好的解决方案的主要原因。

纯模型-视图-演示者或无控制器架构

在我们的例子中，我们选择了一种称为纯模型-视图-呈现器的架构(我们通常也称之为[无控制器架构](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)),它允许我们完全分离模型层的视图。这要感谢展示者，他的唯一功能是将单个视图与单个应用服务(也称为交互器，或用例)联系起来；因此，为了构建一个屏幕，我们可以使用 N 个演示者，每对视图服务一个演示者。

为了说明我在本文中解释的内容，我在 github(纯模型-视图-展示者)上发布了一个小例子/项目:[https://github.com/apiumhub/mvpp-android](https://github.com/apiumhub/mvpp-android)

因此，我们可以将我们的视图和服务理解为黑盒，它发出事件并接收信息(通过输入和输出“电缆”)，我们的呈现者将负责将一个组件的输入电缆连接到另一个组件的输出电缆，反之亦然。

此外，使用 lambdas 将视图与服务进行通信的事实，允许我们避免直接暴露这些组件之间的可观察性。像这样，我们可以在我们的服务中使用 RxJava，这样我们就可以用本库提供的所有操作符来操作数据；以及视图部分中的 LiveData，这将允许我们进行一个实现，该实现知道我们活动的生命周期中的变化，或者甚至使用 Google 为此提供的 ViewModel。

**视图**

在 Android 的情况下，我们的视图的实现将对应于一个活动或一个片段(我们所看的例子/项目中的片段)，但它可能是一个视图模型，甚至是一个可视组件。这里没有考虑视图是由 N 个片段形成的活动的情况，因为每个片段将有 M 个呈现者，并且我们可以假设视图是这些片段中的每一个，尽管它们然后被分组在一个片段中(或者甚至在另一个片段中)。

**服务**

我们的服务将负责应用所有的业务逻辑，并协调不同的领域服务，或者直接协调负责向应用程序提供/存储信息的存储库。

**基础设施层**

该层负责向应用程序提供数据，或存储必要的数据(网关、BD、SharedPreferences、缓存…)。这些通信通道中的每一个都将使用存储库模式来实现，该模式将被注入到需要它们的服务中。

**我们赢了什么？**

正是由于有了依赖服务= >视图倒置，而不仅仅是依赖视图= >服务，我们才能够做到诸如:

*   运行时在视图中添加/删除更多侦听器
*   同时对多个“监听器”使用同一个视图。例如，当点击一个按钮时，我们必须与服务器通信并启动一个跟踪事件。
*   通过调试我们的应用程序，我们可以在单个站点中看到所有事件流。
*   为了能够应用“呈现者优先”的设计，在视图和服务组件之前，定义两者的接口，比如方法和事件

在为我们的应用程序编写测试时，重要的是我们可以测试孤立的代码单元，这些代码单元不会对应用程序的其他部分产生副作用，并且它们的依赖性可以被模仿(这些代码单元不依赖于应用程序的其他部分)。由于在这个架构中，我们的组件是接收事件和发出信息的黑盒，所以很容易验证当接收到 X 事件时，是否发出了信息 Y。

基础设施层(数据)也很容易测试，因为我们所要做的就是模拟服务器的响应，为此我们将使用 OkHttp 的 MockWebServer。

我们要去哪里？

目前有诸如 Redux 和 Redux-saga 之类的架构，它们在依赖投资的原则下工作，在某种意义上，它们完全是事件驱动的。观察前端架构的演变，认为在 Android 中我们正在接近一个类似于 Redux 的架构是不无道理的。

**改进**

有很多东西可以改进，例如:

*   **ViewModel** :可以在视图和负责保存视图状态的演示者之间添加一个附加层。此外，这个视图模型可以通过数据绑定直接注入到 XML 中，并使用 LiveData 将可视组件绑定到 LiveData 的可观察对象。此外，当我们必须处理适配器时，研究如何进行这种绑定是我们目前正在努力解决的一个挑战。
*   **认证**:Github API 有一个限制，在没有认证用户的情况下，同一个 IP 不能执行超过一定次数的调用(【https://api.github.com/rate\_limit】)；因此，通过 Github 网站用 OAuth2 添加一个基本的身份验证将是开始改进这个应用程序的一个很好的起点
*   **分页**:目前还没有实现分页机制，所以应用程序不会显示超过一定数量的搜索结果，所以在这个架构中实现分页机制可能是一个有趣的挑战。

如果您有兴趣了解更多关于纯模型-视图-展示者的信息，我强烈推荐您点击[此处](http://eepurl.com/cC96MY)订阅我们的每月简讯。

## 如果你觉得这篇关于纯模型-视图-展示者的文章很有趣，你可能会喜欢…

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

Android 中的[纯模型视图演示者的帖子首先出现在](https://apiumhub.com/tech-blog-barcelona/pure-model-view-presenter-android/) [Apiumhub](https://apiumhub.com) 上。
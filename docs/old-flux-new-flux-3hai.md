# 旧焊剂，新焊剂

> 原文：<https://dev.to/apium_hub/old-flux-new-flux-3hai>

如果你不是第一次接触[前端开发](https://apiumhub.com/web-development-barcelona/)，你应该听说过新的 Flux、Redux 和 React，并且很可能尝试过或者直接在项目中使用过。这并不奇怪，因为[React 的受欢迎程度是不可否认的](https://medium.com/javascript-scene/top-javascript-libraries-tech-to-learn-in-2018-c38028e028e6)。React 的成功归功于它的简单性、易用性和一个伟大的社区。

同样真实的是，我敢打赌你们中的大多数人用 [Redux](https://redux.js.org/) 来反应。这两个库如此频繁地结合在一起，很难想象没有 Redux 也能使用 React。然而，信不信由你，React 是由脸书开发的用于构建用户界面的库，而 Redux 是由..丹·阿布拉莫夫和安德鲁·克拉克，**而不是**脸书。脸书有自己的图书馆来做这项工作，这就是 **Flux** 。尽管 Flux 比 Redux 早一年发布，并且来自脸书，React 使用的实际标准是后者。

此外，我只见过少数几个真正使用了 [flux 的包](https://www.npmjs.com/package/flux)的开发者。这总是让我唠叨我是多么怀念“[发布-订阅](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)”本质的简单性，因此在视图中有一个监听器能够对应用程序状态的特定变化做出反应。在我看来，有些东西随着 Redux 而消失了。

为了在 new Flux 中实现这一点，一个商店在处理一个动作(事件)之后，必须“手动”发出一个新的事件，该事件将在视图中被监听，这将允许我们触发一个回调函数来对该变化做出反应。尽管增加了代码的冗长性和一定的复杂性，但避免 Redux 的 *connect* 绑定是一个美丽的权衡，尽管大多数时候工作得很好，但有时会迫使开发人员使用**componentWillReceiveProps**生命周期方法来区分哪些道具实际上发生了变化，这陷入了方法内部的噩梦，以检查*道具*中到底发生了什么变化。

当然，Redux 带来了一些很棒的概念，比如 reducers 是纯函数，不可变状态(一个新的状态被生成，而不是修改现有的状态)，还有一些，在我看来，更多的是关于化妆品和'听起来不错'的语句，比如*单店* === *单一真理来源*。

最近，当回到 Flux 文档时，我惊讶地发现没有提到我之前提到的发布订阅实现。它就这么不见了。相反，所有示例都只引用扩展 ReduceStore 的存储，从' flux/ **utils** 。在做一些调查时，我在 flux 的 github 库上碰到了这个[关闭的问题](https://github.com/facebook/flux/issues/448)。基本上，new Flux 实现了他们自己版本的 Redux，其中存储实现了一个 reduce 方法，这是一个返回存储新状态的纯函数。正因为如此，我认为比较一下 Flux 过去是如何实现的，以及它现在是如何使用的会很有趣。

在这个[公共回购](https://github.com/ropre4/flux_example)中，你有两种数据流的工作示例。在`AppContainer.js`中，你可以发现两个 React 组件`AppViewOld`和`AppViewNew`正在被渲染。要查看完整的实现，请随意克隆存储库并进行试验。在这里，我只想关注视图组件中出现的差异。

在`AppViewNew.js`的情况下:

[![new flux code](img/62d8a72341b3732ef3dfaa69c3b7f8b7.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/new.png)

在这里，我们发现一些带有静态函数 *getStores* 和 *calculateState* 的样板文件，然而，在组件 AddItem 和 ItemList 中，我们将可以通过 this.props 访问这里定义的状态。最重要的是，每当商店更新其状态时，*项*就会神奇地更新。在这个简单的例子中，这看起来是一件很棒的事情，但是当应用程序的复杂性增加时，这会给开发人员带来一些麻烦。

现在，在`AppViewOld.js`的情况下，我们会发现:

[![old flux code](img/60773b7ec30326a92e463d90ec83d7c8.png)T2】](https://apiumhub.com/wp-content/uploads/2018/07/old.png)

这里我们看到更多的样板文件，特别是当我们使用 React 组件生命周期函数手动订阅和取消订阅动作时。然而，代码的易读性更简单。我们看到这个特定的视图会对什么事件(或动作)做出反应，更重要的是，一旦这些动作发生，会对视图产生什么影响。在这种情况下，是函数 *refreshItems* 将唯一的更改应用于视图的状态。此外，我们可以为 ITEM_ADDED_OLD 和 ITEM_REMOVED_OLD 操作设置不同的回调，这两个操作都与存储状态的相同部分进行交互；这是第一种方法所不具备的，在第一种方法中，我们只知道*项*发生了变化，但不知道是否添加了新项，或者是否从列表中删除了某些项。事实上，要真正区分这一点，我们需要使用*componentWillReceiveProps*生命周期函数，如果比较旧状态和新状态的话，会非常难看。

如果你想了解更多关于新通量的信息，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月时事通讯。

## 如果你觉得这篇关于新焊剂的文章有用，你可能会喜欢…

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

[Scala generics III:通用类型约束](https://dev.to/apium_hub/scala-generics-iii-generalized-type-constraints-58km)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

[安卓纯 MVP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)

帖子[老通量，新通量](https://apiumhub.com/tech-blog-barcelona/new-flux/)最早出现在 [Apiumhub](https://apiumhub.com) 上。
# 小心“单一的”相似物(太常见了)

> 原文：<https://dev.to/essentialdeveloper/careful-with-singleton-lookalikes-way-too-common-2hl5>

[https://www.youtube.com/embed/YrLzLiAoOnY](https://www.youtube.com/embed/YrLzLiAoOnY) [在 YouTube 上观看](https://www.youtube.com/watch?v=YrLzLiAoOnY&list=PLyjgjmI1UzlT2jbk9WeqNrZhZyKpPPaWq)

有人告诉过你 Singleton 是一个反模式吗？

嗯，单例可能没问题，但是如果你不小心，它们不仅仅是一个反模式。他们可能很危险！

在这一集里，我们将讨论单例模式的优点和缺点，它的优点和缺点，并展示依赖反转如何通过四个简单的步骤帮助我们保护我们的系统。

我们收到了来自社区成员的以下问题:

> “我一直在寻找一种健壮的方法来编写一个可测试的网络层。而且我总是搞不清楚什么时候用什么时候不用单件。如果你能回答这些问题，我将不胜感激。谢谢视频！”

## 什么是独生子女？

Gamma、Johnson、Vlissides 和 Helm 在 [Design Patterns book (GOF)中描述的 Singleton 模式是一种确保一个类只有一个实例的方法，它提供了对它的单点访问。该模式指定类本身应该负责跟踪它的唯一实例。它还可以通过拦截创建新对象的请求来确保不会创建其他实例，并提供访问唯一实例的方法。](https://www.essentialdeveloper.com/book-suggestions)

此外，为了确保类不能从外部实例化一次以上，singleton 模式禁止声明对模块可见的初始值设定项。

## 小写“s”的 singleton 是什么？

小写“s”的变体称为 singleton，它构成了一个在应用程序的整个生命周期中只实例化一次的类，但是，它的 API 不禁止开发人员创建该类的新实例。约束取决于开发人员的选择或规则，即只实例化它一次。

这种对象的一些例子是苹果的`URLSession.shared`和`UserDefaults.standard`。尽管它们提供了一个共享实例来访问自身的不可变引用，但它们也允许客户通过初始化器来实例化它们。

## 单态 vs .全局可变共享状态

对于单例对象，另一个要点是不要与可变的全局共享状态相混淆。可变全局状态通常由一个类的`static sharedInstance`访问，并允许该引用的访问和变异(`static var`而不是`static let`)。例如，一个全局可变的“上下文”提供了对各种值和引用的访问，如`Date`、`Networking`和`Database`组件。

可变的全局共享状态可能有风险，但在访问整个系统中的对象时提供了易用性和系统环境的简单配置(例如，模仿当前时间、地区或网络响应)。

## 优秀单身候选人的例子

那么我们什么时候应该使用单例模式呢？

当我们恰好需要一个类的实例，并且它必须可以从一个众所周知的访问点被客户端访问时。

例如，一个向控制台记录消息的类是一个很好的选择，因为系统可能需要从任何给定的点访问它，而且我们应该只需要它的公共 API 来记录一些东西，而不是重新创建和改变它的引用。

此外，如果我们需要扩展该类的功能，那么 singleton 模式允许我们在类类型上创建子类或扩展。

## 糟糕的单身候选人的例子

经验法则是决定哪些对象应该只创建一次。单例对象在大多数系统中应该很少见，需要和系统有一对一的关系。这意味着“这是有意义的”，或者对于一个系统来说，*强制*只有一个“这种类型的实例”

例如，视图是糟糕的单例候选对象，因为它们应该能够按需分配和释放内存。这同样适用于各种类型的组件，例如演示者、视图模型和协调者。

## 依存倒置

对于库来说，使用单例对象而不是允许我们实例化内部类来方便它们的使用是一种常见的做法。尽管这种方法为开发人员提供了方便，但它会在系统中产生紧密耦合。打破紧密耦合并保护系统其余部分的一个简单方法是使用依赖反转，而不是直接访问具体的 singleton 实例。通过将第三方依赖隐藏在我们拥有并可以扩展的接口后面(例如，协议/闭包)，我们可以使我们系统的模块不知道另一个系统的实现细节。这种分离可以保护我们的代码库免受外部库的破坏。

正如我们在视频中展示的那样，通过注入和反转共享实例的依赖性，我们的模块可以打破共享实例的紧密耦合和内部依赖性。

## 结论

区分好单身和坏单身是很重要的。

通过不必要的引入单例(特别是作为隐式依赖)，我们可以增加耦合和风险，从而增加引入回归和出错的可能性。最初可能是可以的，但是我们需要积极主动，确定什么时候是反转依赖关系和分离模块的时候了。

现在就订阅我们的 Youtube 频道，每周都能看到**的免费新剧集**。

* * *

最初发表于[www.essentialdeveloper.com](https://www.essentialdeveloper.com/articles/careful-with-singleton-lookalikes-way-too-common)。

## 我们来连线

如果你喜欢这篇文章，请访问我们在[https://essentialdeveloper.com](https://essentialdeveloper.com)的网站，获得更多像这样的深度定制内容。

关注我们:[YouTube](https://youtube.com/essentialdeveloper)[Twitter](https://twitter.com/essentialdevcom)[脸书](https://facebook.com/essentialdeveloper)[GitHub](https://github.com/essentialdevelopercom)
# XCTest + Swift:安装/拆卸 vs 工厂方法

> 原文：<https://dev.to/essentialdeveloper/xctest--swift-setupteardown-vs-factory-methods-385>

[https://www.youtube.com/embed/UpduAlWLliU](https://www.youtube.com/embed/UpduAlWLliU) [在 YouTube 上观看](https://www.youtube.com/watch?v=UpduAlWLliU&list=PLyjgjmI1UzlSWtjAMPOt03L7InkCRlGzb)

在这一集里，我们将深入探讨`XCTestCase`的生命周期，并展示在测试中配置您的系统的不同方法。

与 JUnit 类似，XCTest 在测试用例的单独实例上调用每个测试方法。为了举例说明，假设您有十个测试方法，XCTest 框架将实例化测试用例类的十个实例，并且每个实例只调用其中一个方法。我们相信这是一个很好的设计选择，因为通过在单独的实例中运行测试方法，我们可以避免在测试之间共享状态。然而，`XCTestCase`的行为并不是那么明显，这使得它与我们习惯于使用对象在方法间共享状态的方式相比显得有些违反直觉。

## 雨燕工厂方法

虽然`setUp/tearDown`配置方法是有效的，但是在 Essential Developer 中，我们注意到在大多数情况下，将配置代码转移到工厂方法会产生更好的结果。

以下是我们更喜欢工厂方法而不是`setUp/tearDown`配置的主要原因:

1.  许多测试都有不同的设置/配置，因此共享对象实例化并没有显著的好处。

2.  我们通常使用**构造函数注入**。如果我们在类范围内创建一个类作为属性，我们将不得不使用**属性注入**来为每个测试配置实例。这也很好，但是我们更喜欢构造函数注入，因为我们的大多数类不允许可变性。

3.  我们希望在测试实例下测试系统的整个生命周期，以保证当它从内存中删除时没有问题。我们可以用`setUp()`方法实现这一点，但是为了测试整个生命周期，我们还需要在`tearDown()`中将它设置为`nil`。通过在测试方法范围内创建实例，我们保证它的生命周期在方法内被测试(因为实例将被自动释放)。

4.  做同样的事情需要更多的代码行，在我们看来，这会对测试和生产代码的设计产生负面影响(例如`constructor->property`注入)。

5.  `setUp/tearDown`会使测试更难阅读/理解，因为当阅读代码时，我们必须滚动/跳转范围(从测试方法到`setUp/tearDown`代码)来理解整个上下文。我们更喜欢在尽可能短的范围内保持我们的测试设置(给定/何时/然后或安排/动作/断言)。

## 当我们在 Swift 中使用 XCTestCase 设置/拆卸时

明确地说，我们*避免* `setUp/tearDown`配置是因为我们相信有更好的方式，但是当它对我们有用的时候我们会使用它。

例如，`setUp/tearDown`在处理需要为每个测试重述的全局状态(例如，数据库)时会很有用(因为我们不想让这些细节污染测试方法)。

## 结论

我们希望您积极主动地思考您所面临的问题，以便找到最适合您的解决方案。例如，我们的方法可能对你来说是错误的，所以请提出批评，让我们知道你的首选方法。

现在就订阅我们的 Youtube 频道，每周都能看到**的免费新剧集**。

* * *

我们一直在帮助敬业的开发人员从低薪职位晋升到高层职位——有时只需几周时间！为了做到这一点，我们不断开展并分享关于如何提高你的技能的免费市场研究，同时牢记**同理心、诚信和经济学**。如果你想在事业上更上一层楼，[现在就免费获取我们的最新研究](https://www.essentialdeveloper.com/courses/career-and-market-strategy-for-professional-ios-developers)。

* * *

最初发表于[www.essentialdeveloper.com](https://www.essentialdeveloper.com/articles/xctest-swift-setup-teardown-vs-factory-methods)。

## 我们来连线

如果你喜欢这篇文章，请访问我们在[https://essentialdeveloper.com](https://essentialdeveloper.com)的网站，获得更多像这样的深度定制内容。

关注我们:[YouTube](https://youtube.com/essentialdeveloper)[Twitter](https://twitter.com/essentialdevcom)[脸书](https://facebook.com/essentialdeveloper)[GitHub](https://github.com/essentialdevelopercom)
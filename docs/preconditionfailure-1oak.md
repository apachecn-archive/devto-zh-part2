# 预编码失败

> 原文：<https://dev.to/zeitschlag/preconditionfailure-1oak>

我 2018 年 5 月的目标之一是最终阅读[实用程序员](https://pragprog.com/book/tpp/the-pragmatic-programmer)。我还没有成功，这就是为什么今年，六月是五月，加长版，七月是六月，加长版。

几年前，我的一个好朋友向我推荐这本书，我应该听他的。阅读是如此有趣，我学到了很多东西，而我才刚刚读到一半。我喜欢这本书的一点是，它鼓励你思考你的工作，以及你是如何完成的。它向你展示了良好的做法，但并不傲慢。

因为我在精读这本书，所以做了很多笔记。如果你有兴趣阅读它们，我很乐意在另一篇博客上发表。请告诉我。目前，它们被记在我的老式笔记本上。

在《实用程序员》中，有几条建议。其中第 31 条是:

> 合同设计。

写在合同中的几件事情之一是先决条件。它们基本上是要求或条件，一方必须确保它们是真实的，另一方才能开始履行合同的一部分。合同中应明确规定，哪一方必须确保满足这些要求。

我是一名谋生的 iOS 开发者。几个月前，我的一个前同事给我看了[`assert`](https://developer.apple.com/documentation/swift/1541112-assert)[`assertionFailure`](https://developer.apple.com/documentation/swift/1539616-assertionfailure)[`precondition`](https://developer.apple.com/documentation/swift/1540960-precondition)[`preconditionFailure`](https://developer.apple.com/documentation/swift/1539374-preconditionfailure)。

主要的区别——他至少是这样向我解释的——是`assert(false)`和`assertionFailure`只让应用程序在调试版本中崩溃，而`preconditionFailure`也会导致发布版本崩溃

当我在《务实的程序员》中读到先决条件时，我感觉自己被提醒了我和同事之间的对话。于是我又看了一遍 [`preconditionFailure`](https://developer.apple.com/documentation/swift/1539374-preconditionfailure) 的苹果文档。上面写着:

> 表示违反了前提条件。
> 
> 如果 API 使用不当，当控制流只能到达调用时，使用此函数来停止程序。

接下来，我想知道，Swift 是否有某种合同设计支持，但强大的互联网[说没有](https://stackoverflow.com/questions/31817359/design-by-contract-in-swift#31951965):

[![](img/557fb1e81d1d9b282635a221ed4c4b08.png)T2】](https://i.giphy.com/media/utmZFnsMhUHqU/giphy.gif)

苹果的`precondition`和`preconditionFailure`的含义和 *DbC* 略有不同，就像他们希望我使用它们的方式一样:

> 全局函数`assert`、`assertionFailure`、`precondition`和`preconditionFailure`被设计成在不影响发布构建性能的情况下自由地散布在代码中。— [来源](https://stackoverflow.com/a/31951965/5626642)

所以，是的，作为一个结论，人们可以说，`precondition`*DbC*-方式似乎不同于 *swifty* `precondition`。
# 什么是依赖注入？

> 原文：<https://dev.to/flippedcoding/what-is-dependency-injection-1eem>

web 开发界有太多的行话。老实说，令人惊讶的是，我们竟然能相互理解。(我们真的这样想吗？)这就是为什么我要告诉你依赖注入以及它到底是什么。让我们开门见山吧。

依赖注入的核心，也是我们这样做的原因，是制作服务对象的静态映射。服务对象通常只在应用程序启动时构建一次。关键是一些服务相互依赖，它们的使用方式可能会根据任何环境条件或配置而改变。那么怎么用呢？

依赖注入基本上是将参数传递给一个函数。真的就这么简单。我们喜欢这样做的原因是，这样我们可以在代码中保持职责分离。你惊讶吗？它不是一个框架，也不需要什么花哨的东西。您可以使用构造函数或 setter 方法进行依赖注入。

你进入你的服务类，你想创建一个新的方法。通常情况下，您会将参数留空，并在其中编写您需要使用的所有代码和对象。当你进行依赖注入时，这意味着你传递任何你将要使用的对象并使用它，而不是创建一个本地对象。

这样做的目的是让你能够在方法中使用不同的对象，而不必重写任何代码。一旦你开始测试你的代码，依赖注入变得不可思议，因为你可以在不改变任何东西的情况下传入测试对象！

这是它看起来的样子:

[![dependency injection](img/11c0e273db851a8cf1d0e86c1a4d3e1d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dnq8WNoN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flippedcoding.com/wp-content/uploads/2018/11/dependency-inj.jpg)

那个紫色的盒子可以是任何你想要的东西。这种方法的美妙之处在于，您可以在使用之前对其进行处理。所以如果你需要清理或者移动一些东西，你可以提前做。

我希望这对于依赖注入有所帮助！这并不复杂，它可以并且将会对测试有很大帮助。您使用过 Spring 来处理依赖注入吗？我想听听你喜不喜欢。

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)
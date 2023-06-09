# 实用类型:类型与测试

> 原文：<https://dev.to/stereobooster/pragmatic-types-type-systems-vs-tests-4k3e>

类型和测试有一个共同点，它们防止软件中的错误。

有哪些测试？这是您编写的应用程序，以确保您的实际软件没有某种类型的错误，并确保将来不会引入这样的错误。

什么是类型系统？静态类型检查器是一个应用程序，它检查您的实际软件没有某种类型的错误，并确保将来不会引入这样的错误。

补充说明:结合了错误跟踪软件的动态类型系统也有助于发现错误，但是通常不能防止将来错误的引入。动态类型系统更有助于捕捉错误，它实际上发生在哪里，而不是在堆栈中。你曾经在网页上看到过“NaN 什么的”或者“Blah-blah undefined”显示给最终用户吗？这是因为没有什么可以阻止错误沿着堆栈向下传播。

## bug 预防的类型和测试相比如何？

通过测试，您可以检查几乎任何类型的错误，但是这里的问题是您一次只能检查一件事情。因此，测试戳宇宙的所有可能的错误。
另一方面，类型系统可以检查某些错误的整个类不存在，但它限制在它可以检查的错误类中。根据类型系统的功能和使用方式，它可以检查更多或更少的错误。

简化的模式如下所示:

[![](img/2425b261f30b529cb78e84e351465e3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YIefgqbH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/veud7eoptty10opiig5o.png)

## 一种提高打字系统有效性的方法和其他防错方法

有多种方法可以提高类型系统的有效性，例如:

*   [让不可能的状态变得不可能](https://dev.to/stereobooster/pragmatic-types-making-impossible-states-impossible-inh)
*   [模式匹配的彻底检查](https://dev.to/stereobooster/exhaustive-check-1079)
*   [不透明类型](https://dev.to/stereobooster/pragmatic-types-opaque-types-and-how-they-could-have-saved-mars-climate-orbiter-1551)
*   [IO 验证](https://dev.to/stereobooster/pragmatic-types-io-validation-or-how-to-handle-json-based-apis-in-statically-typed-language-1d9m)
*   类型检查副作用
*   依赖类型系统，如 Agda 和 Idris
*   可能还有其他我忘记或不知道的事情

还有其他方法来防止软件错误，例如:

*   垃圾收集解决了不安全内存操作的问题
*   有限状态机确保没有非法状态或非法转换，这就像状态逻辑正式验证的第一步
*   随着[借用检查器](https://doc.rust-lang.org/1.8.0/book/references-and-borrowing.html)进入 Rust，你可以选择[无畏并发](https://blog.rust-lang.org/2015/04/10/Fearless-Concurrency.html)
*   由于 Pony 语言中的[引用功能](https://tutorial.ponylang.org/capabilities/reference-capabilities.html),它可以进行不间断的垃圾收集，在 actor 模型中零拷贝消息。这有点像类固醇上的借条。
*   不变性防止竞争条件
*   quick check——一个随机测试程序属性的库
*   单向数据流

但是以上这些和形式验证比起来都不算什么，像 TLA+，达芙妮，F 星等等。

[![map](img/0d50b687f2f9cec98c0f6891fdfdf26e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eCzmrGmE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yyjie9qhewoy00nxkcqp.png)

结论:

*   类型不会取代测试——即使您使用了类型检查器，您仍然需要编写测试
*   类型在消除某些类别的错误方面更有效
*   错误预防不仅限于类型检查和测试，还有其他方法来确保您的应用程序中没有错误

## 测试与类型的采用速度

对于库(范围有限)或具有既定约定的项目(如 Ruby on Rails ),测试很容易编写。当您刚开始做一些原型设计时，为新代码编写测试会更加困难。

类型很容易开始，除非您必须处理不可读的错误消息和缺少类型签名(流)。

如果你有使用类型的经验，类型应该比测试更容易被采用，但是请记住，你仍然需要为你的代码编写测试，除非你使用一些高级的东西作为正式的验证。

## PS

TDD 意味着测试驱动开发，但也可以意味着[类型驱动开发](https://www.youtube.com/watch?v=X36ye-1x_HQ)。

[这篇文章是](https://dev.to/t/pragmatictypes)系列文章的一部分。在[推特](https://twitter.com/stereobooster)和 [github](https://github.com/stereobooster) 上关注我。
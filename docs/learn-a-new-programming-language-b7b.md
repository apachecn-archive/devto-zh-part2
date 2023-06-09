# 学习一门新的编程语言

> 原文：<https://dev.to/jonstodle/learn-a-new-programming-language-b7b>

我记得两年前看过一条推特说“你应该每年学习一门新的编程语言”。我的第一个想法是，我不需要新的编程语言，我对 C#很满意。我曾经用 JavaScript 和 TypeScript 做过一些小项目，甚至还做过一个 PHP 教程，但是没有被吸引住。

然而，这句话一直困扰着我，大约一年半前，我在制作一个 iOS 应用程序时，最终学会了 Swift 3。我从学习 Swift 3 中学到的经验让我相信“每年学习一门新的编程语言”这句箴言。

学习新的语言帮助我对已经掌握的语言有了新的认识。这让我更加欣赏 C#，教会了我新的编程模式，让我明白 C#远非完美。

以下是我在学习新语言时遇到的一些事情:

## 迅捷(3)

Swift 几乎成了我新喜欢的语言。感觉很像 10 年后设计 C#。自从 C#首次发布以来，它已经增加了许多特性，在 Swift 中，它们已经自成一体。

例如，类型推断。你只需要在编译器想不通的时候声明变量的类型。为了清晰起见，您可以随时添加类型声明，但这取决于您。

在 C#中，有扩展方法。它们允许您向现有的类添加新的功能。另一方面，在 Swift 中，您可以用新属性、新方法扩展类，并使类实现协议(接口)。

Swift 还要求抛出异常的方法用关键字进行标记。无论何时调用这些方法，都必须使用一个`try`子句。开始时感觉有些冗长，但有助于避免未捕获的异常导致应用程序崩溃。

我也最终喜欢上了显式参数名，尽管一开始我不喜欢它们。它们帮助代码自我文档化，并使方法和函数更容易阅读。

还有，斯威夫特的 enums 很牛逼。

## PHP

我以前讨厌 PHP，因为它很酷，现在我不喜欢 PHP，因为我用过它。除了它是一次冒险之外，我没有太多关于 PHP 的好话要说。

我试图做一些显而易见的事情；比如获取数组的长度。我寻找一个叫做`length`或`count`的属性，但是没有运气。我要找的是全局函数`sizeof`，它是全局函数`count`的别名。那里可能有一些奇怪的历史。

哦，PHP 中的数组实际上是字典，或者地图。如果您删除一个项目，这有一个很好的副作用，即弄乱索引。如果你有一个包含三个条目的数组，索引分别为`0`、`1`和`2`，去掉第二个条目(`1`，你现在就有一个包含两个索引的数组:`0`和`2`。

在某个时候，我学会了微笑，耸耸肩，说“这是 PHP，为什么不呢？”当我遇到这些东西的时候。

## 巨蟒

2017 年我用 Python 做了一个小项目。虽然我写的代码不超过几百行，但我发现了对合适的参数解析器的欣赏。Python 用于解析命令行参数的库让我再也不想手工解析了。

## 镖

目前我正在尝试一些 Dart，并且我已经发现了这种语言中我喜欢的一些东西。

所有类都实现一个与类相同的接口。这使得在测试您的代码时交换实现成为可能，或者您可以使您的自定义类符合另一个类，使它们可以互换。

Dart 还删除了访问修饰符。没有`private`或`public`。如果你用一个前导下划线(`_myProperty`)定义一个类、方法或属性，它是私有的。

* * *

我鼓励你尝试一种新的语言——你可能会找到你喜欢的东西。最糟糕的情况是，你最终会更加喜欢你最喜欢的语言。

编码快乐！
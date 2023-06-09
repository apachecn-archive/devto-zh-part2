# 构建 PHP 框架:第 3 部分——行动的时候到了

> 原文：<https://dev.to/mattsparks/building-a-php-framework-part-3---time-for-action-9pl>

在本系列的第 2 部分中，我讨论了什么是 web 框架，以及它们是如何工作的。现在是时候迈出实际构建框架的第一步了。

## 建筑模式还是设计模式？

在上一期文章的末尾，我提到我已经决定使用一种不同于 MVC 的架构模式。这不是对 MVC 的轻视，我只是想尝试一种不同的方法。为此，我研究了 MVC 的替代方案及其利弊。

我不会对每个模式都进行详细的描述，但是在我的研究中，我注意到了一些奇怪的事情(至少对我来说是这样的):MVC 一直被称为“架构模式”和“设计模式”来自[维基百科](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller):

> “MVC 设计模式将这些分离开来……”

来自同一篇维基百科的文章，下一段实际上是:

> “这种架构在以下方面很受欢迎…”

那么是什么原因呢？

这是不是所有的设计模式都是架构模式，但不是所有的架构模式都是设计模式？

也许人们只是互换使用这些术语？

为了进一步迷惑自己，我阅读了这两种模式的维基百科页面。下面是“[架构模式](https://en.wikipedia.org/wiki/Architectural_pattern)页面的第一句话:

> “架构模式是一种通用的、可重用的解决方案，用于解决给定环境下软件架构中常见的问题。”

下面是“[设计模式](https://en.wikipedia.org/wiki/Software_design_pattern)”:

> 在软件工程中，软件设计模式是对软件设计中给定上下文中常见问题的通用、可重用的解决方案

好吧，那没什么用。

幸运的是，一个被称为 pyfunc 的善良灵魂，在 2010 年 11 月 22 日回答了一个[栈溢出问题](https://stackoverflow.com/questions/4243187/whats-the-difference-between-design-patterns-and-architectural-patterns#4243407)。他的部分回答是:

> “设计模式通常与代码级别的共性相关联。它为细化和构建更小的子系统提供了各种方案。通常会受到编程语言的影响。由于语言范式，一些模式变得无足轻重。设计模式是中等规模的策略，它充实了实体的一些结构和行为以及它们之间的关系。
> 
> 而架构模式被视为比设计模式更高层次的共性。架构模式是高级策略，涉及大规模组件、系统的全局属性和机制…
> 
> …如果你遵循了上面的想法。您将看到 Singleton 是一种“设计模式”,而 MVC 是处理关注点分离的“架构”模式之一。"

MVC 是架构模式，不是设计模式！许多其他人[也同意这个评价](https://coderanch.com/t/169944/engineering/Difference-Design-Pattern-Architecture)。我说维基百科是错的吗？我不是说他们是对的。

## 这么多建筑模式

解决了这些混乱之后，让我们回到手头的任务上来。

我不会使用 MVC 模式进行分析。为了决定用什么来代替，我研究了许多选择。我浏览了一堆 MVC 派生:模型-视图-适配器，模型-视图-呈现者，模型-视图-视图模型。我阅读了关于表示-抽象-控制，首先是演示者，以及许多其他的内容。在我遇到[资源-方法-表示](http://www.peej.co.uk/articles/rmr-architecture.html)之前，没有什么真正激起我的兴趣。

早在 2008 年，RMR 就被保罗·詹姆斯定义了。他的目标是概述一种以 RESTful 方式构建 web 应用程序的方法。本质上，资源——比如说，cupcakes——被映射、建模并响应标准的 HTTP 方法(GET、POST、PUT 等)。).因此，要得到一个 id 为 42 的纸杯蛋糕，您的路线应该是这样的:

```
GET http://example.com/cupcakes/42 
```

Enter fullscreen mode Exit fullscreen mode

我敢打赌你会问，“那这和其他模式有什么不同呢？在拉勒维尔，我可以走同样的路线。”

确实如此，但是路线不是重点。

使用 MVC，你可能会有一个指向控制器上的方法的路径。坚持使用 Laravel 示例:

```
Route::get(‘cupcakes/{id}', ‘CupcakeController@index’); 
```

Enter fullscreen mode Exit fullscreen mode

从那里，该方法将从纸杯蛋糕模型中提取数据，并最终发送回某种响应。

RMR 认为这不是展示资源的最佳方式。在 RMR，您将拥有一个带有对应于标准 HTTP 方法的方法的 Cupcake 资源。每个请求几乎都是自动路由到那个资源的，因为它会直接映射到所使用的 HTTP 方法。不需要明确定义路线。做一个 GET 请求，你会得到甜蜜的纸杯蛋糕数据，做一个 DELETE 请求，纸杯蛋糕就不存在了。

太好了，那我就用 RMR 了？不完全是，但算是吧。

## 动作域响应者

在深入研究 RMR 时，我偶然发现了由另一个保罗创建的[动作域响应器](http://pmjones.io/adr/)，这次是“ [M .琼斯](http://paul-m-jones.com/)的变种。

ADR 和 RMR 非常相似。事实上，[琼斯注意到](https://github.com/pmjones/adr/blob/master/OBJECTIONS.md#resource-method-representation):

> ADR 可以被认为是 RMR 的扩展或超集变体，其中资源和可以对其执行的动作被清楚地分成域和动作，并且表示(即，响应的构建)由响应者处理。"

我喜欢这样。我想对 ADR 有更多的了解和理解，所以我将在 Analyze 中使用它。

在下一篇文章中，将会编写代码。

[最初发布于 DevelopmentMatt.com](http://developmentmatt.com/building-a-php-framework-part-3-time-for-action/)
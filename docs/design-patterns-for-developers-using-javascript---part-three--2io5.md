# 使用 JavaScript 为开发人员设计模式——第三部分

> 原文：<https://dev.to/olivermensahdev/design-patterns-for-developers-using-javascript---part-three--2io5>

# 结构设计模式

这是面向使用 JavaScript 的开发人员的设计模式系列文章的第三部分。如果你错过了前几章，请阅读第一部分的[和第二部分](https://dev.to/omensah/design-patterns-for-developers-using-javascript----part-one--b3e)的[。在本文中，我们将讨论结构设计模式。有了这些模式，它关注的是
对象是如何组成的，是什么组成了这些对象，以及如何简化它们之间的关系。如果你还记得创建设计模式，我们只对创建一个对象的实例感兴趣。结构模式处理关系。它帮助一个物体与另一个物体联系起来；这可以通过两种方式实现:通过扩展功能或简化功能。一些常见的结构设计模式有:Decorator 模式，它扩展了对象的功能、Flyweight 模式和 faade 模式；这两者都有助于简化功能。](https://dev.to/omensah/design-patterns-for-developers-using-javascript---part-two--3p39)

## 装饰图案

装饰模式用于向现有对象添加新功能，而无需修改依赖于它们的底层代码。理解如何获取一个现有的对象并在它周围包装新的特性是非常重要的，因为如果理解得不够，你甚至可以使应用程序比以前更复杂。假设您有一个任务，并且您想要另一个称为紧急任务(对象)的任务，它具有通知您截止日期的新功能，我们可以利用现有的任务并对其进行修饰，以获得我们想要新任务做的任何事情。

让我们开始吧:
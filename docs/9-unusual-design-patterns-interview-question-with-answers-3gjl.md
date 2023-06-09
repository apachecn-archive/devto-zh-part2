# 9 个不寻常的设计模式面试问题(带答案)

> 原文：<https://dev.to/aershov24/9-unusual-design-patterns-interview-question-with-answers-3gjl>

[![9 Unusual Design Patterns Interview Questions and Answers](img/ad3b5490ec3880edc4412e226c4c0061.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FNtQGuO_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/678117/pexels-photo-678117.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D350) 
在软件工程中，设计模式是对软件设计中常见问题的通用可重复解决方案。设计模式不是可以直接转换成代码的成品设计。它是如何解决一个问题的描述或模板，可以在许多不同的情况下使用。

> 最初发表于 [FullStack。永远不要再错过你的技术面试](https://www.fullstack.cafe)

### Q1:什么是设计模式，为什么每个人都应该使用它们？

> 主题:**设计模式**
> 难度:⭐

设计模式是对软件开发过程中最常遇到的问题的一种很好的解决方案。

设计模式代表了经验丰富的软件开发人员在一段时间内发展出来的最佳实践。它们促进了可重用性，这导致了更健壮和可维护的代码。

🔗**来源:**【www.educba.com】T2

### Q2:什么是滤镜图案？

> 主题:**设计模式**
> 难度:⭐⭐

**过滤模式**或**标准模式**是一种设计模式，使开发人员能够使用不同的标准过滤一组对象，并通过逻辑操作以去耦的方式将它们链接起来。这种类型的设计模式属于*结构*模式，因为这种模式结合了多个标准来获得单个标准。

**过滤器设计模式**在您想要动态添加过滤器或者您正在实现多种功能，并且其中大多数功能需要不同的过滤标准来过滤某些内容时非常有用。在这种情况下，您可以创建过滤标准并在任何需要的地方重用它，而不是在功能中硬编码过滤器。

考虑:

```
List<Laptop> laptops = LaptopFactory.manufactureInBulk();
AndCriteria searchCriteria = new AndCriteria(
  new HardDisk250GBFilter(), 
  new MacintoshFilter(), 
  new I5ProcessorFilter());
List<Laptop> filteredLaptops = searchCriteria.meets(laptops); 
```

🔗**来源:**【tutorialspoint.com】T2

### Q3:什么是策略模式？

> 主题:**设计模式**
> 难度:⭐⭐

在**策略模式**中，一个类的行为或者它的算法可以在运行时改变。这种类型的设计模式属于*行为*模式。

在策略模式中，我们创建表示各种策略的对象和一个上下文对象，该对象的行为根据其策略对象而变化。策略对象改变上下文对象的执行算法。

[![](img/7c22c57ae10b86ae9aadc50444fcc710.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SsTXCyL9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/4/45/W3sDesign_Strategy_Design_Pattern_UML.jpg)

🔗**来源:**【tutorialspoint.com】T2

### Q4:什么是观察者模式？

> 主题:**设计模式**
> 难度:⭐⭐⭐

**观察者模式**(也称为*发布-订阅模式*)在对象之间存在一对多关系时使用，例如如果一个对象被修改，它的依赖对象将被自动通知。观察者模式属于*行为*模式类别。

与对其状态感兴趣的其他对象具有一对多关系的对象称为*主体*或*发布者*。每当*主体*的状态发生变化时，*观察者*就会收到通知，并采取相应的行动。*主题*可以通知任意数量的从属*观察者*，任意数量的*观察者*可以订阅*主题*以接收此类通知。

观察者模式使用两个参与者类:

*   Observer (os Subscriber)抽象类提供了一个`update()`方法，主题将调用该方法来通知它主题的状态变化。
*   Subject(或 Publisher)类也是一个抽象类，它定义了四个主要方法:`attach()`、`detach()`、`setState()`和`notify()`

[![](img/75c559f5ee27bde4c97975ae44e2abcf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TZ1lgrm8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/0/01/W3sDesign_Observer_Design_Pattern_UML.jpg)

🔗**来源:**【sitepoint.com】T2

### Q5:为什么我要使用责任链而不是装饰者？

> 主题:**设计模式**
> 难度:⭐⭐⭐⭐

关键的区别在于,**装饰器**增加了新的行为，实际上拓宽了原来的接口。这类似于普通扩展如何添加方法，除了“子类”只通过引用耦合，这意味着任何“超类”都可以使用。

**责任链**模式可以修改现有的行为，这类似于使用继承来覆盖现有的方法。你可以选择打电话给`super.xxx()`继续沿着“链条”向上，或者自己处理信息。

🔗**来源:**【stackoverflow.com】T2

### Q6:你什么时候会使用构建器模式？为什么不使用工厂模式呢？

> 主题:**设计模式**
> 难度:⭐⭐⭐⭐

当设计构造函数或静态工厂有很多参数的类时，*构建器模式*是一个很好的选择。

考虑一家餐馆。“今天的饭”的创建是一种工厂模式，因为你告诉厨房“给我今天的饭”，厨房(工厂)根据隐藏的标准决定生成什么对象。

如果您点了一份定制披萨，构建器就会出现。在这种情况下，服务员告诉厨师(建造者)“我需要一个披萨；再加奶酪、洋葱、培根！”因此，构建器公开了生成的对象应该具有的属性，但隐藏了如何设置它们。

🔗**来源:**【stackoverflow.com】T2

### Q7:桥模式和适配器模式有什么不同？

> 主题:**设计模式**
> 难度:⭐⭐⭐⭐

*适配器模式*的目的是使一个或多个类的接口看起来与特定类的接口相同。

*桥模式*被设计成将一个类的接口从它的实现中分离出来，这样你就可以在不改变客户端代码的情况下改变或替换实现。

🔗**来源:**【tutorialspoint.com】T2

### Q8:依赖注入和服务定位器模式有什么区别？

> 主题:**设计模式**
> 难度:⭐⭐⭐⭐⭐

*   有了 **ServiceLocator** ，这个类仍然负责创建它的依赖项。它只是使用服务定位器来做这件事。

*   **服务定位器**隐藏依赖关系——你不能通过查看一个对象来判断它是否命中数据库(例如)当它从定位器获得连接时。

*   使用 **DI** ，类被赋予它的依赖项。它既不知道，也不关心他们从哪里来。

这样做的一个重要结果是 DI 示例更容易进行单元测试——因为您可以向它传递其依赖对象的模拟实现。如果您愿意，您可以将两者结合起来，并注入服务定位器(或工厂)。

🔗**来源:**【stackoverflow.com】T2

### Q9:解释门面、代理、适配器和装饰设计模式之间的区别？

> 主题:**设计模式**
> 难度:⭐⭐⭐⭐⭐

*   适配器使给定的类/对象适应新的接口。在前一种情况下，通常采用多重继承。在后一种情况下，对象被一个符合的适配器对象包装并传递。我们在这里解决的问题是不兼容的接口。
*   门面(Facade)更像是通向一组复杂功能的简单门户。你做了一个黑匣子，让你的客户少担心，也就是说，让界面更简单。
*   代理提供了与 proxied-for 类相同的接口，并且通常自己做一些日常工作。(因此，不是为一个重对象 X 制作多个副本，而是为一个轻量级代理 P 制作副本，它反过来管理 X 并根据需要翻译您的调用。)您解决了客户不得不管理沉重和/或复杂的对象的问题。
*   装饰器用于给你的对象添加更多的火药(注意术语对象——你通常在运行时动态地装饰对象)。您不会隐藏/削弱对象的现有接口，而只是在运行时扩展它

🔗**来源:**【stackoverflow.com】T2

> 谢谢🙌阅读，祝你面试好运！
> *查看更多 FullStack 面试问题&答案上👉 [www.fullstack.cafe](https://www.fullstack.cafe)*
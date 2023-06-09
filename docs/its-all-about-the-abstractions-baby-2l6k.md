# 这都是关于抽象的，宝贝

> 原文：<https://dev.to/kritner/its-all-about-the-abstractions-baby-2l6k>

*[的《抽象插画】隆费尔特](https://unsplash.com/@artbylonfeldt?utm_source=medium&utm_medium=referral)的艺术[的 Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)T5】*

我一生都是一名开发人员，职业生涯大约有 10 年了。在那 10 年里，我觉得我总是能够“完成工作”，但一旦我接受了抽象概念，事情真的开始对我产生影响。

### 什么是抽象？

来自[维基百科](https://en.wikipedia.org/wiki/Abstraction_(computer_science)):

> [抽象，一般来说](https://en.wikipedia.org/wiki/Abstraction)，是计算机科学和[软件开发](https://en.wikipedia.org/wiki/Software_development)的一个基本概念。抽象的过程也可以称为**建模**，与 [*理论*](https://en.wikipedia.org/wiki/Theory) 和 [*设计*](https://en.wikipedia.org/wiki/Design) 的概念密切相关。[模型](https://en.wikipedia.org/wiki/Conceptual_model)根据其对[现实](https://en.wikipedia.org/wiki/Reality)方面的概括，也可以被认为是抽象的类型。

嗯，那对我来说并不太清楚。标签对我来说很难，但我倾向于通过定义“需要发生什么”来将抽象视为概念建模，而不是“事情需要如何发生”。

抽象是概念，不是细节。人们每天都在使用抽象，而不一定理解抽象如何工作的细节。一个简单的例子可能是汽车油门踏板的抽象概念。你可能不知道发动机的内部是如何工作的，但你知道踏板背后的想法是“让汽车跑起来”。对于抽象的消费者来说，抽象如何完成它想要做的事情的细节并不重要，重要的是它做到了。

### 为什么抽象很重要？

抽象是一种组织的手段，而组织是干净的、易于遵循的代码所需要的！完成抽象的传统方法是利用:

*   接口
*   抽象类(不可能！)
*   简单模型类(理想情况下没有行为的属性)
*   目标
*   具有行为的类(尽管从设计的角度来看，可能是最不“好”的抽象类型？)

是的，上面的列表确实包含了大多数语言特性，但是从高层次抽象的角度来看，一些要点可能比其他的更有用。

我发现接口和模型类是对一个概念建模的最有用的工具，因为它们允许你，甚至强迫你以更小的块来考虑问题。如前所述，接口都是关于定义方法签名的——需要完成“什么”,而不是“如何”。

举个例子:

*   我需要基于一些标准的一系列我的对象。

有了(不太好的)抽象层次，您可以创建一个类，比如:

```
public class MyObjectDbRetriever
{
 public IEnumerable<MyObject> GetMyObjects(MyObjectCriteria criteria)
 {
  // Connect to a database
  // Do some sql-ey stuff that limits the result set based on criteria

  return results;
 }
} 
```

虽然上面的代码工作得很好，并将成为我们“最终结果”的一部分，但从调用者的角度来看，它并不是最棒的代码。

调用者如何使用这些代码？

```
public class MyObjectController
{
 public IEnumerable<MyObject> GetMyObjects()
 {
  // make up some criteria
  var myObjectCriteria = new MyObjectCriteria()
  {
   // some criteria
  }

  // Get the data
  var myObjects = new MyObjectDbRetriever()
   .GetMyObjects(myObjectCriteria);
 }
} 
```

在上面的类中，您的调用者与`MyObjectDbRetriever`紧密耦合。为什么这样不好？几个原因:

*   测试控制器很困难(如果有足够的逻辑可以测试)，因为控制器直接引用了`MyObjectDbRetriever`。这意味着，没有数据库逻辑，就没有对控制器逻辑的测试——这使得单元测试变得非常不可能。
*   与上述相关，但我认为我还是应该指出来；控制者与“如何”而不是“什么”联系在一起。“我需要使用`MyObjectCriteria`从数据库中获取`MyObjects`，而不是“我需要使用 MyObjectCriteria 的`MyObjects`”

### 为什么恰当的抽象能帮助你写出更好的代码？

从上面的`MyObject`例子开始，让我们改变一些事情。让我们给我们的抽象引入一个“什么”，而不仅仅是当前的“如何”。

```
public interface IMyObjectRetriever
{
 IEnumerable<MyObject> GetMyObjects(MyObjectCriteria criteria);
} 
```

现在，我们有了一个契约，一个想法，一个“什么”的抽象概念。我们现在可以修改原始的`MyObjectDbRetriever`来利用这个接口:

```
public class MyObjectDbRetriever : IMyObjectRetriever
{
 public IEnumerable<MyObject> GetMyObjects(MyObjectCriteria criteria)
 {
  // Connect to a database
  // Do some sql-ey stuff

  return results;
 }
} 
```

这与之前使用的类完全相同，只是现在它实现了我们的想法`IMyObjectRetriever`。你可能会问，这如何改变控制器上的东西？以一种非常有趣的方式！既然我们是对接口编程，而不是具体化，依赖注入就成了我们的一个选择。

依赖注入是实现可靠设计原则中的“D”的一种方式——依赖倒置。其基本思想是高层模块(控制器)不应该依赖于低层模块(T0)。在第一个例子中，这个原则显然被违反了，但是现在发生了什么变化来防止它呢？

我们再来看看原文`MyObjectController`

```
public class MyObjectController
{
 public IEnumerable<MyObject> GetMyObjects()
 {
  // make up some criteria
  var myObjectCriteria = new MyObjectCriteria()
  {
   //
  }

  // Get the data
  var myObjects = new MyObjectDbRetriever()
   .GetMyObjects(myObjectCriteria);
 }
} 
```

在上面,“高级模块”控制器非常依赖于`MyObjectDbRetriever`的“低级模块”。利用我们的新接口和构造函数依赖注入，我们可以改变这一点！

```
public class MyObjectController
{
 private readonly IMyObjectRetriever _myObjectRetriever;

public MyObjectController(IMyObjectRetriever myObjectRetriever)
 {
  _myObjectRetriever = myObjectRetriever;
 }

 public IEnumerable<MyObject> GetMyObjects()
 {
  // make up some criteria
  var myObjectCriteria = new MyObjectCriteria()
  {
   //
  }

  // Get the data
  var myObjects = _myObjectRetriever.GetMyObjects(myObjectCriteria);
 }
} 
```

在上面的实现中，只有很少的东西发生了变化，尽管它们是非常重要的变化！现在，我们有了一个接受`IMyObjectRetriever`实现的构造函数。函数`GetMyObjects`现在调用`GetObjects(myObjectCriteria)`的接口方法，而不是具体的 db 方法。控制器类*不再依赖于`MyObjectDbRetriever`或数据库*！现在，控制器类只是简单地依赖于可以检索数据的接口的想法，它如何做对控制器的上下文并不重要——松耦合！

如果调用我们控制器的东西根据返回数据的性质有不同的行为，该怎么办？上述变化意味着我们现在可以更容易地通过使用模拟、伪造或垫片来测试控制器。以前，当使用`MyObjectDbRetriever`时，我们必须确保我们的数据库从我们的实际数据库中返回特定的数据需求，用于几种潜在的场景。现在作为一个例子，我们可以抛出一些实现我们的接口的其他类，并根据我们的测试需求返回数据。

```
public class MyEmptyFakeObjectRetriever : IMyObjectRetriever
{
 public IEnumerable<MyObject> GetMyObjects(MyObjectCriteria criteria)
 {
  return new List<MyObject>();
 }
}

public class MyNullFakeObjectRetriever : IMyObjectRetriever
{
 public IEnumerable<MyObject> GetMyObjects(MyObjectCriteria criteria)
 {
  return null;
 }
}

public class MyKritnerFakeObjectRetriever : IMyObjectRetriever
{
 public IEnumerable<MyObject> GetMyObjects(MyObjectCriteria criteria)
 {
  return new List<MyObject>()
  {
   new MyObject("kritner")
  };
 }
} 
```

因为上面所有的类都实现了`IMyObjectRetriever`,所以只需传入我们的假类实例来测试我们的特定场景。在这种情况下，我通常会使用“[模仿](https://dev.to/kritner/getting-started-with-unit-testing-and-moq--part-1-5bo6)”，但这些模仿也很容易演示。

我觉得这只是触及了抽象的表面，但希望这能帮助其他人拥有“点击”时刻！

相关:

*   [开始单元测试和 Moq](https://medium.com/@kritner/what-is-the-business-value-of-unit-testing-part-1-740bce50fc34)
*   单元测试的商业价值是什么？
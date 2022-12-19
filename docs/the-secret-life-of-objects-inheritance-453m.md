# 对象的秘密生活:继承

> 原文：<https://dev.to/riccardo_cardin/the-secret-life-of-objects-inheritance-453m>

*原贴于:[大泥球](http://rcardin.github.io/)T3】*

这篇文章是关于面向对象编程基本概念的系列文章的第二部分。第一篇文章关注的是信息隐藏。这次，我重点介绍一下*传承*的概念。尽管大多数人认为这很容易理解，但是继承是一个很难正确掌握的知识。它包含了许多其他的面向对象编程原则，这些原则并不总是与它直接相关。此外，它与对象间最有效的依赖形式之一直接相关
。

除此之外，许多文章和帖子强烈反对面向对象编程及其原则，只是因为他们不理解它们。其中一个帖子是[再见，面向对象编程](https://medium.com/@cscalfani/goodbye-object-oriented-programming-a59cda4c0e53)，它以非常偏见和天真的眼光对待继承和其他面向对象原则。

所以，让我们开始这个聚会，让我们一劳永逸地揭开面向对象编程中的继承之谜。

## 定义

分析一个概念的一个很好的起点是它的定义。

> 继承是一种语言特性，允许从现有对象定义新对象。

假设每个对象都有一个类

> 对象的类定义了对象是如何实现的，即内部状态和操作的实现。[..相反，一个对象的类型仅指它的接口，即它可以响应的请求集。

所以，

> 理解类继承和接口继承(或子类型化)之间的区别很重要。类继承根据另一个对象的实现来定义一个对象的实现。简而言之，这是一种代码共享的机制。相反，接口(或子类型)描述了一个对象何时可以代替另一个对象使用。

## 天大的误会

到目前为止，我们已经了解了有两种类型的继承:类继承和接口继承(或子类型)。当你使用前者时，你正在执行*代码重用*，也就是说你知道一个类是你需要的一段代码，你正在从它扩展以重用那些代码，重新定义所有你不需要的东西。

```
class AlgorithmThatReadFromCsvAndWriteOnMongo(filePath: String, mongoUri: String) {
  def read(): List[String] = {
      // Code that reads from a CSV file
  }
  def write(lines: List[String]): Unit = {
      // Code that writes to MongoDb
  }
}
class AlgorithmThatReadFromKafkaAndWriteOnMongo(broker: String, topic: String, mongoUri: String)
  extends AlgorithmThatReadFromCsvAndWriteOnMongo(null, mongoUri) {

  def read(): List[String] = {
      // Code that reads from a Kafka topic
  }
}
class AlgorithmThatReadFromKafkaAndWriteOnMongoAndLogs(broker: String, topic: String, mongoUri: String, logFile: String)
  extends AlgorithmThatReadFromKafkaAndWriteOnMongo(broker, topic, mongoUri) {

  def write(lines: List[String]): Unit = {
      // Code that writes to MongoDb and to log file
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

显然，类`AlgorithmThatReadFromKafkaAndWriteOnMongo`继承自`AlgorithmThatReadFromCsvAndWriteOnMongo`只是为了重用写在 MongoDB 上的代码。缺点是我们必须将`null`传递给父类的构造函数。这里有些不对劲。

### 香蕉、猴子、丛林问题

> 面向对象语言的问题是，它们拥有所有这些隐含的环境。你想要一个香蕉，但你得到的是一只大猩猩拿着香蕉和整个丛林。

这句话出自 Erlang 的创造者乔·阿姆斯特朗。根据这一原则，每当您试图重用某个类时，您都需要向它的父类、父类的父类等等添加依赖项。

使用前面的例子，如果您想要重用类`AlgorithmThatReadFromKafkaAndWriteOnMongoAndLogs`，您还需要导入类`AlgorithmThatReadFromCsvAndWriteOnMongo`和`AlgorithmThatReadFromKafkaAndWriteOnMongo`

好吧。以上就是类继承的问题。以上就是*代码复用*的问题。

正如在[依赖](http://rcardin.github.io/programming/oop/software-engineering/2017/04/10/dependency-dot.html)一文中所讨论的，类继承是两个类之间最糟糕的依赖形式。我们已经把类之间的依赖关系的概念和修改一个类的概率与另一个类的变化联系起来。

正常情况下，如果两个类通过这种类型的关系链接在一起，它们必须一起使用。这个事实就是继承的问题:*紧耦合*。

首先，不要使用类继承。不要使用继承来重用一些代码。使用继承在组件间共享**行为**。

之后，不要在一个类中放置一个以上的*责任*。我不会纠结于责任是什么。我已经在[的《单一责任原则做得对](http://rcardin.github.io/solid/srp/programming/2017/12/31/srp-done-right.html)中写过这个话题。然而，如果你让你的组件实现一个以上的用例；如果你让一个组件的两个不同的客户依赖于它的不同部分，那么问题不在于继承，而在于你的整个设计。只实现一种责任的组件很可能从只包含一种类型的层次结构中继承，这种类型大多数时候是一种*抽象类型*。

关于我们前面的例子，有一个代码重用的问题，并且违反了单一责任原则。每个班都做的太多，而且做的方式不对。

### 继承和封装

仔细想想，类继承有一个很大的问题:好像打破了封装。子类(从另一个继承的类)知道并且可以虚拟地访问基类的内部状态。我们正在打破封闭。所以，我们违反了面向对象编程的第一原则，不是吗？

嗯，不完全是。如果你继承的一个类公开了一些`protected`状态，就好像这个类公开了两种接口。

> *公共*接口列出了普通客户端可以看到的内容，而*保护的*接口列出了继承者可以看到的内容。

问题是维护同一类型的两个不同接口非常困难。向一个类中添加更多类型的客户端意味着添加依赖关系。依赖越多，耦合度越高。高耦合意味着类型之间级联变化的概率更高。

所以，不要用类继承。停下来。

如果不能使用继承，为什么这被认为是面向对象编程的本质原则之一？从技术上讲，在某些情况下继承还是可以的。

## 子类型化或重用行为

允许你使用继承的第一种情况是**子类型化**，或者行为继承，或者接口继承。

> 类继承根据另一个对象的实现来定义一个对象的实现。简而言之，它是一种代码和表示共享的机制。相反，接口继承(或子类型)描述了什么时候一个对象可以用来代替另一个对象。

上面这句话信息量很大。它向全世界宣告，如果你想重用行为，你不能覆盖超类的方法。

遵循这个原则，我们可以继承的类型只有*接口*和*抽象类*，避免覆盖后者的任何具体方法。

> 当继承被小心使用时(有些人会说恰当)，从一个抽象类派生的所有类将共享它的接口。[..]子类仅仅添加或覆盖操作，**并不隐藏父类**的操作。

为什么这个原则如此重要？因为*多态性*依赖于它。通过这种方式，客户仍然不知道他们使用的对象的具体类型，**极大地减少了实现的依赖性**。

> 编程到接口，而不是实现。

### 偏爱对象组合胜过类继承

我们必须扩展一个类的行为的唯一方法是使用*对象组合*。通过组装对象获得更复杂的功能，从而获得新的功能。对象是单独使用它们定义良好的接口组成的。

这种复用方式被称为**黑盒复用**。从外部看不到对象的内部细节，从而产生最低的依赖度。

> 对象组合对系统设计有另一个影响。支持对象组合胜过类继承有助于您将每个类封装起来，并专注于一项任务、一项职责。

我们最初的例子可以用两个新的高度专门化的类型重写:一个`Reader`和一个`Writer`。

```
trait Reader {
  def read(): List[String]
}
trait Writer {
  def write(lines: List[String]): Unit
}
class CsvReader(filePath: String) {
  def read(): List[String] = {
      // Code that reads from a CSV file
  }  
}
class MongoWriter(mongoUri: String) extends Writer {
  def write(lines: List[String]): Unit = {
      // Code that writes to MongoDb
  }  
}
class KafkaReader(broker: String, topic: String) extends Reader {
  def read(): List[String] = {
      // Code that reads from a Kafka topic
  }  
}
class LogWriter(logFile: String) extends Writer {
  def write(lines: List[String]): Unit = {
      // Code that writes to a log file
  }  
}
class AlgorithmThatReadFromCsvAndWriteOnMongo(val filePath: String, val mongoUri: String) {
  def read(): List[String] = {
      // Code that reads from a CSV file
  }
  def write(lines: List[String]): Unit = {
      // Code that writes to MongoDb
  }
}
// Composing the above classes into this class, we can read and write from and 
// to whatever we want.
class Migrator(reader: Reader, writers: List[Writer]) {
  val lines = reader.read()
  writers.foreach(_.write(lines))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 里斯科夫替代原理

似乎在任何情况下都不应该使用继承。不能从具体类继承，只能从抽象类型继承。正确吗？有一种特殊的情况，允许从具体的类继承。

利斯科夫替代原理(LSP)告诉我们到底是哪种情况。

> 使用指向基类的指针或引用的函数必须能够在不知道的情况下使用派生类的对象。

当且仅当一个类不重写基类的前置后置条件时，它才能从另一个具体类继承。在派生类中，前提条件不能比基类中的更强。在派生类中，后置条件必须比基类中的更强。

> 当重新定义一个例程(在一个导数中)时，你只能用一个较弱的条件替换它的前置条件，用一个较强的条件替换它的后置条件。

以上称为*合同设计*。遵守这个原则可以避免基类的*外在公共行为*的重新定义，这是类的客户所依赖的行为。

[![Ducks and the Liskov Substitution Principle](../Images/0771771a25a65bd8dc359b39ef9fa4d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t0nRipbA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/cyshi7/lsp.jpg)

回到我们之前的例子，以类`AlgorithmThatReadFromCsvAndWriteOnMongo`及其子类`AlgorithmThatReadFromKafkaAndWriteOnMongo`为例。LSP 告诉我们，每当我们需要对第一个的引用时，我们可以使用对第二个的引用。然而，在我们的例子中，我们不能。第一次从 CSV 读取；第二个来自卡夫卡。

很容易编写一个使用类型为`AlgorithmThatReadFromKafkaAndWriteOnMongo`的对象的测试，其中需要类型为`AlgorithmThatReadFromCsvAndWriteOnMongo`的
对象。很容易看到它失败。

为什么？原因是我们违反了基类*不变量*。我们试图重用代码，而不是重用行为。

老一套，老一套。

## 结论

总结一下:尽量避免代码的复用。尽可能避免类继承。尝试重用行为。尝试使用子类型。不要扩展具体的类。或者，如果你不得不，验证你满足里斯科夫替代原则。

如果您遵循这些简单的规则，您的类之间的依赖程度将保持较低，并且您的架构将更容易维护和发展。

简单。作为。去他妈的。

## 参考文献

*   面向对象语言的四个基本概念，第 10 章:面向对象语言中的概念。《编程语言中的概念》，约翰·米切尔，2003 年，剑桥大学出版社
*   设计模式如何解决设计问题，第一章:简介。设计模式，可重用软件的要素，E. Gamma，R. Helm，R. Johnson，J. Vlissides，1995，Addison-Wesley
*   [利斯科夫替代原理(LSP)。C#中的敏捷原则、模式和实践，罗伯特·C·马丁，弥迦·马丁，2006 年，普伦蒂斯霍尔](https://www.safaribooksonline.com/library/view/agile-principles-patterns/0131857258/)
*   里斯科夫替代原理的一个例子是什么？
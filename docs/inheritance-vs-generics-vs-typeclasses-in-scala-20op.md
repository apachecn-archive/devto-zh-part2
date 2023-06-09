# Scala 中的继承 vs 泛型 vs 类型类

> 原文：<https://dev.to/jmcclell/inheritance-vs-generics-vs-typeclasses-in-scala-20op>

最近，有人在一个编程论坛上问了一个相当无伤大雅的问题:

> 【在 Scala 中】泛型和类型类有什么区别？

我花了一些时间思考这个问题，试图找到困惑的核心所在。我的结论是，对于 Scala 中常见的各种类型的多态性，这是一个误解。特别是对于这个问题，这是**参数多态**(泛型)和**特别多态**(类型类)之间的区别。

# 什么是多态性？

多态性背后的想法总是相同的:我们试图通过以更通用的方式编写代码来增加代码的重用。也就是说，在更高的抽象层次上。

**多态性主要有三种形式:**<sup>[【1】](https://en.wikipedia.org/wiki/Polymorphism_(computer_science))</sup>

*   **亚型多态性** *(遗传)*

    > 当一个名称表示由一些公共超类相关的许多不同类的实例时

*   **参数多态性** *(仿制药)*

    > 当一个或多个类型不是由名称指定，而是由可以表示任何类型的抽象符号指定时

*   **即席多态** *(类型类)*

    > 为任意一组单独指定的类型定义公共接口

为了真正理解这些形式在实践中是如何不同的，我们需要了解每种形式解决什么类型的问题以及它们的局限性。

幸运的是，只用几个例子就可以做到这一点。

# 亚型多态性(遗传)

> 子类型化允许编写一个函数来获取某种类型的对象`T`，但是如果传递一个属于类型`S`的对象，而类型`S`是`T`的子类型(根据[利斯科夫替换原则](https://en.wikipedia.org/wiki/Liskov_substitution_principle))<sup>[【2】](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)#Subtyping)</sup>

换句话说，子类型允许一个函数接受某种类型的值`T`，但是也接受从类型`T`派生的对象，也就是:任何类型的`extends T`。

这是我们在 OOP 中最熟悉的一个。

## 例*无*亚型多态性:

```
class Cat {
  val meow = "meow"
}

class Dog {
  val bark = "woof!"
}

def dogSpeak(d: Dog) = println(d.bark)
def catSpeak(c: Cat) = println(c.meow) 
```

在这里，很明显我们想要定义一个奇异的行为，即`speak`的能力。但是，如果没有任何形式的多态性，我们就无法创建单一的、类型安全的实现。

也就是说，我们可以这样做:

```
 def speak(v: Any) = v match {
  case v: Dog => v.bark
  case c: Cat => c.meow
} 
```

但是，我们失去了静态类型检查，因为所有的类型检查都是在运行时完成的。不仅如此，我们还必须不断地修改这种方法，因为我们在我们的程序中加入了更多的动物。

显然，一定有更好的办法！

## 例*与*亚型多态性:

```
trait Animal {
  def sound: String
}

class Cat extends Animal {
  val sound: String = "meow"
}

class Dog extends Animal {
  val sound: String = "woof!"
}

def speak(a: Animal) = println(a.sound) 
```

虽然这是一个非常基本的例子，但是我们可以看到通过*子类型多态性*使用继承，我们能够通过使用满足我们需求的类型层次结构中最通用的类型，在更高的抽象层次上编写代码。在这种情况下，我们知道所有的`Animal`都可以生成一个`sound`，所以我们可以编写一个单独的`speak`方法，这个方法现在可以用于任何 T3 的*。*

子类型多态性允许我们通过基于类型在类型层次结构中的位置将整组类型视为相同来减少模板。重要的是，它允许我们这样做*，尽管在层次结构*中的类型之间调用的方法中有不同的实现。也就是说，一个`Dog`和一个`Cat`都可以做一个`sound`，因为所有的`Animal`都做`sound`，但是一个`Cat`的`sound`和一个`Dog`的`sound`不一样。*层次结构中不同类型的实现不一致*。

# 参数多态性(Generics)

> 参数多态允许一个函数或一个数据类型被通用地编写，因此它可以统一地处理值，而不依赖于它们的类型<sup>[【3】](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)#Parametric_polymorphism)</sup>

子类型多态性让我们走得很远，但是当我们想要实现一些东西，比如一个定制的`Array`数据类型的时候呢？

```
trait Array {
  def set(index: Int, item: ???): Array
  def get(index: Int): ???
} 
```

我们可以用什么类型来代替`???`？嗯，我们可以做 Java 在泛型之前做的事情，使用**顶级类型**。在 Java 中，最接近 top 类型的是`Object`，在 Scala 中我们有一个*真* top 类型的`Any`。换句话说，*每一个*类型都是 Scala 中`Any`类型的子类型。所以，我们可以做

```
trait Array {
  def set(index: Int, item: Any): Array
  def get(index: Int): Any
} 
```

> **注:**在 Java 中，我们没有*真*顶类型，因为 Java 有`int`、`float`等原语的概念。它们不是对象，因此不是任何类型层次结构的一部分。

但是，当然，这意味着我们将不得不做一些运行时类型检查和类型转换来使我们的自定义类型`Array`变得有用，因为我们放入其中的所有东西都将以类型`Any`的形式出现，这不是一个非常有用的类型。

```
val a: String = "hello"
val b: String = "world"

val strings = new Array() // Pretend this is implemented and not just a trait strings.set(0, a) // a goes in as a String strings.set(1, b) // b goes in as a String 
println(strings.get(0) + strings.get(1)) // strings.get(0) and strings.get(1) are both of type Any
 // ERROR!
 // type mismatch;
 //   found   : Any
 //   required: String 
```

如果我们想要维护*静态类型检查*并避免*运行时强制转换*，我们就不得不为我们想要存储的每种类型创建一个定制`Array`类型的专门版本。

```
trait StringArray {
  def set(index: Int, item: String): StringArray
  def get(index: Int): String
}

trait IntArray {
  def set(index: Int, item: Int): IntArray
  def get(index: Int): Int
} 
```

这显然会很快失控。此外，即使我们没有展示我们的`set`或`get`方法的实现，对您来说可能很明显，我们实际上不需要调用我们的`item`参数的任何方法或访问它的任何字段。**我们的`Array`类型实际上并不关心`item`是什么类型的**，它只是存储了一个对它的引用和一个`index`值，以便以后查找。换句话说，我们的`Array`可以统一处理这些值**。我们可以将`set`和`get`的实现用于`String`和`Int`，只需交换类型，否则它们将在`StringArray`和`IntArray`之间保持相同的。这是参数多态性的亮点，因为它允许我们对代码进行模板化。**

```
trait Array[T] {
   def set(index: Int, item: T): Array
   def get(index: Int): T
}

val a: String = "hello"
val b: String = "world"

val strings: Array[String] = new Array() // Again, pretend it's implemented strings.set(0, a) // a goes in as a String strings.set(1, b) // b goes in as a String 
println(strings.get(0) + strings.get(1)) // both strings.get(0) and strings.get(1) are of type String! 
// We can also parameterize functions def first[T](items: Array[T]): T = items.get(1) 
```

通过使用**类型参数**对代码进行**模板化**，参数多态性允许我们通过编写能够在一系列类型上统一**工作**的代码来减少模板。一个`Array`类型可以为任何类型`T`工作，因为*它的实现不依赖于任何特定于类型的行为*—`Array`实现是所有类型的**统一**。

# 【即席多态性】(类型类)

> 术语即席多态[指]可以应用于不同类型的参数的多态函数，但是根据它们所应用的参数的类型而表现不同<sup>[【4】](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)#Ad_hoc_polymorphism)</sup>

如果我们需要能够创建方法/函数，其参数可以是不同的**不相关的**类型(关于子类型)*，并且实现也是类型特定的*，即:**不统一**，该怎么办？

### 假设你想写一个函数，**给定一列数字**，会不会**返回这些数字的算术平均值**:

> **注意:**我引用了 Daniel Westheide 关于这个主题的一篇优秀的[博客文章中的例子，为什么？因为他的例子太棒了，你应该看看他的帖子，比我在这里的简单介绍更好地理解类型类。](https://danielwestheide.com/blog/2013/02/06/the-neophytes-guide-to-scala-part-12-type-classes.html)

首先，让我们来实现`Double`

```
def mean(xs: List[Double]: Double = xs.reduce(_ + _) / xs.size 
```

不错！...但是`Int`的列表呢？当然，我们希望能够获得其他*数字*类型的平均值，而不必首先将每个类型转换为 double。

```
def mean(xs: List[Int]: Double = xs.reduce(_ + _) / xs.size 
```

好吧，酷。感谢 Java 的重载能力，我们现在已经为 `Double`和`Int`*实现了`mean`，对吗？*

## 运行时类型擦除有时会使过载变得困难

```
def mean(xs: List[Double]): Double = xs.reduce(_ + _) / xs.size
def mean(xs: List[Int]): Double = xs.reduce(_ + _) / xs.size

 // ERROOR:
 // double definition:
 // def mean(xs: List[Double]): Double at line 1 and
 // def mean(xs: List[Int]): Double at line 2
 // have same type after erasure: (xs: List)Double
 // def mean(xs: List[Int]): Double = xs.reduce(_ + _) / xs.size
 // ^
 // Compilation Failed 
```

因为**运行时类型擦除**，*JVM，在运行时，不能区分`List[Int]`和`List[Double]`* ，所以**我们不能使用重载**来实现我们的`mean`函数——至少不容易。对于我们关心的每一种类型，一遍又一遍地实现函数也有点重复。

## 亚型多态性来拯救？

在 Scala 中， **`Int`和`Double`除了都从`AnyVal`扩展之外，不共享类型层次**。*要是数字类型扩展了某种`Numeric`特征就好了！*

如果他们做了呢？

```
def mean(xs: List[Numeric]): Numeric = ??? 
```

**丹尼尔的建议？**

> 幸运的是，在这种情况下，没有这样的共同特征，所以我们根本不想走这条路。然而，在其他情况下，这很可能是事实——而且仍然是一个坏主意。我们不仅丢弃了以前可用的类型信息，还关闭了 API，防止将来扩展我们无法控制其来源的类型:我们不能让来自第三方的一些新的数字类型扩展[`Numeric`]特征。

他是对的。如果我们在共享的超类型`Numeric`上键入我们的`mean`函数，我们在输出时将输入的类型扩展到它的超类型，这意味着我们丢失了类型信息。换句话说，事物以特定的类型`Int`、`Double`等进入。但却是更普通的类型`Numeric`。

此外，假设我们导入了一个第三方库，比如 **Joda Time** ，并希望将我们的`mean`函数用于它的`Duration`类型——我们可能都同意这符合`Numeric`的定义。我们不走运了！ *没有办法让第三方的数值型扩展我们的`Numeric`特质！*

## 经典适配器模式呢？

```
trait NumberLike[A] {
  def get: A
  def plus(y: NumberLike[A]): NumberLike[A]
  def divide(y: Int): NumberLike[A]
}

case class NumberLikeDouble(x: Double) extends NumberLike[Double] {
  def get: Double = x
  def plus(y: NumberLike[Double]) = NumberLikeDouble(x + y.get)
  def divide(y: Int) = NumberLikeDouble(x / y)
}

case class NumberLikeInt(x: Int) extends NumberLike[Int] {
  def get: Int = x
  def plus(y: NumberLike[Int]) = NumberLikeInt(x + y.get)
  def divide(y: Int) = NumberLikeInt(x / y)
}

def mean[A](xs: List[NumberLike[A]]): NumberLike[A] = xs.reduce(_.plus(_)).divide(xs.size) 
```

**这是针对特定多态性问题的经典 OOP 解决方案。从好的方面来看，我们重新获得了完整的编译时类型安全性和可扩展性。然而，不利的一面是，在`mean`函数可以使用之前，必须将`List[Int]`中的每个** `Int`转换为`NumberLike[Int]`，这会影响性能。因此，我们希望传递给`mean`的数字列表越大，我们必须创建的适配器实例就越多。**类型类解决了这个问题**，也解决了我们在寻找特定多态性解决方案时遇到的其他问题。

## 那么什么*是 a 型班呢？*

 *> 类型类`C`以操作的形式定义了一些行为，这些行为必须被类型`T`支持才能成为类型类`C`的成员。类型`T`是否是类型类`C`的成员并不是该类型所固有的。相反，任何开发人员都可以通过提供类型必须支持的操作的实现来声明类型是类型类的成员。现在，一旦`T`成为类型类`C`的成员，约束了一个或多个参数成为`C`成员的函数可以用类型`T`的参数调用。
> 
> 类型类允许特别的和追溯的多态性。依赖于类型类的代码对扩展是开放的，无需创建适配器对象。

换句话说，**我们在不使用继承**的情况下在一个接口上提供了多态性，但是我们保持了为我们的*类型类接口*定义的一组必需的操作在不同类型之间拥有**不同实现的能力。此外，**我们保持了随意轻松扩展类型类成员的能力**，即:*以一种特别的方式*。**

> **注意:**在类型类作为语言特性内置的语言中，它们以类似于其他两种多态形式的方式将我们从模板代码中拯救出来。然而，在 Scala 中，我们没有在语言中内置类型类，相反，我们必须使用强大的类型系统对它们进行编码。我们最终编写的代码量与适配器模式的代码量相似，但它最终是一个更干净、运行时效率更高的实现。我们还必须*利用*参数多态性(泛型)以及 Scala 的隐式功能。这两件事是能够在 Scala 中编码类型类的关键，如下所示。

所以，首先，为了定义我们的类型类，我们需要一个特征来表示它

```
trait NumberLike[T] {
  def plus(x: T, y: T): T
  def divide(x: T, y: Int): T
} 
```

这定义了一个类型必须实现的接口，以便成为我们的类型类的成员。本质上，这个*就是*这个类型类。

然后，我们可以向类型类添加一些默认成员

```
object NumberLike {

  implicit object NumberLikeDouble extends NumberLike[Double] {
    def plus(x: Double, y: Double): Double = x + y
    def divide(x: Double, y: Int): Double = x / y
  }

  implicit object NumberLikeInt extends NumberLike[Int] {
    def plus(x: Int, y: Int): Int = x + y
    def divide(x: Int, y: Int): Int = x / y
   } 
```

如果`NumberLike`是类型类，那么`NumberLikeDouble`和`NumberLikeInt`都是类型类*成员*。嗯，至少是最直接的。它们的作用是通过提供类型类接口的 API 的特定于类型的实现，允许`Double`和`Int`成为类型类的*成员*。也就是说，`plus`是通过`NumberLikeDouble`和`NumberLikeInt`分别为`Double`和`Int`定义的，就像`Int`和`Double`都是从`Numeric`超类型扩展而来，并且都直接实现了`plus`一样。使用类型类，我们不需要子类型关系就可以实现这一点！

**需要注意的是，这些对象被定义为`implicit`** 。这是在 Scala 中使类型类*可编码*的关键，通过允许这些对象在正确的情况下隐式可用。理解隐式在 Scala 中是如何工作的是理解我们在 Scala 中如何编码类型类的关键，但是这是一个比我在这里讨论的更大的主题。

一旦我们定义了类型类及其成员，我们就可以利用我们刚刚创建的特定多态性来编写我们的`mean`方法:

```
def mean[T](xs: List[T])(implicit ev: NumberLike[T]): T = ev.divide(xs.reduce(ev.plus(_, _)), xs.size) 
```

现在，当我们用`List[Int]`调用`mean`时，`T`将被绑定到`Int`，因此编译器将试图定位一个**隐式** `NumberLike[Int]`来自动传递到第二个参数列表中。如果不能为我们指定的类型`T`找到`NumberLike[T]`的实例，那么我们知道我们已经将`T`绑定到一个类型，该类型不满足我们的类型类接口，因此*不是类型类的成员。如果我们回到我们的子类型多态性例子，这就像给`speak`传递一个值，而不传递给`extend Animal`。*

在这种情况下，我们传入`List[Int]`，因此`T`被绑定到`Int`，并且我们已经定义了一个隐式的`NumberLike[Int]`对象`NumberLikeInt`，因此我们知道`Int`满足类型类接口，并且我们能够使用在`NumberLikeInt`中具体定义的`plus`和`divide`方法来实现我们的`mean`功能。

## 扩展性

我们还可以很容易地证明，通过简单地向我们的`NumberLike`类型类添加更多成员，我们能够任意地向`mean`添加对更多类型的支持。

```
object JodaImplicits {
  implicit object NumberLikeDuration extends NumberLike[Duration] {
    def plus(x: Duration, y: Duration): Duration = x.plus(y)
    def divide(x: Duration, y: Int): Duration = Duration.millis(x.getMillis / y)
  }
} 
```

这就是类型类的亮点。如果我们有许多接受`NumberLike`作为参数的函数，那么每当我们向类型类添加一个新成员时，我们就会自动扩展它们的效用。而且，令人惊讶的是，我们可以将任何类型添加到我们的类型类**中，即使我们不控制类型**的源代码！

类型类允许我们获取以某种任意方式相关的类型，并提供一个公共接口来标识它们，即使它们没有通过子类型相关联。在我们的例子中，我们定义的任意关系是*数字*或`NumberLike`。我们能够确定任何`NumberLike`值必须能够支持加法和除法，因此这成为了通过`plus`和`divide`方法在我们的`NumberLike`类型类中定义的接口。我相信你可以想象出适合`NumberLike`值的其他方法，例如:`multiply`。

# 结论

我希望这能帮助那些对 Scala 中三种形式的多态性有疑问的人——尤其是类型类。

至于最初的问题，需要注意的是**我们必须利用参数多态(泛型)来编码 Scala** 中的类型类。这并不意味着它们是一回事。***
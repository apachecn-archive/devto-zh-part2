# Scala - On 类和对象

> 原文：<https://dev.to/avikaminetzky/scala---on-classes--objects-3jij>

我为 Scala 中令人讨厌的类和对象部分编写了一个半入门指南，非常适合来自 JavaScript 和 Python 等语言的人，并且几乎没有接触过编译过的强类型语言。

我试图。举个最简单的例子
2。永远不要把 Scala 和 Java
3 相提并论。避免向你推销函数式编程或 Scala

每一节都建立在前一节的基础上。

### 推荐资源

这些资源与本教程没有直接关系，但对于学习 Scala 和函数式编程非常有用。不要对你的
[ **做任何假设，从这里插入:**函数式编程/类型理论/面向对象编程/范畴理论/数学博士/演员模型/Java 8/反应式编程]能力。

*   [创意 Scala(电子书)](https://underscore.io/training/courses/creative-scala/)
*   [基本 Scala(电子书)](https://underscore.io/books/essential-scala/)
*   [Scala 之旅](https://docs.scala-lang.org/tour/tour-of-scala.html)
*   [Gitter](https://gitter.im/scala/scala) -初学者提问欢迎！
*   [功能编程指南(电子书)](https://github.com/MostlyAdequate/mostly-adequate-guide)
*   没看过这个，但我确定很棒:[弗里斯比教授介绍可组合函数 Javascript(视频课程)](https://egghead.io/courses/professor-frisby-introduces-composable-functional-javascript)

### 特质

> Traits 用于在类之间共享接口和字段...类和对象可以扩展特征，但是特征不能被实例化，因此没有参数。([文档](https://docs.scala-lang.org/tour/traits.html)

特征可以有抽象和具体的方法。

```
trait Person {
  // concrete method, can only be overridden by class if it's a def, not a var or val
  def name = "Tim"
  // abstract method, must be implemented in class
  def greeting: String
}

class Tim extends Person {
  def greeting: String = s"Welcome, ${name}"
}

val timClass = new Tim
timClass.greeting
// res0: String = Welcome, Tim 
```

Enter fullscreen mode Exit fullscreen mode

您可以覆盖人员特征:
中的`name`

```
class Tim extends Person {
  // override trait name.
  override val name: String = "Mr. Tim"
  def greeting: String = s"Welcome, ${name}"
}

val timClass = new Tim
timClass.greeting
// res0: String = Welcome, Mr. Tim 
```

Enter fullscreen mode Exit fullscreen mode

### 普通物体

对象本身只是一种将数据和函数打包在一起的方式。从技术上讲，它是一个匿名类的[单例](https://en.wikipedia.org/wiki/Singleton_pattern)实例。

```
trait Person {
  val name: String
}

object Tim extends Person {
  val name = "Tim"
}

Tim.name
// res0: String = Tim 
```

Enter fullscreen mode Exit fullscreen mode

App trait 的另一个例子:

> App trait 可以用来快速将对象转化为可执行程序。"([文档](https://www.scala-lang.org/api/current/scala/App.html))

啰嗦道

```
object HelloWorld {
  def main(args: Array[String]): Unit = {
    val name = args(0)
    println(s"Hello, $name")
  }
}
// "Hello, dude" 
```

Enter fullscreen mode Exit fullscreen mode

无样板:

```
object HelloWorld extends App {
    val name = args(0)
    println(s"Hello, $name")
} 
```

Enter fullscreen mode Exit fullscreen mode

要执行，从命令行运行`scala HelloWorld.scala dude`。

### 选项/部分

任何可能包含 null/空值的值都有一个选项类型，在赋值时，必须用。

```
val name: Option[String] = Some("Tim")
name.getOrElse("Anon")
// res0: String = Tim 
```

Enter fullscreen mode Exit fullscreen mode

当 name 为`None`时，getOrElse 方法将提供一个默认值。它还会将`name`从一个`Some("Tim")`值降低到一个普通的`"Tim"`值。

[一篇关于对`Option`值的惯用处理的好文章](http://blog.originate.com/blog/2014/06/15/idiomatic-scala-your-options-do-not-match/)。

> 引入也许会引起一些最初的不适。Swift 和 Scala 的用户会明白我的意思，因为它在 Option(al)的伪装下被烘烤到核心库中。当总是被要求处理空支票时(有时我们绝对肯定值是存在的)，大多数人都会情不自禁地觉得有点费力。然而，随着时间的推移，它会成为第二天性，你可能会欣赏安全。毕竟，大多数时候它会防止偷工减料，拯救我们的兽皮。
> 
> 编写不安全的软件就像在将鸡蛋投入市场之前小心翼翼地用蜡笔涂上颜色；就像用三只小猪警告过的材料建养老院。给我们的功能增加一些安全性会对我们有好处，也许会帮助我们做到这一点。([功能编程指南第 8 章](https://mostly-adequate.gitbooks.io/mostly-adequate-guide/ch08.html#releasing-the-value))

### 模式匹配

Scala 中的模式匹配，[就像类固醇](https://thecodegeneral.wordpress.com/2012/03/25/switch-statements-on-steroids-scala-pattern-matching/)中的`switch`语句。

```
val name: Option[String] = Some("Bob")

name match {
  // for an exact match on a Tim name
  case Some("Tim") => "Hi, Tim!"

  // for any other Some(value)
  case Some(name) => s"Hi ${name}"

  // default case for any other value
  case _ => "Anonymous has no name!"
}

// res0: String = Hi Bob 
```

Enter fullscreen mode Exit fullscreen mode

### 对象的 apply()和 unapply()方法

我对这些函数的看法是，`apply()`是一个对象的内置构造函数，而`unapply()`是一个对象如何解构的，这对模式匹配特别有用。这两个函数是互斥的，所以可以定义一个而不定义另一个。( [StackOverflow](https://stackoverflow.com/questions/18468786/understand-how-to-use-apply-and-unapply) )来自[的例子文档](https://docs.scala-lang.org/tour/extractor-objects.html)。

```
object Person {
  def apply(name: String) = {
    s"Welcome--$name"
  }
  def unapply(name: String): Option[String] = {
    val nameSplit = name.split("--")
    // we can't be sure the value exists in the object
    if (nameSplit.tail.nonEmpty) Some(s"Goodbye--${nameSplit(1)}")
    else None
  }
}

// using apply() to construct the object
val dudePerson = Person("dude")
println(dudePerson)
// res0: Welcome--dude

// using unapply() to assign a variable
val Person(dudeName) = dudePerson
println(dudeName)
// res1: Goodbye--dude

// using unapply() for pattern matching
val matchTheDude = dudePerson match {
  case Person(name) => name
  case _ => "Goodbye--anonymous dude"
}
println(matchTheDude)
// res2: Goodbye--dude 
```

Enter fullscreen mode Exit fullscreen mode

### 类伴侣对象

当一个对象与一个类同名时，它通常被称为伴随对象。它对于存储不依赖于类实例的类功能很有用(把它们看作静态或类方法)。

对于一个名为`Person`且构造函数为`name: String`的类/伴随对象，该对象可以通过调用`new Person("Tim").value`来访问该类中的任何内容，而该类可以通过调用`Person.value`来访问其对象中的任何内容。即使这些值被设置为`private`。

```
 class Person(val name: String) {
  // Person(Tim) instead of the default memory address Person@3018b152
  override def toString = s"Person($name)"
}

object Person {
  // static method to track number of people objects instantiated
  var count = 0

  def apply(name: String): Person = {
    count = count + 1
    // using the Person object as the constructor, allows you to call Person("Tim") 
    // without the need to write "new Person("Tim")"
    new Person(name)
  }

  def unapply(person: Person): Option[String] = Some(person.name)
}

// calling apply() to instantiate the Person class
val bob = Person("Bob")

// our custom toString function
println(bob.toString)
// res0: Person(Bob)

// the apply() increments the count variable for each new Person
val tim = Person("Tim")
Person.count
// res1: 2

// unapply() with pattern matching
val matchTim = tim match {
  case Person(name) => name
  case _ => "Sorry, no match"
}
matchTim
// res2: String = Tim 
```

Enter fullscreen mode Exit fullscreen mode

### 案例类

`case class`是类的一种类型，对于用最少的样板文件存储不可变数据很有用。`case object`有[类似的功能](https://stackoverflow.com/questions/5270752/difference-between-case-object-and-object)，但是是针对单例类的。

```
case class Person(title: Option[String] = None, name: String)

// comes with a companion object apply() method, no need for new Person("Dude")
val dude = Person(name="Dude")

// name parameter is set to an immutable val
dude.name
// res0: String = Dude

// sane toString method, as opposed to Person@721d791f weird hex stuff
dude.toString
// res1: String = Person(None,Dude)

val tim = Person(name="Tim")

// convenient copy method if you only need to change specific values
val timCopy = tim.copy(title=Some("Mr"))
// timCopy: Person = Person(Some(Mr),Tim)

// comes with a companion object unapply() method, useful for pattern matching
timCopy match {
  case Person(title, name) => title
  case _ => "No title"
}
// res2: Some(Mr)

// sane equals method which compares the content of case classes
dude.equals(tim)
// res3: Boolean = false

// class equality is usually done by comparing the memory address of each class 
// instance, that's why you get this weird behavior
class Guy(name: String)

val guy1 = new Guy("guy")
// guy1: Guy = Guy@5f46825d

val guy2 = new Guy("guy")
//guy2: Guy = Guy@17be95d

guy1 == guy2
//res4: Boolean = false 
```

Enter fullscreen mode Exit fullscreen mode

### 暗示

一个很好的例子来自[这里](https://stackoverflow.com/a/10375941/4822174) :

```
// probably in a library
class Prefixer(val prefix: String)
def addPrefix(s: String)(implicit p: Prefixer) = p.prefix + s

// then probably in your application
implicit val myImplicitPrefixer = new Prefixer("***")
addPrefix("abc")  // returns "***abc" 
```

Enter fullscreen mode Exit fullscreen mode

[Play 框架控制器](https://www.playframework.com/documentation/2.6.x/ScalaActions)使用隐式，因此来自`Action`的任何函数调用都将获得对 http `request`变量:
的访问权

```
def action = Action { implicit request =>
   anotherMethod("Some para value")
   Ok("Got request [" + request + "]")
 }

def anotherMethod(p: String)(implicit request: Request[_]) = {
 // do something that needs access to the request
 } 
```

Enter fullscreen mode Exit fullscreen mode

使用 [Play Json 库](https://www.playframework.com/documentation/2.6.x/ScalaJson)在 Json 和 case 类之间转换时，`implicit`很方便。这是在创建 API 时获得类型安全 JSON 的简单方法:

```
import play.api.libs.json.Json
case class Totals(
                   subTotal: Double = 0,
                   estimatedShippingAndInsurance: Double = 0,
                   estimatedTax: Option[Double] = Some(0),
                   orderTotal: Double = 0
                 )
// withDefaultValues is required if you want your case class defaults to be applied // to the Json conversion
object Totals { implicit val writeTotals = Json.using[Json.WithDefaultValues].format[Totals] } 
```

Enter fullscreen mode Exit fullscreen mode

### 类结构化

在面向对象编程中，[我们在*是-a* 和*是-a* 之间做了区分](https://stackoverflow.com/questions/2218937/has-a-is-a-terminology-in-object-oriented-language)。 *is-a* 是继承的候选。

一个产品*有——一个* `salePrice` *和*一个`regularPrice` :

```
trait Product {
  val salePrice: Double
  val regularPrice: Double
} 
```

Enter fullscreen mode Exit fullscreen mode

一台电脑*是一家商店*的产品:

```
case class Computer(
                     salePrice: Double,
                     regularPrice: Double,
                     processor: String,
                     operatingSystem: String
                   ) extends Product 
```

Enter fullscreen mode Exit fullscreen mode

衣服*也是*的产品:

```
case class Clothes(
                     salePrice: Double,
                     regularPrice: Double,
                     size: Int,
                     material: String
                   ) extends Product 
```

Enter fullscreen mode Exit fullscreen mode

另一点要考虑的是，产品是电脑类型的*或*衣服。

*的一个常见模式是-或*(来自 Essential Scala):

```
sealed trait TrafficLight {
// pattern matching on case objects
  def next: TrafficLight = this match {
    case Red => Green
    case Green => Yellow
    case Yellow => Red
  }
}
final case object Red extends TrafficLight
final case object Green extends TrafficLight
final case object Yellow extends TrafficLight

val myLight = Red.next
// res0: TrafficLight = Green
myLight.next
// res0: TrafficLight = Yellow
myLight.next.next
// res1: TrafficLight = Red 
```

Enter fullscreen mode Exit fullscreen mode

`sealed trait`用于告诉编译器只期待红色、绿色和黄色类型的流量指示灯。如果我们在模式匹配时忽略黄色，编译器会抱怨`match may not be exhaustive`。`final`就是禁止任何人延伸红绿黄阶级。
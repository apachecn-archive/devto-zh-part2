# 科特林-好的，坏的和丑陋的

> 原文：<https://dev.to/martinhaeusler/kotlin---the-good-the-bad-and-the-ugly-3jfo>

嗨伙计们！

这是我很久以来一直想写的一篇文章。我见过我的那份 Kotlin，也在生产中使用过。Kotlin 是一种基于 JVM 的语言，由 Jetbrains 开发，以其 ide 和开发工具而闻名。从语言的角度来看，Kotlin 非常有趣，它有许多优点，但也有一些陷阱和笨拙的设计选择。让我们开始吧！

# 好人

我可以写满整本书，讲述我喜欢科特林的地方。这是高光的概要。

## 分号是可选的

不是“可选的”，比如“大多数时候没有它们也能工作，只是有时会有所不同”，而是“使用它们或放弃它们，程序不会改变”。代码不那么杂乱，我的手腕对此非常满意。

## NULL 是一个单独的类型

`String`(非`null`字符串)和`String?`(可空字符串)是 Kotlin 中两种截然不同的类型。如果你坚持 Kotlin 的方法，你的程序就不能把可怕的`NullPointerException`扔给你。

## 扩展功能

你曾经想要一个安全的 Java 版本的 T1 吗？好吧，科特林掩护你！您可以编写静态方法(带有某些签名)并像调用常规成员方法一样调用它们。这也标志着充满静态方法的可怕的`StringUtils`类的死亡。这些现在都可以是扩展方法了！

## 流分型&式推断

对于所有的局部变量，你只需使用`var`，编译器会自己找出类型。Java 10 也有这一点，但有了 Kotlin 更强的类型系统，它就更有价值了。科特林也不需要演员。如果你检查一个对象是某个类的`instanceOf`，那么在这个`if`块中，这个变量将被如此处理，而不需要你手动的向下转换它。科特林称之为“智能铸造”。

## 终犯易

在 Java 中，需要使用`final`修饰符来使局部变量不可赋值。这是一个非常有用的属性，因为它消除了一大群潜在的错误。不过，`final String myString`是一口饱。几乎没有人想处理这个问题。在 Kotlin 中，final 和非 final 变量的字符数是完全相同的*:可变变量的`var myString`和不可变变量的`val myString`。*

 *## 智能标准库

科特林标准图书馆是一个充满惊喜的宝库。在 Java 中没有一种优雅的方式来使用锁和`try-with-resources`是否困扰过你？嗯，在科特林你有`lock.withLock{ doWork() }`。虽然是小事，但是真的很有帮助。你多久会忘记打开一个`finally`条款中的锁？还是解锁了读锁而不是写锁？科特林标准图书馆到处都是这样的小帮手。

再来一个:`mutableListOf<T>(T... elments)`。如果您提供元素，Kotlin 编译器足够智能，可以计算出`T`。因此对这个方法的调用将有两种形式:

```
// empty list, need to specify the type argument explicitly
val myList = mutableListOf<String>()

// pre-filled list, no need for type argument
val yourList = mutableListOf("Hello", "World"!) 
```

Enter fullscreen mode Exit fullscreen mode

我们能不能在这里停一会儿，欣赏一下这两种情况下这段代码读起来像散文一样？此外，Kotlin 区分了可变集合和只读集合，同时仍然与 Java 兼容。这本身就是一项了不起的成就，它让 Kotlin 代码对细微的错误更加健壮(嘿，谁修改了这个集合！？).

## 溪流，流线型

如果你想在 Java 中过滤一个`List`，你需要这样做:

```
List<String> newList = oldList.stream()
    .filter( s -> s.startsWith("a") )
    .collect(Collectors.toList()); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们真正想要做的是:
，那么这将是相当多的文本

```
List<String> newList = oldList.filter(s -> s.startsWith("a")); 
```

Enter fullscreen mode Exit fullscreen mode

那用`stream()`的意义在哪里？通过包围你的过滤器(和地图操作和...)有了`stream()`和`collect(...)`，Java 流就可以*懒*。然而，你可能会说，如果你想做的只是一个过滤器，那么懒惰是没有意义的。您是对的，对于单个过滤器操作，流是多余的，无论是在语法上还是在概念上。但是在 Java 里，`List#filter(...)`根本就不存在(你可以用一个静态的 utility 类，但是拜托，那不是一回事)。

科特林用一种非常优雅的方式解决了这个问题:

```
// single filter, quick and easy
val newList = oldList.filter { it.startsWith("a") }

// streams for multiple filters (same syntax!)
val newList = oldList.asSequence().filter { it.startsWith("a") }.toList() 
```

Enter fullscreen mode Exit fullscreen mode

因此，Kotlin 给了你**基于流的懒惰链(称为*序列*)以及单次操作。最好的一点是，列表中的`filter`的语法与序列中的`filter`的语法完全相同。如果您使用现有的代码，并意识到您需要另一个过滤器，只需在前面插入`asSequence()`并在后面插入`toList()`以使其惰性，并保留现有的过滤器。**

## 万岁`Iterator`

迭代器是一个非常基本和强大的概念。它们从 1.0 版本开始就存在于 Java 中(这说明了很多)。然而，在 Java 中，迭代器被当作继母一样对待。您不能像对`Collection`那样对迭代器进行`for`循环。`Collection#addAll`只接受其他`Collection`作为参数，不接受`Iterable`的，见鬼，你连`iterator.stream()`都不会。我一直不明白为什么。

科特林解决了上述所有问题。在 Kotlin 中处理迭代器就像处理集合一样愉快，而不会失去迭代器的好处:

```
for(element in iterator) { ... } /* works! */

iterator.asSequence().filter { ... } /* works! */

myList.addAll(iterable) /* works! */ 
```

Enter fullscreen mode Exit fullscreen mode

## Java 互操作

...真的很好。就像“非常非常好”。您可以混合 Kotlin 和 Java 源代码(以及二进制文件！)在同一个项目中没有问题。尽管有一些陷阱；稍后我们将对此进行详细介绍。

## 多平台，一种语言

Kotlin 翻译成 JVM 字节码，以及 JavaScript。本地实现也在进行中。我认为不用说，这是一件大事。

# 坏的&丑的

尽管有其优点，科特林也有一些缺点。它们并没有真正破裂，但仍然值得注意。

## 否`static`修改

在 Kotlin 中，没有`static`关键字。如果您只想定义一个实用程序方法，这会带来一些麻烦。你要做的反而是:

```
class MyClass {
   companion object {
       @JvmStatic
       fun myStaticMethod(/*...*/) { /*...*/ }
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是丑陋的。在 Kotlin 看来，常量有`const`(这与 Java 中的`public static final`成员大致相同)，你可以在任何类之外声明顶级函数(这实际上使它们成为了`static`)。但是如果你*真的*想要一个静态方法，上面概述的方法是唯一的选择(据我所知)。

最初引入`companion object`是为了简化单件的构造。伴随对象是它所在的类的单例实例。我觉得这是一个错误的决定。首先因为静态方法是有用的，不应该需要太多的语法，其次因为单例模式没有冗长到需要这样的简写，也没有有用到值得如此突出的语言特性。

## `open`关键词

Kotlin 中默认的所有类都是`final`。让那件事过去一段时间。

```
class MyClass {

}

class SubClass : MyClass { /* NOPE: compile error! MyClass is final! */

} 
```

Enter fullscreen mode Exit fullscreen mode

这只是冰山一角。想象一下像 Spring 或 Hibernate 这样的框架，它们在运行时生成扩展类的字节码。不行，不能这么做——这门课是`final`。转眼之间，你所有的 Kotlin 类突然变得与几乎所有常用的框架都不兼容了。有两种方法可以规避这个问题:

*   要么将您的类声明为`open class MyClass`要么
*   向 Kotlin 编译器添加一个编译器插件。

这两种选择都不太好。Kotlin 语言开发人员的推理是，可扩展性应该是一个选择加入的东西。只有当你的类*打算*扩展时，它才应该能够被扩展。在我看来，这种推理是有缺陷的:防御性编程最佳实践会告诉你，你应该准备你的代码在最意想不到的环境中工作。如果你写了一个类，当它被子类化时，你写了一个坏的类。科特林在这里给出了完全错误的激励，简单地封闭了一切。

## 构造函数丰富

作为一种语言，Kotlin 对构造函数有一种非常奇怪的迷恋。看一看:

```
// primary constructor
class PersonA(val firstName: String, val lastName: String) { }

// regular constructor
class PersonB {

    val firstName: String
    val lastName: String

    constructor(firstName: String, lastName: String){
        this.firstName = firstName
        this.lastName = lastName
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

上面的两个例子**做了完全相同的事情**。你可能会说带有“主构造函数”的变体更短，因此更好。我不同意，理由如下:

*   主构造函数必须总是列出(并分配)所有字段。当您需要一个额外的瞬态字段用于本地缓存时，这是非常烦人的。
*   类声明行变得非常长。
*   如果从具有主构造函数的基类扩展，则...
    *   ...**必须自己声明一个主构造函数吗**
    *   ...**必须**调用基类的主构造函数
    *   ...**必须在类声明中完成所有这些吗**

总之，一旦你被锁定在主构造器工作流中，你就不能再挣脱了。你的类声明很快就会变得非常大。考虑一下这个:

```
class Student(val firstName: String, val lastName: String, val studentId: String) : Person(firstName,lastName) {

} 
```

Enter fullscreen mode Exit fullscreen mode

当然，你可以从这样的线路中得到很多好处。它声明一个子类，声明字段，声明构造函数，声明基类，最后声明继承的字段应该如何填充。这是非常高的信息密度。我还是不喜欢。太多的信息被打包在一起，对于人类来说很难解析。我不推荐使用主构造函数(除了你别无选择的数据类)，但是有时候你会因为标准库中的类而被迫使用它们。

## 数据类——一个半生不熟的绝妙概念

数据类本身就是一个绝妙的主意。看一看:

```
data class Person(val firstName: String, val lastName: String) 
```

Enter fullscreen mode Exit fullscreen mode

您指定一个类的字段及其类型，并得到:

*   指定的字段
*   吸气剂/沉降剂
*   一个构造函数，它做你所期望的事情
*   哈希码
*   等于
*   toString
*   克隆
*   各种公用事业

...全部免费，*不用写*。这意味着你的`hashCode()`永远不会与你的`equals()`不同步。您的`toString()`将永远不会忘记打印您最近添加到类中的字段。所有这些都是可能的，因为这些东西没有文本表示；编译器只是直接生成字节码。这是一个非常酷的概念，远远优于通过 IDE 生成上述所有东西(因为生成的代码很容易不同步)。

问题是直到最后才想通。主要问题是在编写数据类时不能使用继承。数据类不能扩展任何基类，并且是`final`本身(不，你也不能在这里使用`open`)。这个决定背后的理由是，当你试图引入继承时，事情可能会中断(特别是在克隆实用程序中)。在我看来，这完全是垃圾。UML 和 Ecore 已经展示了如何通过多重继承和克隆工具正确地拥有数据类。

缺少继承选项极大地限制了数据类的适用性。甚至简单的数据传输对象(dto)或 JPA 实体(数据类非常有用的主要例子！)往往需要继承。

## 丰富的关键词

Kotlin 有一个非常大的[关键词和操作符](https://kotlinlang.org/docs/reference/keyword-reference.html)。Kotlin(像 C#)有“软关键字”的概念，这意味着某个单词仅在某些上下文中被视为关键字，但可以在代码中的所有其他地方用作标识符(例如变量名)。他们中的一些人相当...晦涩难懂又极其具体，比如`crossinline`。无论如何，这使得语言很难学习，甚至更难掌握，因为你必须知道所有这些关键字。在常规使用中，你不需要比在 Java 中更多的东西。但是如果你想阅读和理解库函数，你会经常遇到它们。我个人认为，这些关键词中有很多可以通过注释更好地表达出来(比如用`@Actual`代替修饰语`actual`)。

# 裁决

我绝对推荐任何 Java 程序员至少仔细看看 Kotlin。在很多领域，它都是比 Java 优越得多的语言。它继承了所有的 Java 基础设施和库，并在上面添加了许多智能设计选择。它有一个更好的类型系统，用于增强安全性和编译时错误检查。仅仅是默认情况下它是`null`安全的这个事实就足以证明在 Java 上使用 Kotlin 是正确的。然而，它并不完美。Kotlin 使编写短小精悍的代码变得更容易，但它也可以用来产生一堆看似随机的字符组合，两天后就没人能破译了。然而，尽管它有缺点、弱点和偶尔奇怪的设计选择，它是一种优秀的语言。

你有什么使用这种语言的经验分享吗？你在生产中使用它吗，你对它满意吗？请在下方留言评论。*
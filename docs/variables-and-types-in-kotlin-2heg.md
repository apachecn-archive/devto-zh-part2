# kotlin 中的变量和类型

> 原文：<https://dev.to/raulmonteroc/variables-and-types-in-kotlin-2heg>

[![](img/9fffbb5718cc3cae0d47414dbab8ce86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F5e5_4Fk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raulmonteroc.com/wp-content/uploads/2018/07/kotlin_800x320-1.png)

所有的语言都是不同的，但同时，它们有很多共同点，有很多相似的成分。

很有可能，这不是你的第一语言，因此你熟悉变量的概念和你在语言中可能遇到的不同的常见类型。尽管如此，我还是会带你了解 kotlin 与众不同的地方，这样你就能开始对这种语言的哲学和做事方式感到舒服了。

我们开始吧。

# 变量

变量的工作很简单，赋一个值并给它一个名字以便以后调用。Kotlin 采用了这个简单的概念，并添加了一些额外的规则来增强代码的性能和可读性。

#### 申报

在 Kotlin 中，变量声明有一个简单的结构:

1.  var(或 val)关键字来指示变量声明的开始。
2.  变量的名称。
3.  变量类型后面跟两个点。(可选)
4.  给变量赋值的等号。

下面是一个变量声明
的例子

```
// Using var 
var a:String = "Something Interesting" 

// Using val 
val b:String = "Something even more interesting" 
```

如你所见，我们可以使用 var 或 val 关键字来声明一个变量。它们之间的主要区别是可变性或改变的能力。使用 var，你可以改变变量的值，而使用 val，你不能，当我说值时，我指的是实例，因为在 kotlin 中，一切都是对象。当我们使用 val 时，对象的属性能够改变，但是它所引用的实例却不能。

值得注意的另一件有趣的事情是，val 声明的变量的行为类似于常量值，但有一个主要的区别:val 允许给它分配一个将解析为值的表达式，而 const 只允许一个原语或字符串文字。

这里我们可以看到一个 val vs const
的例子

```
val value:Int = 1+1 // Valid const 

val constant:Int = 1+1 //Invalid const 
val constant2:Int = 1 //Valid 
```

这里有 var 和 val

```
// Using var 
var a:Int = 5 
a = 6 // Valid 

// Using val 
val b:Int = 5 
b = 6 // Invalid 
```

#### 初始化

Kotlin 不允许空值，这意味着你不能声明一个没有赋值的变量，至少不能用标准声明。即使使用可空值，由于 kotlin 的可读性促进理念，您需要在声明变量的初始状态时显式赋值(甚至空值)。

但是在我们不知道变量的值的情况下，比如一个类中的实例变量，怎么办呢？我们可以做到这一点，但我们必须明确地告诉科特林我们的意图。我们通过在变量声明前使用关键字 **lateinit** 来做到这一点。

你可以把 **lateinit** 当做一个承诺。承诺在以后使用变量之前初始化变量。然后编译器相信你，让你按自己的方式去做，但是万一你有点冒险，如果你试图在赋值前访问它，它会抛出一个异常。

现在，让我们来看看这是怎么回事

```
//Without lateinit 
var a:Int //Error 
a = 5 //Error 

//With lateinit 
lateinit b:Int // valid 
b.toString() // Throws exception 

b = 5 
b.toString() // valid 
```

#### 鸭子打字

Kotlin 是一种[静态类型语言](https://hackernoon.com/i-finally-understand-static-vs-dynamic-typing-and-you-will-too-ad0c2bd0acc7)，这意味着所有的变量类型都在编译时被评估，但这并不意味着你需要显式地指定变量类型。Kotlin 有一个名为 duck-typing 的系统，允许它根据分配给它的值来推断类型，让您不必亲自动手。

不要与其他语言(例如 javascript)中的动态类型特征相混淆，动态类型特征允许您在运行时根据变量包含的值来更改变量的类型。虽然 kotlin 可以推断变量类型，**这发生在编译时**，一旦变量类型被确定，就永远不能改变。

# 类型

在这里，我们将讨论变量可以开箱即用的常见类型(或信息种类)。其中大部分你应该非常熟悉，但与此同时，它们也有一些小小的变化。

#### 数字

数字(在其任何变体中)都是对象，因此它们拥有方法和属性。

在 JVM 上运行 kotlin 的特殊情况下，这些数字被存储为原语，如果使用可空的数字，装箱/拆箱过程会自动完成。

kotlin 的一个奇怪之处在于，数字不是隐式转换的。这意味着你不能在没有事先进行造型的情况下给一个整型变量赋一个长整型变量。

```
var small:Int = 5 
var large:Long = small // Invalid 

var larger:Long = (Long) small //Valid 
```

另一件奇怪的事情是，你可以给一个数字文字添加任意数量的下划线字符，使其更具可读性，kotlin 编译器会删除这些下划线，并使用剩余的数字作为其值。

这意味着，

```
var number1:Int = 1\_000 
var numer2:Int = 1000 
number1 == numer2 //Evaluates to true 
```

#### 字符

字符代表一个字母或数字，以及几乎任何你能在 ASCII 表中找到的符号。您可以使用单引号来定义它们。

```
val c: Char = 'C' 
```

在某些语言中，您可以使用字符的数字表示来与 int 进行比较。在 kotlin 中，这不是现成的，而是必须使用 toInt()方法来完成转换。

#### 字符串

如果你过去做过文本操作，你会对 kotlin 中的字符串感到很舒服。kotlin 处理字符串的方式非常标准，方法和属性非常简单易懂。

以下是关于这种数据类型的几个要点:

1.  **字符串是字符**的集合，因此可以使用集合的方式通过使用索引操作符来访问它们的元素，并且还可以访问集合的方法来操作其内容。
2.  **字符串是不可变的**，这意味着任何“修改”现有字符串的操作，实际上是分派先前的字符串并创建一个新的结果。
3.  有两种类型的字符串，转义的和未转义的。对于第一种类型，任何特殊字符都必须通过使用特殊转义字符来显式包含，而另一种则足以包含在字符串中。
4.  **你可以在字符串**中计算变量和表达式。这个过程叫做字符串模板，你可以用它在变量前加上货币符号($)，或者在表达式中用花括号括起来。

好了，是时候用一些例子把这些话付诸行动了。首先，让我们使用索引操作符来获取字符串中给定位置的字符。就像数组一样，您可以在左括号和右括号旁边使用变量名，括号内的数字表示您想要的元素的位置。

```
var text:String = "This is a text!" text[1] // Returns h 
```

现在，让我们来看看如何转义和不转义字符串相互比较。请注意，原始字符串(非转义)变量需要三个双引号，而转义的变量只需要两个。

在这个例子中，我们可以看到转义的字符串不能识别文本中的换行符，而另一个却可以。如果我们希望有一个换行符，在转义字符串中我们必须显式地添加转义字符(\n)。

```
var escaped:String = "first line second line" // Throws a compiler error var raw:String = """first line second line""" // Valid 
```

最后，我们将看到如何从字符串
中获取变量值或执行表达式

```
var age:Int = "29" 
var text:String = "Hello!, I'm Raul and I'm $age years old" // That prints Hello!, I'm Raul and I'm 29 years old 
var expression:String = "1+1 = ${1+1}" // That prints 1+1 = 2 
```

# 待续…

我希望这些信息足以让您大致了解 kotlin 是如何与变量和类型交互的。关于 kotlin 的主要数据类型还有一些内容要介绍，但是我们有了一个良好的开端。

接下来，我们将看到数组和集合，以及它们如何工作并与它们所保存的信息进行交互。我们还将看到 kotlin 特有类型的 Pairs，与其他语言中的元组非常相似。

这篇文章是系列文章的一部分，旨在向您介绍这种语言，并体验 kotlin 的工作方式。你可以在这里找到:
[科特林语指南](https://raulmonteroc.com/mobile/kotlin/a-guide-to-the-kotlin-language/)
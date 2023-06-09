# 科特林扩展函数

> 原文：<https://dev.to/tedhagos/kotlin---extension-functions-4e10>

在 Java 中，如果我们需要向类添加功能，我们可以向类本身添加方法，或者通过继承来扩展它。Kotlin 中的扩展函数允许在不使用继承的情况下向现有类添加行为，包括用 Java 编写的类。我们可以为现有的类定义额外的行为，并在该类之外进行定义。

为了演示扩展函数，让我们从一些简单的代码开始，并在进行过程中逐步构建。

```
fun main(args: Array<String>) {
   val msg = "My name is Maximus Decimus Meridius"
   println(homerify(msg))
   println(chanthofy(msg))
   println(terminatorify(msg))

 }

 fun homerify(msg: String) = "msg -- woohoo!"
 fun chanthofy(msg: String) = "Chan, msg , tho"
 fun terminatorify(msg: String) = "$msg -- I'll be back" 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码示例有三个函数，它们接受一个字符串参数，向其中添加一些字符串，然后将它们返回给调用者，这很简单。它实际上是可用的，但是我们可以通过将所有三个函数放在一个公共类中来对它进行进一步的整合，这将成为我们的实用程序类，这样的类可能看起来像这样

```
fun main(args: Array<String>) {
   val msg = "My name is Maximus Decimus Meridius"

   val util = StringUtil()
   println(util.homerify(msg)) 
   println(util.chanthofy(msg))
   println(util.terminatorify(msg))
 }

/*
  The StringUtil class consolidates our 3 methods as member functions.
  This is a very common Java practice
*/
 class StringUtil {
   fun homerify(msg: String) = "msg -- woohoo!"
   fun chanthofy(msg:String) = "Chan, msg , tho"
   fun terminatorify(msg: String) = "$msg -- I'll be back"
 } 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码可以直接使用。将多少有些关联的方法合并到一个实用程序类中被认为是一个好主意。Kotlin 通过扩展函数让我们走得更远。如果我们可以将自己的方法附加到 String 类中，这不是很好吗？像这样。

```
fun String.homerify() = "$this -- woohoo!" 
```

Enter fullscreen mode Exit fullscreen mode

这看起来似乎很简单，但这确实是编写一个扩展函数所需要的。扩展函数引入了接收者类型和接收者对象的概念。

1

在我们的示例扩展函数中，接收器类型是 String，这是我们想要添加扩展函数的类。receiver 对象是该类型的实例，在我们的示例中是“我的名字是 Maximus Decimus Meridius”。当您将一个扩展函数附加到一个类型上时，比如我们示例中的字符串，扩展函数可以使用关键字 this 引用 receiver 对象。从所有的意图和目的来看，扩展函数看起来就像在接收器类型上定义的任何成员函数。因此，扩展函数能够引用它是有意义的。

好了，现在让我们把 chantofy，homerify 和 terminatory 添加到 String 类中。

```
fun main(args: Array<String>) {
   val msg = "My name is Maximus Decimus Meridius"

   println(msg.homerify())
   println(msg.chanthofy())
   println(msg.terminatorify()) 
 }

 fun String.homerify() = "this -- woohoo!"
 fun String.chanthofy() = "Chan, this , tho"
 fun String.terminatorify() = "$this -- I'll be back" 
```

Enter fullscreen mode Exit fullscreen mode

用 Kotlin 编写实用函数完全没问题，但是有了扩展函数，使用它们似乎更自然，因为它增加了代码的语义价值。使用扩展函数语法感觉更自然。

顺便说一下，如果你在 Java 中，你可以在不扩展类的情况下给现有的类(尤其是最终类)增加功能。你可以通过*合成*来实现。我在 dev.to 上看过一篇文章，就是这么做的。你可以在这里找到它。

[![bertilmuth](img/dcb4e631a1c765f4fceebb73d53cd256.png)](/bertilmuth) [## Java 中的最终类

### bertil Muth 7 月 1 日 181 分钟阅读

#java #programming #coding #oop](/bertilmuth/final-classes-in-java-2jh2)

[![](img/835cc5d342acc97ebe3515a542ba4093.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q5JES4IT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/WorkingDev/%257E4/VSpcDf5VrX8)
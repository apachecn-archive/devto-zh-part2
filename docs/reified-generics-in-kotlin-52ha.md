# Kotlin 中的具体化泛型

> 原文：<https://dev.to/tedhagos/reified-generics-in-kotlin-52ha>

先来处理一下*具体化*的意思。它的意思是让事情变得真实，我们在同一个语句中使用具体化和泛型的原因是因为 Java 的类型擦除。

类型擦除的意思和你想的完全一样。Java 和 Kotlin 在运行时清除泛型类型信息。这有很好的理由，但不幸的是，我们不会讨论语言设计如此的原因——但我们会讨论它的影响。因为*类型删除*，你不能执行任何反射活动，也不能对类型进行任何运行时检查。所以，下面的代码是行不通的。

```
fun checkInfo(items:List<Any>) { 
  if(items is List<String>) {   // (1)
    println("item is a list of Strings")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**(1)** 这一行不会编译，错误是“无法检查擦除类型的实例”

`is`关键字在运行时对泛型类型不起作用，智能强制转换会因为类型擦除而中断。如果你对列表的运行时类型有一些信心，你可以做一个推测性的决定，并使用关键字`as`进行转换，就像这个

```
val i = item as List<String> 
```

Enter fullscreen mode Exit fullscreen mode

编译器会让你通过，但这是一件危险的事情。让我们再考虑一个例子，我们可以建立一个更强的案例来解释为什么我们需要在运行时保留类型信息。

假设我有一个对象列表，`Programmer`和`Tester`对象。我想创建一个函数，可以传递一个类型参数，并使用该类型参数过滤列表。我想让函数返回过滤后的列表。

以下代码向我们展示了如何做到这一点的示例——当然，由于类型擦除问题，这是行不通的，但是先通读一遍，我们稍后会修复它。

```
fun main(args: Array<String>) {
  val mlist = listOf(Programmer("Ted"), Tester("Steph")) // (1)
  val mprogs = mlist.typeOf<Programmer>() // (2)

  mprogs.forEach { // (3)
    println("${it.toString()} : ${it.javaClass.simpleName}")
  }
}

fun <T> List<pass:[*]>.typeOf() : List<T> { // (4)

  val retlist = mutableListOf<T>() // (5)
  this.forEach {
    if (it is T) {  // (6)
      retlist.add(it) // (7)
    }
  }
  return retlist  // (8)
}

open class Employee(val name:String) {
  override fun toString(): String {
    return name
  }
}
class Programmer(name:String) : Employee(name) {}
class Tester(name:String) : Employee(name) {} 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建一个程序员和测试人员对象的列表

**(2)** 让我们调用一个名为 typeOf 的(列表类型的)扩展函数。我们将 Programmer 作为类型参数传递，这意味着，我们希望这个函数只返回 Programmers 对象的列表

我们正在遍历列表中的每一项。我们打印 name 属性和 Java simpleName

**(4)** 现在我们来看扩展函数的定义。我们在定义一个类型参数，我们用 T 作为这个函数的返回类型。此外，我们希望这个函数可以处理任何类型的列表，因此语法

让我们定义一个可变列表，我们将用它来保存过滤后的列表

**(6)** 这是不会编译的代码，因为我们在运行时已经不知道这是什么类型的列表了。像 Java 一样，Kotlin 删除类型信息。但是让我们假设 Kotlin 确实保留了泛型类型信息；如果是这种情况，那么这段代码是没问题的

**(7)** 如果条件没问题，我们把当前项加到返回值上

**(8)** 最后，让我们返回过滤后的列表

如果`List.typeOf`能够(在运行时)记住它是什么类型的列表，那么最后一个代码示例将会完美地工作。事实证明，Kotlin 实际上可以让泛型类型在运行时记住这些东西。我们只需要使用几个特殊的词，比如`inline`和`reified`。让我们看看如何在代码中做到这一点。

```
inline fun <reified T> List<pass:[*]>.typeOf() : List<T> { // (1)

  val retlist = mutableListOf<T>()
  this.forEach {
    if (it is T) {
      retlist.add(it)
    }
  }
  return retlist
} 
```

Enter fullscreen mode Exit fullscreen mode

**(1)** 将函数**内联**，在类型参数前使用**具体化**关键字。这样做之后，函数可以在运行时保留类型信息

您只能具体化内联函数。当你内联一个函数时，编译器会用实际的字节码(不仅仅是函数的地址)替换对该函数的每次调用。这就像在调用函数的地方复制并粘贴函数的字节码一样。这就是编译器知道您用作类型参数的确切类型的方式。因此，编译器可以为用作类型参数的特定类生成字节码。

如果我们对编译器为我们的具体化函数生成的字节码进行逆向工程，它可能看起来像下面的代码。

```
val retlist = mutableListOf<Programmer>()
this.forEach {
  if (it is Programmer) {
    retlist.add(it)
  }
}
return retlist 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们不再测试 if `it is T`，而是测试 if `it is Programmer`。生成的字节码引用了一个特定的类(程序员)，而不是像`<T>`那样的类型参数。这就是具体化函数不受类型擦除影响的原因。这当然会增加运行时程序的大小，所以要谨慎使用。

如果你想自己尝试一下，这里有完整的代码清单

```
fun main(args: Array<String>) {
  val mlist = listOf(Programmer("Ted"), Tester("Steph"))
  val mprogs = mlist.typeOf<Programmer>()

  mprogs.forEach {
    println("${it.toString()} : ${it.javaClass.simpleName}")
  }
}

inline fun <reified T> List<*>.typeOf() : List<T> {

  val retlist = mutableListOf<T>()
  this.forEach {
    if (it is T) {
      retlist.add(it)
    }
  }
  return retlist
}

open class Employee(val name:String) {
  override fun toString(): String {
    return name
  }
}
class Programmer(name:String) : Employee(name) {}
class Tester(name:String) : Employee(name) {} 
```

Enter fullscreen mode Exit fullscreen mode

图片来源:yenwen / E+ / Getty Images
免责声明:无意侵犯版权
# 科特林函数的基本介绍

> 原文：<https://dev.to/tedhagos/a-really-really-basic-intro-to-kotlins-functions-2le3>

Kotlin 函数是使用`fun`关键字定义的，后面是函数名、一对括号、可选参数、函数的返回类型，然后是函数体，即一对花括号。它非常像 Java 函数，除了在 Java 中，我们没有`fun`(双关语)，函数的类型写在函数的左边(在 Java 中)，而在 Kotlin 中，函数的返回类型在函数名的右边。

当函数声明返回类型时，类型和函数名必须用冒号分隔。比如这个函数接受一个浮点参数并返回一个浮点值

```
fun inchesToCm(inches:Float) : Float {
  return inches * 2.54
} 

fun main(args:Array<String) {
  println(inchesToCm(10))
} 
```

Enter fullscreen mode Exit fullscreen mode

如果函数体中只有一条语句(就像前面的代码示例)，您可以将其编写为表达式。像这样

```
fun inchesToCm(inches:Float) = inches * 2.54 
```

Enter fullscreen mode Exit fullscreen mode

在函数表达式中，您不必编写返回类型，因为 Kotlin 可以从 RHS(赋值操作符的右边)中推断出它。

当一个函数(不是作为表达式编写的)没有指定它的返回类型时，编译器会认为它返回了单元类型。把 Unit 想象成 Java 的 void 的等价物，就像这样

```
fun talk() {       // (1)
  println("Hello") // (2)
} 
```

Enter fullscreen mode Exit fullscreen mode

(1)我们的函数没有声明的返回类型。因此，它是自动单位

(2)返回类型为 Unit 的函数体中不能有任何 return 语句；就像 Java 中的 void 方法一样

函数可以写在 3 个地方；作为顶级(你已经看到了)在类内(你也看到了)和在另一个函数内。

```
fun foo() {                 // (1)
  fun baz() : String{       // (2)
    return "Bar"
  }
  println("Foo ${baz()}")   // (3)
}

fun main(args:Array<String>) {
  foo()
  baz() // (4)
  println("${Person(lastname="Doe", firstname = "John").fullname()}")
}

class Person(val firstname:String, val lastname:String) {
  fun fullname() = "$firstname $lastname"  // (5) 
```

Enter fullscreen mode Exit fullscreen mode

(1) `foo()`是一个顶级函数。它没有嵌套在类或其他函数中

(2) `bazz()`嵌套在函数`foo()`内。也就是说 baz 的作用域是 foo。也就是说，它只能在`foo()`的体内被调用

(3)该调用是可以的，`baz()`可在`foo()`内调用

(4)不 OK。本次呼叫将失败，`baz()`不能从`main()`呼叫，您只能从`foo()`内呼叫`baz()`

(5)像 Java 一样，你可以把函数放在一个类中

## 默认函数参数

您可以为函数参数分配默认值，就像这样

```
fun connectToDb(hostname: String = "localhost",
                username: String = "mysql",
                password:String = "secret") {
} 
```

Enter fullscreen mode Exit fullscreen mode

因为有默认的参数，你可以调用这个函数而不用传递任何参数。像这样

```
connectToDb() 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以只传递两个参数，就像这样

```
connectToDb("mycomputer", "ted") 
```

Enter fullscreen mode Exit fullscreen mode

“我的电脑”是主机名，“ted”是用户名。我们传递的两个参数对应于函数中定义的前两个参数。

## 命名参数

除了默认的函数参数，我们还可以通过在调用点命名参数来调用函数。

```
fun main(args:Array<String>) {
  connectToDb(hostname="neptune",
              username="saturn")
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们只传递了两个参数，但是因为我们的函数有默认参数，所以没问题。

与此相关的一些内容有:

[![tedhagos](img/d33df494b4c7494599bd1bc717f8b9d1.png)](/tedhagos) [## 科特林扩展函数

### ted Hagos 9 月 30 日 183 分钟阅读

#kotlin #beginners](/tedhagos/kotlin---extension-functions-4e10)

[![](img/f844e6f0ae02837abc70d4e4100753c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IU8kI4wJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/WorkingDev/%257E4/qy6HpWQtklM)
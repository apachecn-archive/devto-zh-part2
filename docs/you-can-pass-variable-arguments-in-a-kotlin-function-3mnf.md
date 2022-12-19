# 您可以在 Kotlin 函数中传递变量参数

> 原文：<https://dev.to/tedhagos/you-can-pass-variable-arguments-in-a-kotlin-function-3mnf>

与 Java 一样，Kotlin 中的函数也可以接受任意数量的参数。语法与 Java 有点不同，我们没有在类型`...`后使用三个点，而是使用了`vararg`关键字。

```
fun<T> manyParams(vararg va : T) {  // (1)
  for (i in va) { // (2) 
    println(i)
  }
}

fun main(args: Array<String>) {
  manyParams(1,2,3,4,5)  // (3)
  manyParams("From", "Gallifrey", "to", "Trenzalore")  // (4) 
  manyParams(*args) // (5) 
  manyParams(*"Hello there".split(" ").toTypedArray()) // (6)
} 
```

Enter fullscreen mode Exit fullscreen mode

**(1)**vararg 关键字让我们为
这个函数接受多个参数。在这个例子中，我们声明了一个函数，它有一个类型化的
参数；它是通用的。我们不必为了让
处理变量参数而将它声明为泛型，我们只是选择这样做，这样它就可以处理各种类型

这是一个简单的循环机制，因此我们可以打印参数中的每一项

我们可以传递整数，我们可以传递任意多的整数，因为 manyParams 接受可变数量的参数

它也适用于字符串

**(5)** 和 Java 里一样，我们可以把一个数组传递给一个接受变量参数的函数。我们需要使用 spread operator *来解包数组。这就像手动一个接一个地传递数组的各个元素

**(6)**split()成员函数将返回一个*数组列表*，你可以将它转换成一个*数组*，然后使用 spread 运算符，这样你就可以将它传递给一个 *vararg* 函数

[![](../Images/dca45c19ad326004794581c75650d371.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ne0046Pw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/WorkingDev/%257E4/Nw_buFAs-sM)
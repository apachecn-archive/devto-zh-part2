# Android-kot Lin Digest # 5–零安全性

> 原文：<https://dev.to/itscoderslife/android-kotlin-digest-5--null-safety-4j6l>

编程中最令人恼火的事情之一是应用程序由于 null / nil 对象而崩溃。

在 Objective-C 中，你可以**调用 nil 对象上的一个方法**它不会崩溃，但它永远不会给出预期的结果，我们开发人员最终会花费大量的时间去调试寻找罪魁祸首。

在 Java 中，它曾经是**NullPointerException**(NPE)。以及应用崩溃的原因。

现代语言通过零安全机制解决了这个问题。

Swift 使用期权**？**并且解开**！**选装件。Swift 还有 *if let* 和 *guard let* 语句，用于在 *nil* 值的情况下安全解包和处理。

在这里，我们将讨论科特林如何确保零安全

```
var str: String = "abcd"
str = null // This line gives a compilation error 
```

Enter fullscreen mode Exit fullscreen mode

```
var str: String? = "abcd"
str = null // This pass thru since str is declared String? 
```

Enter fullscreen mode Exit fullscreen mode

现在假设你想在可选的情况下检查 str 的长度，为了安全起见，你通常需要:

```
val len = if (str != null) str.length else -1 
```

Enter fullscreen mode Exit fullscreen mode

还是用**安全呼叫**接线员(**？。**)喜欢:`str?.length`

*(在 swift 中我们称之为可选链接)*

在 Kotlin 中，前面的 if else 语句也可以使用所谓的**猫王**操作符(* *)来编写？:** )

```
val len = str?.length ?: -1 
```

Enter fullscreen mode Exit fullscreen mode

如果开发人员确定引用有值，那么使用非空断言操作符(**！！** )

```
val len = str!!.length 
```

Enter fullscreen mode Exit fullscreen mode

(这样做的问题是，如果引用为空，则抛出 **NullPointerException** )

快乐编码！
# if-then，Java 中的一个语句；但是科特林语中的一个表达

> 原文：<https://dev.to/tedhagos/if-then-a-statement-in-java-but-an-expression-in-kotlin-1nam>

Kotlin 中的 *if* 结构与 Java 中的几乎一样。

```
val theQuestion = "Doctor who?"
val answer = "Theta Sigma"
val correctAnswer = ""

if (answer == correctAnswer) {
 println("You are correct")
} 
```

Enter fullscreen mode Exit fullscreen mode

再比如，这一次，我们用`else if`和`else`子句。

```
val d = Date()
val c = Calendar.getInstance()
val day = c.get(Calendar.DAY_OF_WEEK)

if (day == 1) {
 println("Today is Sunday")
}
else if (day == 2) {
 println("Today is Monday")
}
else if ( day == 3) {
 println("Today is Tuesday")
}
else {
  println("Unknown")
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，它看起来很像你在 Java 中是怎么做的。不是吗？Kotlin 的不同之处在于，`if`结构不是一个语句，而是一个表达式。这意味着，您可以将`if`表达式的结果赋给一个变量。像这样

```
val theQuestion = "Doctor who"
val answer = "Theta Sigma"
val correctAnswer = ""

var message = if (answer == correctAnswer) {
 "You are correct"
}
else{
 "Try again"
} 
```

Enter fullscreen mode Exit fullscreen mode

如果在 If 和 else 块中只有一条语句，你甚至可以缩短代码，就像这样

```
var message = if (answer == correctAnswer) "You are correct" else "Try again" 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/9532ecb9eebfbfd85162e549daa842e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DuXg3mGT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/WorkingDev/%257E4/FIKDlWIJNVM)
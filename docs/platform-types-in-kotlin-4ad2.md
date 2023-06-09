# 科特林的平台类型

> 原文：<https://dev.to/preslavrachev/platform-types-in-kotlin-4ad2>

*注:这篇文章最初发表在我的[博客](https://preslav.me/2018/03/18/platform-types-in-kotlin/)上。*

与 Kotlin 一起工作过一段时间后，我想您现在一定已经见过了`?`和`!!`操作符。如果你还没有，我建议你把这篇文章收藏起来，等你对这门语言的基础有了更多的经验后再来看看。

开个玩笑，我们简单回顾一下两者。如果你有一个可以是`null`的变量，Kotlin 要求你通过给它的类型添加一个问号来把它标记为可选的:

```
var person: Person? 
```

Enter fullscreen mode Exit fullscreen mode

要访问`person`变量的属性和方法，您必须使用安全调用运算符(`?` ):

```
// the end result will either be a value of null, never an NPE
var name = person?.name

// The opertor allows for deep chaining as well
var firstName = person?.name?.firstName 
```

Enter fullscreen mode Exit fullscreen mode

或者如果你真的确定这个变量不能为空，你可以用一个*非空断言* ( `!!` ):
来断言它

```
// an exception will be thrown if person == null
var name = person!!.name 
```

Enter fullscreen mode Exit fullscreen mode

与 Java 的不同之处在于，通过应用 *not-null-assertion* ，这是开发人员自己的责任，而不是编译器的责任。

## 好吧，那`!`呢

事实上，除了明确指定类型为可选类型(`Person?`)之外，Kotlin 还向我们展示了另一种野兽，称为*平台类型*，通过添加一个感叹号来指定(例如`Person!`)。这个概念是出于兼容性的考虑，在从空的不安全平台(如 Java)访问代码时创建的。经常出现的情况是，当使用 Java 库时，许多方法返回`SomeType!`，因为 Kotlin 编译器无法推断结果是否可为空。

作为开发人员，您应该像对待可选类型一样对待平台类型。它们只是令人讨厌，因为它们需要看似不必要的样板文件。作为 Kotlin 开发人员，我们喜欢围绕非空值设计方法契约，避免将可选类型作为参数签名的一部分。如果有些东西是可选的，人们宁愿分配一个合理的默认值，也不愿接受一个可选的参数。因此，Java 风格的`if (person == null)`检查虽然完全合法，但看起来非常不雅，Kotlin 开发人员在不得不这样做时会发出咕哝声。

如果你使用一个 Java API，它返回给你`SomeType!`，然后你想在一些 Kotlin 代码中使用它，期望得到`SomeType`，你有三个选择:

1.  做一个 Java 风格的`null`检查(😒)
2.  使用`!!`显式地/展开/变量(但是你必须 100%确定它永远不会返回 null，否则你又回到了 Java 世界)
3.  使用内置的 let magic 函数

Let 将 lambda 作为参数，并将变量的值作为参数传递给这个 lambda。请看下面的代码:

```
var result = someJavaApi.doSomething() // returns SomeType!
result?.let { someKotlinApi.doSomething(it) } // someKotlinApi.doSomething expects SomeType 
```

Enter fullscreen mode Exit fullscreen mode

上面这段代码编译完美，解决了平台类型的问题。神奇之处在于，只有当`result`的值不是`null`时，才会调用`let`，否则对它的调用将被完全跳过。如果到达了`let`，Kotlin 编译器会足够聪明地将`it`(lambdas 中的默认参数名)的类型推断为`SomeType`，而不是`SomeType!`或`SomeType?`。

你会选择哪种方法？请在评论中告诉我。

* * *

## 进一步阅读

[Kotlin 中的单感叹号-栈溢出](https://stackoverflow.com/a/43826700/1107412)
T3】从 kot Lin 调用 Java-kot Lin 编程语言
[let - Kotlin 编程语言](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html)
[kot Lin 函数的区别:' let '、' apply '、' with '、' run '和' also'](https://proandroiddev.com/the-difference-between-kotlins-functions-let-apply-with-run-and-else-ca51a4c696b8)
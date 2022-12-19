# Kotlin 解决了 Java 泛型的难题

> 原文：<https://dev.to/rapasoft/pains-of-java-generics-solved-in-kotlin-4g4a>

## 问题

> 为什么这在 Java 中不能编译？

```
Optional<Integer> optionalInteger = Optional.of(1);
Optional<Number> optionalNumber = optionalInteger;

int i = optionalNumber.get().intValue(); 
```

Enter fullscreen mode Exit fullscreen mode

在 Java 中使用泛型时，我通常非常保守。当我试图变得“聪明”时，我通常会把事情搞得过于复杂，最终导致代码中到处都是注释。编程语言应该总是既容易写又容易读。那么，为什么不能在上面的例子中进行造型呢？

我希望上述方法能够奏效，因为:

*   `Number`是 Java 中`Integer`类的父类
*   `optionalNumber`是生产`Number`

## Java“解决方案”

可以用`extends` / `super`关键字指定上界来解决。

```
Optional<? super Integer> optionalInteger = Optional.of(1);
Optional<Number> optionalNumber = (Optional<Number>) optionalInteger;

int i = optionalNumber.get().intValue(); 
```

Enter fullscreen mode Exit fullscreen mode

这可以工作，但是我们会在第二行得到`Unchecked cast`警告，这*在 Java 泛型的世界里*有意义，但是仍然很难看。另外，当使用`<? super Integer>`时，它对读者说了什么？

## 科特林解

Kotlin 已经用 [**声明解决了这个问题——使用关键字`in` / `out`的站点差异**。对于这个例子:
，我们将使用`get()`函数模拟`Optional`类(因为 Kotlin 中没有)](https://kotlinlang.org/docs/reference/generics.html#declaration-site-variance)

```
class Optional<out T>(val t: T) {
    fun get(): T = t
}

val optionalInteger = Optional(1)
val optionalNumber: Optional<Number> = optionalInteger

val i = optionalNumber.get().toInt() 
```

Enter fullscreen mode Exit fullscreen mode

通过用关键字`out`注释`T`参数，我们将指定`T`将总是被用作输出值(例如，产生的)，因此执行上述转换是安全的(因为产生的值可以安全转换)。同样，如果你尝试添加成员函数`fun set(t: T)`，你会得到一个编译器警告，即`T`是`out`参数，但出现在`in`位置。它易于书写、阅读和理解。

正如我提到的，Kotlin 没有单独的`Optional`类。取而代之的是，它有一些很酷的语言特性，可以在任何其他现代语言中找到这些特性:

```
haveReadArticle?.giveItA❤️() 
```

Enter fullscreen mode Exit fullscreen mode

😎
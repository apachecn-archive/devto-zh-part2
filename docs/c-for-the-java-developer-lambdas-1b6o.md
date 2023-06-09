# 面向 Java 开发人员的 c#:Lambdas

> 原文：<https://dev.to/rnowif/c-for-the-java-developer-lambdas-1b6o>

lambda 是一个匿名函数，可以赋给变量，作为方法的参数传递，并且可以随时调用。我们可以在 Java 和 C#中找到 lambdas，得到的代码非常相似。Java lambda 可以被视为只有一个方法的接口的实现(称为*函数接口*，而 C# lambda 可以被赋值给`delegate`，这是 Java 中不存在的概念。本文旨在解释 lambdas 如何在 Java 和 C#中工作，并强调它们的不同和相似之处。

## Java 功能接口

在 Java 中，lambda 可以简单地看作一个匿名函数，它只使用一个方法实现一个接口。这种接口被称为*函数接口*，可以用`@FunctionalInterface`注释进行注释，告知编译器强制执行“只有一种方法”规则:

```
@FunctionalInterface
interface Printer {
  void print(String message);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// The function interface is implemented with a lambda
Printer standardPrinter = message -> System.out.println(message);

// The print method of standardPrinter can be invoked
standardPrinter.print("Hello World!"); 
```

Enter fullscreen mode Exit fullscreen mode

从前面的代码片段中，您应该注意到接口的实现方式完全没有区别。它可以是一个 lambda，一个具体的类，甚至是一个匿名类。无论如何，您可以简单地调用接口的`print`方法。

JDK 中有一些预定义的[通用功能接口](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)，可以直接使用。主要的如下所述:

| 名字 | 方法 | 作用 |
| --- | --- | --- |
| [T2`Function<T,R>`](https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html) | `R apply(T t)` | 接受给定类型 T 的参数并返回类型 R 的对象 |
| [T2`Consumer<T>`](https://docs.oracle.com/javase/8/docs/api/java/util/function/Consumer.html) | `void accept(T)` | 接受给定类型 T 的参数并做一些有用的事情(通常有副作用) |
| [T2`Predicate<T>`](https://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html) | `boolean test(T)` | 接受给定类型 T 的参数并返回一个布尔值(类似于`Function<T, Boolean>`) |
| [T2`Supplier<T>`](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html) | `T get()` | 返回 T 类型的对象 |

## C#委托人

在 C#中，lambda 可以赋给一个委托，这个委托是封装了一个方法的类型:

```
delegate void Print(string message); 
```

Enter fullscreen mode Exit fullscreen mode

```
// A lambda is assigned to the delegate
Print print = message => System.Console.WriteLine(message);

// print can be directly invoked as a method
print("Hello World!"); 
```

Enter fullscreen mode Exit fullscreen mode

像在 Java 中一样，一些委托已经在。NET 框架，主要的描述如下:

| 方法 | 作用 |
| --- | --- |
| [T2`TResult Func<in T,out TResult>(T arg)`](https://docs.microsoft.com/en-us/dotnet/api/system.func-2) | 接受给定类型 T 的参数，并返回类型 TResult 的对象 |
| [T2`void Action<in T>(T obj)`](https://docs.microsoft.com/en-us/dotnet/api/system.action-1) | 接受给定类型 T 的参数并做一些有用的事情(通常有副作用) |
| [T2`bool Predicate<in T>(T obj)`](https://docs.microsoft.com/en-us/dotnet/api/system.predicate-1) | 接受给定类型 T 的参数并返回一个布尔值(类似于`Func<T, bool>`) |

## 结论

从 Java 的角度来看，在 API(比如 Linq)中使用 lambdas 非常简单。然而，当深入挖掘时，需要了解一些细微的差异。我发现 Java 方法更简单，因为它没有引入另一个概念，但是 C#方法更简洁，因为可以像方法一样直接调用委托。
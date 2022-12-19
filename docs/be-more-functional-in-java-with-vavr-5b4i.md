# 使用 vavr 在 java 中实现更多功能

> 原文：<https://dev.to/apium_hub/be-more-functional-in-java-with-vavr-5b4i>

随着 java 8 的发布，在 java 开发中发现了一个新的范例，但是问题出现了——这就足够了吗？如果我们可以在 java 中拥有更纯粹的函数式语言的其他功能，那会怎么样？为了满足这些需求, [vavr](http://www.vavr.io/) 被发明出来，其使命是减少代码，使其更具可读性，并通过数据的不变性增加健壮性。在本文中，我们将看到如何使用 vavr 在 java 中发挥更大的作用。

## 用 vavr 在 java 中更有功能

Vavr 包括列表和函数中的不变性，它还包括一些在其他语言中最常用的单子，更多的函数，currying 和函数中的部分应用。

## 功能

**构图**

随着 java 8 的到来，函数和双函数的概念被包含进来，用它我们可以定义一个或两个输入参数的函数，例如:

```
 Function<Integer, Integer> pow = (n) -> n * n;
assertThat(pow.apply(2)).isEqualTo(4);
BiFunction<Integer, Integer, Integer> multiply = (a, b) -> a * b;
assertThat(multiply.apply(10, 5)).isEqualTo(50); 
```

有了 vavr，我们可以拥有多达 8 个参数的函数

```
 Function1<Integer, Integer> pow = (n) -> n * n;
assertThat(pow.apply(2)).isEqualTo(4);
Function3<Integer, Integer, Integer, Integer> multiply = (n1, n2, n3) -> n1 * n2 * n3;
assertThat(multiply.apply(5, 4, 3)).isEqualTo(60); 
```

除了能够创建多达 8 个输入参数的函数，它还为我们提供了函数与操作的组合。然后。应用和。构成

```
 Function1<String, String> toUpper = String::toUpperCase;
Function1<String, String> trim = String::trim;
Function1<String, String> cheers = (s) -> String.format("Hello %s", s);
assertThat(trim
            .andThen(toUpper)
            .andThen(cheers)
            .apply(" john")).isEqualTo("Hello JOHN");
Function1<String, String> composedCheer =
cheers.compose(trim).compose(toUpper);
assertThat(composedCheer.apply(" steve ")).isEqualTo("Hello STEVE"); 
```

**提升**

使用提升，我们得到的是在构造函数时处理异常，用它函数将返回一个选项。

这在编写使用可以返回异常的第三方库的函数时非常有用。

```
 Function1<String, String> toUpper = (s) -> {
    if (s.isEmpty()) throw new IllegalArgumentException("input can not be null");
    return s.toUpperCase();
};
Function1<String, String> trim = String::trim;
Function1<String, String> cheers = (s) -> String.format("Hello %s", s);
Function1<String, String> composedCheer = cheers.compose(trim).compose(toUpper);
​
Function1<String, Option> lifted = Function1.lift(composedCheer); 
assertThat(lifted.apply("")).isEqualTo(Option.none());
assertThat(lifted.apply(" steve ")).isEqualTo(Option.some("Hello STEVE")); 
```

**部分应用**

使用部分应用程序，我们可以通过为现有函数设置 n 个参数来创建新函数，其中 n 将始终小于原始函数的 arity，并且返回将是原始 arity 函数-参数集

```
 Function2<String, String, String> cheers = (s1, s2) -> String.format("%s %s", s1, s2);
Function1<String, String> sayHello = cheers.apply("Hello");
Function1<String, String> sayHola = cheers.apply("Hola");
assertThat(sayHola.apply("Juan")).isEqualTo("Hola Juan");
assertThat(sayHello.apply("John")).isEqualTo("Hello John"); 
```

我们已经定义了一个通用的 cheers 函数，它接受两个输入参数，我们已经将它派生为两个新的 sayHello 和 sayHola，并部分应用了它，我们已经有了两个更具体的 say hello 函数，如果需要，我们可以派生更多的情况。

**阿谀奉承**

Currying 是将多个参数的函数分解成一个参数的一系列函数的技术。

```
 Function3<Integer, Integer, Integer, Integer> sum = (a, b, c) -> a + b + c;
Function1<Integer, Function1<Integer, Integer>> add2 = sum.curried().apply(2);
Function1<Integer, Integer> add2And3 = add2.curried().apply(3);
assertThat(add2And3.apply(4)).isEqualTo(9); 
```

**记忆化**

函数式编程的前提之一是有纯函数，没有副作用，这基本上意味着传递相同参数的函数总是必须返回相同的结果。

因此，如果它总是返回同一个东西，为什么不缓存呢？因为这是记忆化的任务，缓存函数的输入和输出以便只启动它们一次。

```
 void memoization() {
        Function1<Integer, Integer> calculate =
            Function1.of(this::aVeryExpensiveMethod).memoized();
        StopWatch watch = new StopWatch();
        watch.start();
        calculate.apply(40);
        System.out.println(watch.getTime());

        calculate.apply(40);
        System.out.println(watch.getTime());

        calculate.apply(50);
        System.out.println(watch.getTime());
}
private Integer aVeryExpensiveMethod(Integer number) {
    try {
        Thread.sleep(5000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    return number * number;
} 
```

## 单子

**试试**

monad Try 包括一个可能的异常的执行捕获，它的两个可能的返回值是异常失败的情况或如果运行良好的值。

一些有用的尝试方法有:

。isSuccess () ->顾名思义，通过检查是否成功来返回一个布尔值。

。isFailure () ->通过检查是否失败来返回布尔值。

get()-->获取值以防它已经正确执行，如果进行了一个 get 并且没有检查它是否完成而没有检查它是否成功，它将丢弃异常。

map () ->如果顺利，映射值，如果失败，将不执行。

getOrElse (T) ->允许在出错时返回默认值。

getOrElse (Supplier) ->允许在出错的情况下传递另一个函数。

recover(throwable-> { })-->与 getOrElse 相同，但在这种情况下，我们将拥有已抛出的异常，以便能够实现它，或者能够根据异常的类型返回不同的值。

```
 Function2<Integer, Integer, Integer> divide = (n1, n2) -> n1 / n2;
assertThat(Try.of(() -> divide.apply(10, 0)).isFailure()).isTrue();
assertThat(Try.of(() -> divide.apply(10, 5)).isSuccess()).isTrue();
assertThat(Try.of(() -> divide.apply(10, 5)).get()).isEqualTo(2);
assertThat(Try.of(() -> divide.apply(10, 0)).getOrElse(0)).isEqualTo(0); 
```

**懒惰**

Lazy 是关于第一次被评估的供应商的 monad。

```
 Lazy<List> lazyOperation = Lazy.of(this::getAllActiveUsers);
assertThat(lazyOperation.isEvaluated()).isFalse();
assertThat(lazyOperation.get()).isNotEmpty();
assertThat(lazyOperation.isEvaluated()).isTrue(); 
```

**任一**

表示两种类型的值，左和右是约定的，当值正确时放在右边，当值不正确时放在左边。

结果永远是左或右，永远不可能是两者都是。

## 数据结构

**不可变列表**

如果函数式编程的原则之一是不变性，那么当我们定义一个列表并添加条目时会发生什么？我们正在改造它。

Vavr 提供了一个列表的特殊化，列表一旦创建就不能修改，任何添加、删除、替换的操作，都会给我们一个新的应用了修改的实例。

```
 import io.vavr.collection.List;
...
//Append
List original = List.of(1,2,3);
List newList = original.append(4);
assertThat(original.size()).isEqualTo(3);
assertThat(newList.size()).isEqualTo(4);
//Remove
List original = List.of(1, 2, 3);
List newList = original.remove(3);
assertThat(original.size()).isEqualTo(3);
assertThat(newList.size()).isEqualTo(2);
//Replace
List original = List.of(1, 2, 4);
List newList = original.replace(4,3);
assertThat(original).contains(1,2,4);
assertThat(newList).contains(1,2,3); 
```

除了不变性之外，它还提供了直接操作链表的方法，而不需要遍历流，得到最小值、最大值、平均值..更多关于这个列表的信息，请查看 [javadoc](https://www.javadoc.io/doc/io.vavr/vavr/0.9.2) 。

这些是 vavr 为我们提供的主要功能，然而，还有一些功能可以帮助我们在像 java 这样的语言中使用 Vavr。

如果你想获得更多关于 vavr 的信息，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月时事通讯。

## 如果你觉得这篇关于 java 和 vavr 的文章很有用，你可能会喜欢…

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

[Scala generics III:通用类型约束](https://dev.to/apium_hub/scala-generics-iii-generalized-type-constraints-58km)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

[安卓纯 MVP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)

vavr 的帖子[在 java 中更具功能性最早出现在](https://apiumhub.com/tech-blog-barcelona/functional-java-with-vavr/) [Apiumhub](https://apiumhub.com) 上。
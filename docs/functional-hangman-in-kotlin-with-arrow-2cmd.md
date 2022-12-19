# 带箭头的科特林功能刽子手

> 原文：<https://dev.to/s_anastasov/functional-hangman-in-kotlin-with-arrow-2cmd>

几天前，我看到了这篇关于使用 [Scala ZIO](https://scalaz.github.io/scalaz-zio/) 实现一个控制台刽子手游戏的[博文。这篇博客文章是基于](https://abhsrivastava.github.io/2018/11/03/Hangman-Game-Using-ZIO/)[的这个演讲](https://www.youtube.com/watch?v=XONTFZ4afY0)由 [John De Goes](https://twitter.com/jdegoes) 为 Scala Kyiv meetup 发表的，他在那里使用函数式编程编写了一个控制台刽子手游戏。

在我看到这个帖子后，我很好奇用 Koltin 写的带有箭头的代码会是什么样子，所以我决定试着写一下。你可以在这里找到结果[。](https://github.com/LordRaydenMK/arrow-hangman)

我还在学习函数式编程，所以我在[kot linlang](https://kotlinlang.slack.com/)slack workspace 上向 arrow 维护人员寻求[反馈](https://kotlinlang.slack.com/archives/C5UPMM0A0/p1541707359083600)。他们很有帮助，我根据他们的反馈做了一些改进。

# 关键区别

我想指出的是，Scala 和 Kotlin 中的程序并不是 100%等价的。语言和使用的函数库都有差异。

## 伊奥单子

在 Scala 中，ZIO `IO[E, A]`描述了一个效果，它可能会因为一个`E`而失败，永远运行，或者产生一个`A`。在 Kotlin 版本中，我使用 Arrow 中的`IO<A>`。`IO<A>`描述一个效果，可能会因`Throwable`失败或产生一个`A`。

两个类型类都产生一个`A`。关键区别在于误差。在 Scala ZIO 中，你可以使用任何错误类型，甚至是`Nothing`来表示程序永远不会结束。在 Arrow 中，`E`类型总是`Throwable`，所以你不必指定它。

## 从控制台读写

Scala ZIO 自带了与[控制台](https://scalaz.github.io/scalaz-zio/usage/console.html)交互的内置原语。在 Kotlin 版本中，我必须自己实现`readStrLn`和`putStrLn`函数。

```
fun putStrLn(line: String): IO<Unit> = IO { println(line) }

fun getStrLn(): IO<String> = IO { 
    readLine() ?: throw IOException("Failed to read input!") 
} 
```

## 用于理解

为了便于理解，scala 语言内置了。不幸的是，Kotlin 没有同样的功能。但是 Kotlin 有[协程](https://kotlinlang.org/docs/reference/coroutines-overview.html)，所以 Arrow 团队在协程上构建了[理解，可以用类似的方式使代码更具可读性。](https://arrow-kt.io/docs/patterns/monad_comprehensions/#comprehensions-over-coroutines) 

```
//Scala with For comprehensions
val hangman : IO[IOException, Unit] = for {
    _ <- putStrLn("Welcome to purely functional hangman")
    name <- getName
    _ <- putStrLn(s"Welcome $name. Let's begin!")
    word <- chooseWord
    state = State(name, Set(), word)
    _ <- renderState(state)
    _ <- gameLoop(state)
} yield() 
```

```
//Kotlin with Arrow (Comprehensions over coroutines)
val hangman: IO<Unit> = IO.monad().binding {
    putStrLn("Welcome to purely functional hangman").bind()
    val name = getName.bind()
    putStrLn("Welcome $name. Let's begin!").bind()
    val word = chooseWord.bind()
    val state = State(name, word = word)
    renderState(state).bind()
    gameLoop(state).bind()
    Unit
}.fix() 
```

# 结论

将函数式 Hangman 从 Scala 重写为 Kotlin 是学习 FP 的一个很好的练习。Scala 和 Kotlin 与 Scala ZIO 和 Arrow 中的`IO` type 类之间存在差异，但核心原理是相同的，我们通过解决小问题来编写程序，然后组合解决方案。我们使用惰性评估，将副作用移到系统的边缘。

感谢编写这个函数式刽子手的约翰， [Abhishek](https://twitter.com/abhishes) 编写这篇文章，感谢 Arrow 团队的 [Paco](https://twitter.com/pacoworks) 、 [Raul](https://twitter.com/raulraja) 和 [Leandro](https://twitter.com/mLeandroBF) 对我的代码的反馈。
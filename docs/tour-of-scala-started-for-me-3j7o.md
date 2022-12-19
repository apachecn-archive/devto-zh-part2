# 我开始了 scala 之旅

> 原文：<https://dev.to/lukaszkuczynski/tour-of-scala-started-for-me-3j7o>

## 动机

我参加了 [Geecon 2018，今年的 IT 大会](https://2018.geecon.org)。我考虑了两个要点。首先，这是 JVM，正如我所看到的，有很多这样的东西在世界各地运行，所以即使整个世界都神奇地转换成 Python 之类的东西，也需要很长时间。必须有人让它继续运转。其次，这是我在 Java 世界流处理领域感兴趣的两个项目 [Spark](https://spark.apache.org/) 和 [Kafka](https://kafka.apache.org/) 的语言。因此，我想更加流畅，用建议的语言编写这些处理管道，这样我就可以利用所有的功能。

## 第一道小吃

Scala 不错。它不太冗长。正如我所看到的，它从 Java 中学习了一些好的特性，作为一个内置的特性应用到它的语法中(例如不变性)。这让我的第一步感觉很好。尤其是我喜欢下面的:

*   推断类型
*   不变
*   内嵌函数

## 学习

我决定带[去参观斯卡拉](https://docs.scala-lang.org/tour/tour-of-scala.html)。你可以在 [github 这里](https://github.com/lukaszkuczynski/tour-of-scala)看到我在回购中学习的方式。学习是通过编写单元测试并用`sbt test`检查其结果来完成的。然后，我继续谈另一个问题。它帮助我:

1.  更好地理解要点
2.  在学习中享受乐趣
3.  做一个循序渐进的报告，便于回顾

这很好。我花了一段时间来寻找最好的工具，并在我的 Windows PC 上运行，并在 Intellij 上运行，但最终我成功了。

这是一个我如何使用它的例子，我在 Scala 中写了 spec:

```
it should "make Fenix singleton by it being an Object" in {
        Fenix.createOrRecreateFromAshes()
        val historyOfFenix = Fenix.createOrRecreateFromAshes()
        assert(historyOfFenix == 2)
    } 
```

Enter fullscreen mode Exit fullscreen mode

使用以下`object` :

```
object Fenix {
    var livesCount = 0
    def createOrRecreateFromAshes() ={
        livesCount += 1
        livesCount
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我的 github 上，你可以检查它，现在担心。
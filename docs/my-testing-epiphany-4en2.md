# 我的测试顿悟

> 原文：<https://dev.to/ptrbrynt/my-testing-epiphany-4en2>

我做应用程序已经有 2 年了，在那段时间里，我从来不理解社区对单元测试的痴迷。我想，如果我可以运行应用程序并看到它工作，那么花大量时间编写自动化测试有什么意义呢？

答案最近浮现在我眼前。

> 你不用写测试来确保你的代码现在工作:你写测试是为了你能在将来发现你的代码出错。

[![Mind blown](img/90f39e28a21cb4bc886d27b0b20f356a.png)T2】](https://i.giphy.com/media/3o8dFn5CXJlCV9ZEsg/giphy.gif)

这里有一个例子。我们已经在 Android 的 [RotaCloud](https://rotacloud.com/) 中编写了一个文件，其中包含了一系列与时间相关的函数。下面是我们将时间戳(以秒为单位)格式化为 24 小时制的方法:

```
fun Long.secondsToTime() {
    return SimpleDateFormat("HH:mm", Locale.UK)
        .format(this * 1000)
} 
```

Enter fullscreen mode Exit fullscreen mode

这个功能对我们的应用程序非常重要:如果它坏了，突然每个人的值班表都会显示不正确的信息。

那么当一个开发者无意中像这样改变了这个函数，会发生什么呢？

```
fun Long.secondsToTime() {
    return SimpleDateFormat("HH:mm", Locale.UK)
        .format(this * 100)
} 
```

Enter fullscreen mode Exit fullscreen mode

这种变化不会导致应用程序崩溃，但这将意味着整个系统变得非常无用，我们的用户会*而不是*感到高兴。

于是我们写了一个测试:

```
@Test
fun secondsToTimeTest() {
    val timeInSeconds = 1523451825
    val expectedTime = "13:03"
    assertEquals(expectedTime, timeInSeconds.secondsToTime())
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的测试，但是在新版本发布之前运行它可以确保这个重要的功能继续工作。

我们可以扩展这些测试来覆盖一些其他的需求，例如小时应该总是两位数:`09:30`是正确的，但是`9:30`不是。

这就是我的试验顿悟。仅仅通过观察，就可以很容易地手动确定某个东西是否工作。然而，自动化测试所做的是确保*所有*应用程序的功能在未来继续工作，而不需要你手动浏览整个程序。

测试对于协作开发也非常有用:只需要求每个人在提交 pull 请求之前运行测试套件，您就会发现自己必须处理的不完整的实现要少得多。
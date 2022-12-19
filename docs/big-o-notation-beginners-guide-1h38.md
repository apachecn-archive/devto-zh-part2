# 大 O 符号:初学者指南

> 原文：<https://dev.to/metcoder/big-o-notation-beginners-guide-1h38>

在编程的世界里，像今天是你在地球上的最后一天一样写代码并不是编程艺术的主要内容。除了理解我们的代码将解决什么问题之外，我们在编写代码时还需要考虑其他变量。例如:

*   复杂性
*   可维护性
*   设计模式
*   算法
*   以及其他...

最重要的事情之一，也是这篇文章的主题，是当我们实现一个算法时，我们的应用程序的性能。

我们的算法的性能帮助我们使我们的应用程序更加用户友好，让用户只投入必要的时间来完成任务，并在厌倦和关闭窗口(浏览器或其他任何东西)之前专注于其他事情。无论用户在哪里使用我们的应用程序，设备的速度和熟练程度如何。在当今世界，时间是我们拥有的最宝贵的东西。

当我们谈论性能时，有许多变量需要考虑，比如内存的使用、磁盘空间、执行时间等等。确定我们算法性能的最重要的工具之一是 **Big-O 符号**。

# 大 O 符号

[![](../Images/da2520388dbc5792217b13d6495d6c88.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NopDcuWg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k9k0jpup4sol713xqh87.jpg)

Big-O 符号是一种工具，它让我们确定算法的复杂性，让我们衡量特定算法的性能。这个工具测量算法达到需求最高点的最坏情况。

主要的大 O 术语如下:

*   O(1) -> **常数**
*   O(n) -> **线性**
*   O(log n) -> **对数**
*   O(n ^ 2) -> **二次**
*   O(2 ^ n) -> **指数**

## O(1):恒定复杂度

**恒定复杂度**越简单，所有的算法谁，**无论输入或输出的大小，执行时间和使用的资源都将始终相同**，都有这个复杂度。无论算法被执行多少次或在哪里执行，**这个都会有完全相同的行为**。例如:

[![A function that returns the last item of an array](../Images/c26dd2be0a562c3c8f4fe77c26029a5c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ub64SPeM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5bdb46ohkczv50oq0r2r.png)

正如我们看到的，无论作为参数给出的数组有多大，行为总是相同的。唯一可能改变的是输出，因为数组可能不会一直存储相同的数据。

## O(n):线性复杂度

当**他的执行时间和/或使用的资源与输入大小**成正比(以线性方式增长)**时，我们说一个算法具有**线性复杂度**。例如:**

[![A function that prints each item stored in an array](../Images/4f95ce6240a6072d9910f37832e38f1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MqELtwRC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xaq6zgtgm1t0paetndbu.png)

为了更容易理解这种复杂性，我们可以将它与我们每天(或大部分时间)做的一项活动进行比较，例如，**看书或看电影**。我们读书或看电影的时间取决于一本书的页数和电影的时长。例如，如果一部电影有两个小时，你会花**两个小时看电影**。如果这本书有 100 页，而你在一个小时内读了 50 页，你会花**两个小时读完这本书**。

## O(log n):对数复杂度

这种复杂性存在于算法的影响**与输入大小**的对数结果成正比的地方。换句话说，当我们的输入大小为 10 时，我们需要 1 秒来完成我们算法的任务，我们需要在 2 秒内完成输入大小为 100 的完全相同的任务，在 3 秒内完成输入大小为 1000 的任务，以此类推。

[![Binary search (thanks to room_js)](../Images/09f8155309d1d1af0d7e165fdb2f674f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZSe9MqY6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uzbixq65z45o2gqbxzso.png)

一个有趣的例子是二分搜索法号。在这个算法中，我们将数组(之前已排序)分成两部分。我们将中间的索引作为参考，以获得数组中间的值。如果那个点上的数字等于我们正在寻找的数字，我们返回中间的索引，加上我们用来寻找存储我们正在寻找的数字的点的前缀。由于这个原因，如果这个数大于存储在数组中间的数，我们就在数组的右边寻找这个数，反之，如果小于这个数，我们就在数组的左边寻找。之后，我们使用这个前缀作为下一次迭代的新前缀，以递归的方式。我们重复这个循环，直到得到给定的数字。

## O( n^2):二次复杂度

当算法的影响**与输入大小的平方**成正比时，存在**二次复杂度**。这意味着，如果我们有一个长度为 4 点的数组作为输入，并且我们想比较数组是否有重复项，我们需要做 16 次比较来完成我们的任务。这种复杂性在排序算法中很常见，如[冒泡排序](https://www.geeksforgeeks.org/bubble-sort/)、[插入排序](https://en.wikipedia.org/wiki/Insertion_sort)和[选择排序](https://en.wikipedia.org/wiki/Selection_sort)。

[![A function that prints "miau" when two items are equal in an array](../Images/7846e326b5c85466ccd38c4c7e26de02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4-2wz8XL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8lx1q2uyh5s21gb1vjya.png)

在这个函数中，如果我们添加更多的 for 循环，复杂度会增加。这可能会使我们达到 O(n * n)复杂度。

## O( 2^n):指数复杂度

当一个算法具有**指数复杂度**时，这意味着**复杂度将随着输入**中每增加一项而加倍。如果 10 件需要 100 秒，11 件需要 200 秒，13 件需要 400 秒，依此类推。

[![Fibonacci sequence](../Images/754d04d87e15e252312d40000f6d1483.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gDlgS9jb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/igf2xwae7v3bdj1ixp17.png)

这并不意味着只存在 O( 2^n)，这只是出于解释的目的。可以长到 O( 3^n)、O( 4^n)等等。

# 包装完毕

我希望这能帮助你更多地理解 Big-O 符号，它对测量算法的复杂性有很大的帮助，还有很多工具可以做到这一点，但这是比较常见的工具之一。

如果你有问题或者想纠正一些错误，请在下面留下你的评论。我在下面列出了一些关于 Big-O 符号的资源。

# 更多资源

*   [https://es . khanacademy . org/computing/computer-science/algorithms/渐近符号/a/big-o-notation](https://es.khanacademy.org/computing/computer-science/algorithms/asymptotic-notation/a/big-o-notation)

*   [https://rob-bell . net/2009/06/a-初学者指南-大 o-notation/](https://rob-bell.net/2009/06/a-beginners-guide-to-big-o-notation/)

*   [https://en.wikipedia.org/wiki/Big_O_notation](https://en.wikipedia.org/wiki/Big_O_notation)

*   [http://interactive python . org/rune stone/static/python ds/algorithm analysis/bigo notation . html](http://interactivepython.org/runestone/static/pythonds/AlgorithmAnalysis/BigONotation.html)
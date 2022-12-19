# 洗牌算法和随机化，以提高算法的运行时间。

> 原文：<https://dev.to/s_awdesh/everyday-im-shuffling-im-a-card--fire-1f8b>

洗牌是每一个纸牌游戏的重要组成部分。洗牌的技术有很多，但上手洗牌和随机洗牌是最受欢迎的。

**上手洗牌**

在这种洗牌中，一组卡片从底部转移到顶部，同样的过程递归执行。

[![overhand shuffle](../Images/3b418f382504b7be0dd84b54e51ff347.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6Cyu4uGg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j45nr8lkzklnhg1rbb95.jpg)

一副牌本质上是长度为 52 的固定大小的阵列。上手洗牌将一组牌从数组的末尾放到数组的开头。这个过程重复进行，以获得良好的洗牌。

**重复洗牌**

[![riffle shuffle](../Images/b94d2af34229f93dc54e7106af6c0bad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9TZw_swZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5lczb2w52vh9gxjm12uo.gif)

这涉及到把牌切成两半，这样我们就有了两组牌，然后把它们重新洗牌，这样最后两半牌就交错在一起了。

riffle shuffle 的快速实现如下所示。

[![kata](../Images/43424d80b61364d1097d54c4ded06f7f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hf942bHP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/88cjgoufggzflq6jmx4z.png)

现有的洗牌算法运行速度更快，并给出一致的结果。这些算法依靠随机化来在每次迭代中生成唯一的随机数。

As per Wikipedia

> 如果一台计算机能够访问纯随机数，它就能够产生“完美洗牌”。

Fisher-Yates shuffle 就是这样一种使用随机数发生器实现完美洗牌的算法。这种算法是以罗纳德·费雪和弗兰克·耶茨的名字命名的，他们在 1938 年的书中首次描述了这种算法。后来，姜懿翔·克努特和理查德·德斯滕费尔德在 1964 年提出了一种改进的算法。

与在两个不同的索引处交换项目不同，该算法在数组内的元素范围之间生成一个随机数 k。每次迭代都会更新范围中的最后一个元素，因此随机生成器每次迭代都会在新的范围内工作，并且每次都会生成一个唯一的数字。

[![kata](../Images/066033070e1b4903041fcd2b0b729660.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---7F3mNVZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l377t94t4dk1fca402oc.png)

上述算法在线性时间内工作，比 riffle shuffle 更快。为 100 个整数的数组设置两种混洗算法的时间会产生以下结果。

[![kata](../Images/0965d74252c1378bb16d88eead2f91c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a9Qzd3Er--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vi8mpbfrq999nfa6q7jl.png)

编程语言在其内置的 shuffle 方法实现中使用类似的算法。Java 的 shuffle 方法的实现可以通过调用

`collections.shuffle()`

为了混洗一个不允许通过索引访问对象的链表，Java 先把它转换回数组，这样就可以进行随机访问，混洗后再把它转换回链表。

Java 文档中 Shuffle 方法的实现

[![kata](../Images/8cf6e0b36c9789343d6d8dbcedaff51c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EXLD6Fjr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/onwuxfzjhcrks36dil00.png)

### 随机化能提高算法的运行时间吗？

一个好的洗牌算法有一个函数，它始终产生一个唯一的随机数。快速排序在排序后的数组上给出二次时间性能，它通过先对排序后的数组进行随机化，然后将其送入快速排序算法，可以产生一致的 O(nlogn)结果。

有两种不同类型的随机化算法，即**拉斯韦加斯**和**蒙特卡洛**算法。IMHO，对于洗牌算法来说，没有比拉斯维加斯更好的名字了:)

**拉斯韦加斯**算法保证以时间复杂度为代价给出结果，而**蒙特卡洛**如果没有找到想要的输出，则通过提前退出来妥协对结果的保证。如果我们必须在数组中搜索一个项，拉斯维加斯算法将一直执行，直到找到期望的项，而蒙特卡洛将执行几个周期，如果没有找到项就停止。Rabin Karp 字符串搜索算法使用 Las Vegas 算法来查找输入字符串中所有匹配的子字符串。

### 应用程序

随机化算法在需要一致的好结果而不管算法的输入的应用中是有用的。火箭、卫星、飞机、密码术中的软件利用随机化在算法上获得好结果的高概率

### 资源

[https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle)
[https://www . geeks forgeeks . org/randomized-algorithms-set-2-class ification-and-applications/](https://www.geeksforgeeks.org/randomized-algorithms-set-2-classification-and-applications/)

图片取自谷歌。

### 结论

关于洗牌算法和随机化，有太多的东西需要学习和书写。在我的下一篇文章中，我们将使用语言中内置的排序函数对洗牌后的卡片进行排序。

* * *

如果你想让我写一个特定的话题，请在下面的评论区随意发表。

你可以在下面给我买杯咖啡来支持我的工作。💚💚💚💚💚💚！！
[![Buy me a ko-fi](../Images/2c34722d2ff9357df105fc9e9ec792b6.png)T3】](https://ko-fi.com/F1F0J8U1)
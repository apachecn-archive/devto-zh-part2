# 寻找重复的算法系列-给定范围的元素作为输入。

> 原文：<https://dev.to/s_awdesh/finding-duplicates-in-a-given-range-of-elements-d60>

在我之前的**寻找重复**系列[这里](https://dev.to/s_awdesh/check-if-duplicate-exists-inside-a-sorted-array-3aa5)和[这里](https://dev.to/s_awdesh/finding-duplicates-inside-an-array---easy-version-37la)的帖子中，我们已经看到了这个问题的不同变体。这是问题的另一个有趣的转折，因为现在我们已经给出了一个给定范围内的输入。

让我们看一个例子:

下面我们给出一个例子，其中给定的数组包含 1-10 范围内的元素。

[![problem](../Images/7d92c62f13d15a7b81ce36f9349b7fa4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s3Zpv_fT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sz769vzu3mhnu58q2xqp.jpeg)

[![hint](../Images/749061307313671a49ca5842d1463ef8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1hlGxk63--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ov23mr6kllun9x2dxlzu.jpeg)

当我们给定一个范围内的输入时，**计数排序**之类的算法是我们能实现的最好的算法。更多关于计数排序的是[这里](https://www.geeksforgeeks.org/counting-sort/)

受计数排序算法的启发，为了解决这个算法，我们将创建另一个计数数组，在该数组中存储索引处每个项目的计数，即在索引 2 处，我们将在计数数组中存储项目 2。

[![Algo](../Images/7a4871a487111d9a32d0d9532c54ca89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o-TIf1XE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gpq0js3dt96en4ytqqcx.jpeg)

这里是 Java 的 repl。

[https://repl.it/@AwdeshSharma1/OutrageousDirectModem?lite=true](https://repl.it/@AwdeshSharma1/OutrageousDirectModem?lite=true)

我们已经学习了另一种类型的**寻找重复**的问题，另外我们还学习了计数排序。在接下来的帖子中，我想研究与字符串相关的问题。

快乐编码。干杯。
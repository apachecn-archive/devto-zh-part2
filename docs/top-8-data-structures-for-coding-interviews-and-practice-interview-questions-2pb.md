# 编码面试和练习面试问题的 8 大数据结构

> 原文：<https://dev.to/fahimulhaq/top-8-data-structures-for-coding-interviews-and-practice-interview-questions-2pb>

瑞士计算机科学家尼古拉斯·沃斯在 1976 年写了一本书，书名是:

> 算法+数据结构=程序

40 多年后，这个等式仍然成立。这就是为什么软件工程候选人必须展示他们对数据结构及其应用的理解。

几乎所有的问题都要求考生表现出对数据结构的深刻理解。不管你是刚毕业(大学或编码训练营毕业)，还是有几十年的经验。

有时面试问题会明确提到数据结构，例如“给定一棵二叉树”其他时候，它是隐含的，例如，“我们希望跟踪与每个作者相关的书籍数量。”

即使你只是想在当前的工作中做得更好，学习数据结构也是必不可少的。先从了解基础开始。

*如果你正在为编码面试寻找数据结构方面的资源，看看基于互动&挑战的课程:[掌握编码面试的数据结构](https://www.educative.io/m/data-structures-for-coding-interviews)，其中包括 Python、Java、C++和 JavaScript 的数据结构面试课程。*

对于更高级的问题，请看[探索编码面试:编码问题的模式](https://www.educative.io/courses/grokking-the-coding-interview)。

# 什么是数据结构？

简单地说，数据结构是一个以特定布局存储数据的容器。这种“布局”允许数据结构在一些操作中是有效的，而在另一些操作中是无效的。你的目标是理解数据结构，这样你就可以选择最适合手头问题的数据结构。

## 我们为什么需要数据结构？

由于数据结构用于以有组织的形式存储数据，并且数据是计算机科学中最重要的实体，所以数据结构的真正价值是显而易见的。

无论你在解决什么问题，你都必须以这样或那样的方式处理数据——无论是员工的工资、股票价格、购物清单，甚至是一个简单的电话簿。

基于不同的场景，数据需要以特定的格式存储，我们有一些数据结构可以满足我们以不同格式存储数据的需求。

# 常用数据结构

让我们首先列出最常用的数据结构，然后我们将更详细地讨论它们:

1.  数组
2.  大量
3.  行列
4.  链接列表
5.  树
6.  图形
7.  尝试(它们实际上是树，但最好还是分别调用它们)。
8.  散列表

# 数组

数组是最简单也是最广泛使用的数据结构。堆栈和队列等其他数据结构都是从数组中派生出来的。

这是一个大小为 4 的简单数组的图像，包含元素(1、2、3 和 4):

[![Array](../Images/02e1f2ccc2f715693ee6258b62c9e610.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BoZFOixU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d6r1v3nie2chznvyckfa.png)

每个数据元素都被赋予一个称为索引的正数值，它对应于该项在数组中的位置。大多数语言将数组的起始索引定义为 0。

*以下是两种类型的数组:*

*   一维数组(如上所示)
*   多维数组(数组中的数组)

## 对数组的基本操作

*   Insert -在给定的索引处插入一个元素
*   Get -返回给定索引处的元素
*   删除给定索引处的元素
*   Size -获取数组中元素的总数

## 常见问阵面试问题

*   查找数组的第二个最小元素
*   数组中第一个不重复的整数
*   合并两个排序的数组
*   重新排列数组中的正值和负值

# 书库

我们都熟悉著名的撤销选项，它几乎出现在每个应用程序中。想知道它是如何工作的吗？想法是:你把你工作的前一个状态(限制在一个特定的数字内)按照最后一个状态先出现的顺序存储在内存中。这不能仅仅通过使用数组来实现。这就是堆栈派上用场的地方。

现实生活中的堆栈示例可以是一堆按垂直顺序放置的书籍。为了拿到中间的那本书，你需要把放在它上面的所有书都拿走。这就是后进先出法的工作原理。
这是包含三个数据元素(1、2 和 3)的堆栈图像，其中 3 位于顶部，将首先被删除:
[![Stack](../Images/f71e3b5baf68fb5779606781d557388a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---1ciAACB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xnfomtvmz0284rlasyha.png)

*栈的基本操作:*

*   推-在顶部插入元素
*   从堆栈中移除后，返回顶部元素
*   isEmpty -如果堆栈为空，则返回 true
*   Top -返回顶部元素，但不从堆栈中移除

## 常见问栈面试问题

*   使用堆栈计算后缀表达式
*   对堆栈中的值进行排序
*   检查表达式中的平衡括号

# 队列

与堆栈类似，队列是另一种以顺序方式存储元素的线性数据结构。堆栈和队列之间唯一的显著区别是，队列不使用 LIFO 方法，而是使用 FIFO 方法，这是先进先出的缩写。

现实生活中一个完美的排队例子:人们在售票亭排队。如果来了一个新的人，他将从末尾加入队伍，而不是从开始-站在前面的人将是第一个拿到票并因此离开队伍的人。

这是一个包含四个数据元素(1、2、3 和 4)的队列图像，其中 1 位于顶部，将首先被删除:

[![Queue](../Images/1b1d7f7712f8edd9b0e8a54c5e768ccb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ua5MnwE9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ltt8xj4sgrb21sux1bg3.png)

## 队列基本操作

*   Enqueue() -在队列末尾插入元素
*   Dequeue() -从队列的开头删除一个元素
*   isEmpty() -如果队列为空，则返回 true
*   Top() -返回队列的第一个元素

## 队列面试常见问题

*   使用队列实现堆栈
*   反转队列的前 k 个元素
*   使用队列生成从 1 到 n 的二进制数

# 链表

链表是另一种重要的线性数据结构，起初看起来可能与数组相似，但在内存分配、内部结构以及插入和删除的基本操作如何执行方面有所不同。

链表就像一个节点链，其中每个节点都包含数据和指向链中下一个节点的指针等信息。有一个 head 指针，它指向链表的第一个元素，如果链表是空的，那么它只是指向 null 或 nothing。

链表用于实现文件系统、哈希表和邻接表。下面是一个链表内部结构的可视化表示:

[![Linked List](../Images/2c7f57da5620bae0eb21dc8e9bb83790.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y7tQytQ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xxrsac9g2afohilzw14p.png)

*以下是链表的类型:*

*   单向链表(单向)
*   双向链表(双向)

## 链表的基本操作:

*   在链表的末尾插入给定的元素
*   在链表的开始/头部插入给定的元素
*   从链表中删除给定的元素
*   删除链表的第一个元素
*   Search -从链表中返回给定的元素
*   isEmpty -如果链表为空，则返回 true

## 链表面试常见问题

*   反转一个链表
*   检测链表中的循环
*   返回链表中从末尾开始的第 n 个节点
*   从链接列表中删除重复项

# 图表

图是以网络形式相互连接的一组节点。节点也称为顶点。一对(x，y)称为边，表示顶点 x 连接到顶点 y。边可能包含权重/成本，显示从顶点 x 遍历到 y 需要多少成本。

[![Graph](../Images/534ec710c8994df148f23c459b7786ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cAsADbGC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0swh6g6r5ugvptlzqeu3.png)

*图形类型:*

*   无向图
*   有向图

在编程语言中，图形可以用两种形式表示:

*   邻接矩阵
*   邻接表

*常见的图遍历算法:*

*   横向优先搜索
*   深度优先搜索

## 常见问图面试问题

*   实施广度和深度优先搜索
*   检查一个图是否是树
*   计算图中的边数
*   寻找两个顶点之间的最短路径

# 树木

树是一种分层数据结构，由顶点(节点)和连接它们的边组成。树和图很相似，但是区别树和图的关键点是树中不能存在循环。

树广泛用于人工智能和复杂算法中，为解决问题提供有效的存储机制。

下面是一个简单树的图像，以及树数据结构中使用的基本术语:

[![Tree](../Images/636cbcfef08234051ca89d85bd06da87.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y-AMvRwX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h2ohetg2bqhiyiedrr78.png)

*以下是树的类型:*

*   N-ary Tree
*   平衡树
*   二叉树
*   二叉查找树
*   AVL 树
*   红色黑色的树
*   2–3 棵树

在上面的树中，二叉树和二叉查找树是最常用的树。

## 常见问树面试问题

*   求二叉树的高度
*   求二叉查找树中的第 k 个最大值
*   查找距离根“k”距离的节点
*   在二叉树中查找给定节点的祖先

# 排序

Trie 也称为“前缀树”，是一种类似树的数据结构，被证明对于解决与字符串相关的问题非常有效。它提供快速检索，主要用于在字典中搜索单词，在搜索引擎中提供自动建议，甚至用于 IP 路由。

下面是三个单词“top”、“thus”和“their”如何存储在 Trie 中的示例:

[![Trie](../Images/92b951e5b53bfcbcdef0e16ef4c2858b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XHQo-v78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nj3ymtbis2f9zfz9xmzm.png)

单词以从上到下的方式存储，其中绿色的节点“p”、“s”和“r”分别表示“top”、“those”和“their”的结尾。

## Trie 面试常见问题:

*   统计 Trie 中的单词总数
*   打印存储在 Trie 中的所有单词
*   使用 Trie 排序数组元素
*   使用 Trie 从字典中形成单词
*   建立一个 T9 字典

# 哈希表

哈希是一个用于唯一标识对象并将每个对象存储在某个预先计算的唯一索引(称为其“键”)中的过程因此，对象以“键-值”对的形式存储，这种项目的集合称为“字典”每个对象都可以使用该键进行搜索。基于哈希有不同的数据结构，但是最常用的数据结构是*哈希表*。

哈希表通常使用数组来实现。

*哈希数据结构的性能取决于这三个因素:*

*   散列函数
*   哈希表的大小
*   冲突处理方法

下面的例子说明了散列是如何映射到数组中的。这个数组的索引是通过哈希函数计算的。

[![Hash Table](../Images/ab8be63198ae5213dce3befab757e3e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wHLNazUq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j62lo4fi45nl17oi20te.png)

## 常见的问散列面试问题

*   在数组中寻找对称对
*   追踪旅程的完整路径
*   查找一个数组是否是另一个数组的子集
*   检查给定的数组是否不相交

* * *

以上是你在参加编码面试之前应该了解的 8 大数据结构。

*如果你正在为编码面试寻找数据结构方面的资源，看看基于互动&挑战的课程:[掌握编码面试的数据结构](https://www.educative.io/m/data-structures-for-coding-interviews)特色课程包括 Python、Java、C++和 JavaScript。*

对于更高级的问题，请看[探索编码面试:编码问题的模式](https://www.educative.io/courses/grokking-the-coding-interview)。

祝好运，学习愉快！:)
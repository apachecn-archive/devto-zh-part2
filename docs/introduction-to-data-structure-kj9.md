# 数据结构导论

> 原文：<https://dev.to/rinsama77/introduction-to-data-structure-kj9>

既然我们处在一个处理大量信息的时代，我们需要一些有效的方法来处理它们。在计算机科学中，我们将学习如何操作数据和/或解决数据结构中的计算问题(如算法)!

#### 还不明白？

想想图书馆，要在一个脏乱的图书馆里找到一本书(至少)要花你一整天的时间。但是如果书的每个位置都被标记和记录，或者书被整齐地排列，那么找一本书不到一分钟！
[![hehe](img/e5f6619b545fe502a0e632587a306749.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--mePyZmL---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3ndywoev0ajs5sz09h3.png)

现在，这篇文章将介绍*数组*和*大 O 符号(渐近上限)。*先说**大 O** 。

* * *

#### **算法分析**

*   谈论*时间*和*空间*。
*   我们使用术语**大 O** - >渐近上界(大 O 符号)
    *   函数中的最大或最大项![Big O comparison](img/621b966006ef6ed1291376500eea794b.png)

> 属性:
> 
> *   传递性:若 f(n) = O(g(n))，g(n) = O(h(n))，则 f(n) = O(h)n))也。
> *   函数和:若 f(n)和 g(n) = O(h(n))，则 f(n) + g(n) = O(h(n))。
> *   多项式:设 f(n)为“d”次多项式，则 f(n) = O(n <sup>d</sup> )。

欲了解更多信息，[点击此处](https://medium.freecodecamp.org/my-first-foray-into-technology-c5b6e83fe8f1)！

#### 阵列

正如我所说的，存储数据的方法有很多，数组就是其中之一。但它是非常基本的数据结构。现在我们来看看它的属性。

> 属性:
> 
> *   连续存储的数据
> **   可通过索引访问的数据(例如 A[i])*   数组的大小是预先确定的(意味着它是固定的)*

 *有*两种*类型的数组；*有序与无序*。有什么不同？好吧，区别可以从做一些运算所用的时间和算法上看出来。

> 基本操作:
> 
> *   插入
> *   搜索
> *   删除

这是比较两者的表格:

|  | 有序数组 | 无序数组 |
| --- | --- | --- |
| **插入** | **Slow** (因为你必须先搜索，然后你可能必须移动数组中的元素/数据来放入数据) | **快速**(刚加到最新位置；O(1) |
| **搜索** | **快速**(如果用二分搜索法；O(logN)，但使用线性搜索仍然很慢；O(N/2) - >平均使用时间 | **慢**(只能用线性搜索；O(N/2)) |
| **删除** | **也慢** T_T(用二分搜索法搜索选中的数据；O( logN)，删除，然后移动所有数据<sup id="fnref1">T31</sup>；o(不适用) | **慢**(过程相同，除了线性搜索；O(N)) |

##### Java 中的语法

*创建数组::

```
int[] intArray;
intArray = new int[100]; 
```

或者

```
int[] intArray = new int[100]; 
```

::访问数组元素::

```
temp = intArray[4];
intArray[25] = 7; 
```

* * *

1.  删除后，数据之间会有一个空格，所以必须移动数据，这样就不会有间隔，因为*数组是连续的*。 [↩](#fnref1)*
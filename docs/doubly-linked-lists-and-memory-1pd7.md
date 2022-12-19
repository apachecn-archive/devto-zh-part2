# 双向链表和内存

> 原文：<https://dev.to/zanehelton/doubly-linked-lists-and-memory-1pd7>

## 一个什么？

为了一石二鸟，下面是单链表的样子:

[![](../Images/b22ae858498552e71e993be9ed5f461f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_dNHX3PE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yvlap0e8cd1sykzfg8w6.png)

从(可选的)“head”元素开始，很明显每个元素都由一些数据和对其后元素的引用组成。有了这些信息，我们就可以在列表中前进，但是对于单链表，如果没有额外的信息，就无法引用列表中前面的元素。

这就是双向链表的闪光点🌞

双向链表是一种由元素组成的数据结构，与单向链表相同，但在它前面有一个对元素的额外引用。

[![](../Images/1d47dc673a8da31f23eed01e7daee6a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mVIMOpvc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zdqfmrt45qg9uqgwlahy.png)

## 我们做一个吧

```
// linked.c
#include <stdlib.h>
#include <stdio.h> 
// what a node look like internally
struct Node {
  int idx;
  struct Node *r_next;
  struct Node *r_prev;
};

... 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码定义了我们的`Node`结构。
对于那些没有摄入每日所需维生素 C 的人来说，as*terisk 意味着`r_next`和`r_prev`拥有另一个`Node`结构体的内存地址。

```
...

int main() {
  // define 3 nodes
  struct Node n0, n1, n2;

  // initialize the nodes
  n0.idx = 0;         // the first node has an index of 0 
  n0.r_next = &n1;    // with a reference to the second node
  n0.r_prev = NULL;   // and a reference to NULL b/c there's no previous node

  n1.idx = 1;
  n1.r_next = &n2;
  n1.r_prev = &n0;

  n2.idx = 2;
  n2.r_next = NULL;
  n2.r_prev = &n1;

  // create a new reference to n0 called r_first_element
  struct Node *r_first_element = &n0;
  printf("The first node lives @ %p\n", r_first_element);
  printf("The first node has idx: %d\n", r_first_element->idx);

  // print n1 info
  printf("The second node lives @ %p\n", r_first_element->r_next);
  printf("The second node has idx: %d\n", r_first_element->r_next->idx);

  // print n2 info
  printf("The third node lives @ %p\n", r_first_element->r_next->r_next);
  printf("The third node has idx: %d\n", r_first_element->r_next->r_next->idx);

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

编译和执行时，输出类似于:

```
The first node lives @ 0x7ffee41de960
The first node has idx: 0
The second node lives @ 0x7ffee41de948
The second node has idx: 1
The third node lives @ 0x7ffee41de930
The third node has idx: 2 
```

Enter fullscreen mode Exit fullscreen mode

概括一下——我们从创建一个`Node`结构开始，这个结构有一个索引、一个对下一个节点的引用和一个对上一个节点的引用。然后，创建了一个对`n0`的新引用，我们称之为`r_first_element`。

双向链表现在已经设置好了，我们可以通过使用 C 中的`->`操作符来读取`Node`结构的成员，从而遍历和检索信息。

## 记忆

关于这个例子，我认为最后需要注意的是这个特殊的双向链表的内存布局。我在输出中包含了内存地址(如上图),这样这个视觉效果更有意义...

[![](../Images/2839b5ae9710dc9d45dff1110e8f4ef0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y4Jh7lZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nnmr00azr3yzsoz84isy.png)

如果你以前处理过低级内存，你会知道有一个从高级内存地址增长到低级内存地址的[堆栈](https://www.youtube.com/watch?v=CgFVgp_VCN8)。这就是为什么`n0`–即使先定义也是位于最高的内存地址。

在左边，我标记了每个节点到`n0`的距离。`n1`位于`n0`之前 24 个字节，`n2`位于`n0`之前 48 个字节。

这是因为我们的`Node`结构是 24 字节的。我们可以用下面的代码片段来证实这一点:

```
printf("sizeof(struct Node): %lu", sizeof(struct Node)); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
sizeof(struct Node): 24 
```

Enter fullscreen mode Exit fullscreen mode

## 太棒了！

[![](../Images/186308616c9679f28a8929fe97e128f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ULTWfQgq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/Br00TCn.gif)

老实说，我从未在工作中使用过这种数据结构，但我认为认识到这些数据结构在未来可能适用是很重要的。

双向链表通常可以与数组互换，但是如果数据是并排移动而不是首尾相连，那么双向链表会更有效。

一些真实的例子:

*   音乐队列
*   撤消和重做功能
*   区块链(反向单链表)

感谢您关注我的第一篇开发日志！

*延伸阅读*:[http://open data structures . org/ODS-Java/3 _ 2 _ dl list _ doubley _ Linked _ Li . html](http://opendatastructures.org/ods-java/3_2_DLList_Doubly_Linked_Li.html)
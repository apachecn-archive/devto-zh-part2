# 计算机科学 101 -链表介绍

> 原文：<https://dev.to/donaldkellett/computer-science-101---introduction-to-linked-lists-1c89>

如果你以前做过任何编程，你可能至少会偶然发现“链表”这个术语一次。如果你是一名计算机科学专业的学生，并且已经在大学学习了至少一个学期，那么你现在应该对它们非常熟悉了。但是对于那些没有计算机科学背景的人来说，这篇文章旨在向你介绍“链表”的概念以及如何使用它们。那么它们是什么，为什么它们很重要，我们如何与它们合作？

链表基本上是表示一系列项目的多种可能方式之一。例如，如果你有一个数字列表`1, 3, 2`，至少有两种(标准的)方法可以在你的程序中表示它——1)通过使用一个数组(你应该已经知道了)或者 2)通过使用一个链表。总之，链表的特殊之处在于它是一种*递归*数据类型，即它可以根据自身来定义，典型的定义如下:

1.  基本情况——让一个空链表表示一系列`0`元素。这通常被定义为`null`/`nil`/`NULL`/等等。在大多数编程语言中。
2.  递归情况——让任何一个非空链表都是一个`Node`(我们列表中的一个条目),它包含一个头元素`data`和一个尾元素，尾元素是链表的其余部分——我们称尾元素为`next`。

因此，空链表将被简单地表示为`NULL`，我们的整数列表`1, 3, 2`(按此顺序)可以表示为:

```
oneThreeTwo : Node // oneThreeTwo is a Node, i.e. a linked list
oneThreeTwo =
  data: 1
  next:
    data: 3
    next:
      data: 2
      next:
        NULL 
```

注意`oneThreeTwo`的“tail”(即`next`字段)本身就是一个链表:

```
threeTwo : Node
threeTwo = tail oneThreeTwo // or threeTwo = oneThreeTwo.next
// threeTwo =
//   data: 3
//   next:
//     data: 2
//     next:
//       NULL 
```

`threeTwo`的尾部也是如此(一个包含头部元素`2`的列表，其尾部是`NULL`)以及`tail threeTwo`的尾部(它只是`NULL`，根据定义，它本身就是一个链表)。 *N.B.* `NULL` *也被平凡地认为是* `Node` *虽然是特殊的一个。*

现在你应该想知道为什么我们不用数组来代替。毕竟，没有了那些递归和术语，数组的定义似乎简单多了。这是因为在某些情况下，链表比普通数组具有多种优势:

1.  将一个元素添加到一个现有的链表中既简单又有效——您只需定义一个新的包含给定元素的`Node`,该元素的尾部或`next`字段是原始的链表。相反，在大多数实现中，将一个元素添加到数组前需要将所有前面的元素向右移动一个位置，这可能很耗时。
2.  链表的长度在其定义中自动编码；相比之下，大多数编程语言中的数组实现需要一个单独的数字存储在项目序列旁边(尽管它们通常对程序员隐藏了这个实现细节)

说到这里，这里有一个如何定义`prepend`的伪代码示例(将一个元素添加到现有列表中):

```
prepend : (T, Node) -> Node // prepend takes an item of type T (if such
// a type needs to be specified) and an existing list, returning a new list
prepend (elem, list) =
  data: elem
  next:
    list 
```

...和`length`可以递归定义如下:

```
length : Node -> Size // length takes a linked list and returns a number
// representing its length/size in terms of number of elements
length list
  = 0                       if list == NULL
  = 1 + length list.next    otherwise 
```

上面代码示例的意思是，如果`list`是空列表(在大多数情况下用`NULL`表示)，那么它有`0`项。否则，它不为空，并且项目的数量正好比列表中其余项目的数量大`1`(`list.next`/`tail list`)。

可以在链表上定义更多可能的操作(例如使用自定义的映射函数递归地转换链表的每个元素，通过谓词过滤链表中的项目，以升序排序链表的元素，等等)。)但我们不会在本帖中讨论它们。我们将以链表的真实世界用例的非穷尽列表来结束下面的帖子:

*   许多高效的[栈](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))在内部被表示为一个链表
*   使用一个聪明的链表包装器也可以有效地实现队列——在以后的文章中会有更多(也许；)
*   双端队列也称为“deque ”(堆栈和队列的一般化),可以使用包装器有效地实现双链表(本文没有讨论的一种特殊类型的链表)
*   散列表/关联数组(例如， [java.util.HashMap](https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html) 、Python 字典、Ruby 散列)可以被实现为链表的(可增长的)数组(尽管术语“桶”通常用于表示“数组”)

令人震惊的是——**区块链**(是的，区块链为许多现代加密货币提供动力的*)基本上是美化了的链表(具有一种叫做“哈希”的安全特性)！所以如果你掌握了链表*，你*也许*就能赚很多钱；)*

我希望这篇文章给了你什么是链表以及它们为什么有用的简要概述——欢迎在:D 评论中分享你的想法

外部资源:

*   [链表-维基百科](https://en.wikipedia.org/wiki/Linked_list)
*   [Codewars 上的链表形系列](https://www.codewars.com/kata/linked-lists-push-and-buildonetwothree)

*当然，除了其他方面，p
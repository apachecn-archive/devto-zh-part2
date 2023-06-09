# JS 中的链表介绍

> 原文：<https://dev.to/ryanfarney3/intro-to-linked-lists-in-js-19b4>

### 概述

链表是数据的有序集合。该集合包含许多不同的节点。每个节点都包含一些数据以及对下一个节点的引用。当我们将这些节点放在一起时，我们称之为链表，因为它实际上是链接在一起的节点的列表。我们也经常称之为链。形成链的节点列表有一个顺序，不会突然或随机改变，当然，除非我们想改变它。在每个链表中有两个特殊的节点；头和尾。头节点总是列表的第一个节点。尾节点总是列表的最后一个节点。尾节点总是可以通过它没有对任何其他节点的引用这一事实来识别。

[![alt text](img/3e8f545a54e63172c5a3c5f895e4dab7.png "Linked List")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qh9cThPi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.geeksforgeeks.org/wp-content/uploads/gq/2013/03/Linkedlist.png)

节点中可以包含的数据绝对可以是我们想要的任何数据类型；字符串、数字、数组、对象，任何类型的 JS 值都可以包含在这些节点中。节点的另一部分是对下一个节点的引用。

使用链表有好处也有坏处。看看这个 Quora [论坛](https://www.quora.com/What-are-the-pros-and-cons-of-using-a-linked-list)吧！

我认为学习链表(以及大多数数据结构/算法题)的最好方法是自己实际练习。打开一个 repl，让我们从创建最基本的链表开始。

```
const nodeOne = {
  data: "Hi"
}

const nodeTwo = {
  data: "Sofia"
}

nodeOne.next = nodeTwo

console.log(nodeOne) // => { data: 'Hi', next: { data: 'Sofia' } } 
```

Enter fullscreen mode Exit fullscreen mode

本质上，我们刚刚创建了我们自己的链表...我真的鼓励你自己去做，看看它是如何工作的，因为我们将在这里做得更深入一些。

我们之前说过，链表是由节点组成的。这听起来像是我们可以爆发的事情。因此，让我们创建 Node 和 LinkedList 函数。但是，在我写出来之前...想想这些函数可能包含什么。我们知道一个节点有它的数据和对下一个节点的引用。首先，我们知道链表有一个头。嘣！让我们从这里开始。

```
function Node(data, next = null) {
  this.data = data,
  this.next = next
}

function LinkedList() {
  this.head = null
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们稍微试验一下我们的链表，并对它执行一些操作。这里我将使用原型委托。如果你不确定那是什么，我强烈建议你在其他时间深入了解类与原型继承的优缺点和差异，但是不要担心...你仍然可以跟上。

另外，我可以补充一点，有很多方法可以做到这一点，如果你用另一种方法，我很想知道为什么。

我们希望能够做的第一件事是在列表的前面添加一个节点。在这一点上，我假设你是跟随在一个 repl。

让我们创建一个函数 addToFront，它将链表的头部设置为我们的新节点！

```
LinkedList.prototype.addToFront = function(data) {
  this.head = new Node(data, this.head)
}

let list = new LinkedList()
let node = new Node(5)
list.head = node
list.addToFront(10)
console.log(list) // => LinkedList { head: Node { data: 10, next: Node { data: 5, next: null } } }

// You should continuously be testing in your repl like above ^^ 
```

Enter fullscreen mode Exit fullscreen mode

* * *

现在，也许我们想检查我们的链表的大小。我们可以创建一个名为 size 的函数来计算列表中的每个节点！

```
LinkedList.prototype.size = function() {
  let counter = 0
  let node  = this.head

  while (node) {
    counter++;
    node = node.next
  }
  return counter
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们在这里使用了 while 循环。这是一个非常好的技术，对于许多其他问题来说会很方便。我们设置计数器，然后将节点变量设置为第一个节点。当列表中有一个节点时(或者直到 node === null ),我们增加计数器，同时将节点变量重置为列表中的下一个节点。最后我们返回计数器。

* * *

也许我们希望有不同的函数来检索第一个和最后一个节点。因此，我们创建 retrieveFirst 和 retrieveLast 函数。为了节省空间，检索第一个节点将只是返回 *this.head* ，所以我们不会把它写出来，但是你应该写出来。然而，对于 retrieveLast，我们必须做一些类似于 size 函数的事情。

```
LinkedList.prototype.retrieveLast = function() {
  let node = this.head
  if (!node) {
    return null
  }

  while(node) {
    if (node.next === null) {
      return node
    }
      node = node.next
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们所做的就是返回列表中的最后一个节点...尾巴。但是，如果没有第一个节点，我们返回 null。如果有，我们就进入 while 循环，只是这一次我们要确保检查下一个节点是否在那里。如果没有对下一个节点的引用，我们知道我们已经到达了尾部，我们返回它。

* * *

也许我们想一起删除整个链表，或者至少清除它。让我们创建一个名为 erase 的方法。这实际上比看起来容易得多。我们知道链表是以头开始的，它引用下一个节点，依此类推。如果我们把怪物的头砍下来呢？！如果链表没有初始参考点，那么它就没有了。试试看。

```
LinkedList.prototype.erase = function() {
  return this.head = null
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，如果我们只想删除第一个节点/头会怎么样？

首先，我们要检查是否有一个要删除。那么我们可以让第一个节点等于下一个节点！

```
LinkedList.prototype.removeFirst = function() {
  if (!this.head) {
    return;
  }
  return this.head = this.head.next
} 
```

Enter fullscreen mode Exit fullscreen mode

我们开始了！稍微难一点的几个怎么样？

让我们删除最后一个节点，并尝试创建一个新的尾节点。要删除最后一个节点，我们首先需要处理一些边缘情况。1)我们希望确保有一个头节点，2)我们希望确保如果只有一个头节点，我们只需*返回 null* 。之后有几种不同的方法，但我会带你走一遍对我来说最有意义的方法。

```
LinkedList.prototype.deleteLast = function() {
  if (!this.head) {
    return;
  }

  if (!this.head.next) {
    return this.head = null
  }

  let previous = this.head
  while(previous) {
    let node = previous.next
    if (!node.next) {
      return previous.next = null
    }
    previous = previous.next
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

检查之后，我们设置两个变量；从头部开始的前一个节点和将始终在前一个节点前面的节点。当有一个节点时，我们希望继续循环，一旦对下一个节点的引用为空，我们知道我们已经到达了最后一个节点，我们希望删除那个节点。

* * *

最后，如果我们要删除最后一个节点，我们也可以添加到最后一个节点。我给你看最后一个窍门。上面我们创建了一个名为 retrieveLast()的原型委托方法。让我们轻松一下，用它来找到最后一个要添加的节点。

此外，我们需要在这里创建一个新节点，因为我们正在添加一个节点，所以我们的函数将接收数据。然后，我们将把 retrieveLast()函数设置为一个变量。最后，我们要确保链表不是空的。如果是，我们将设置新节点为头节点，如果不是，我们将它设置为 last.next.

```
LinkedList.prototype.insertLast = function(data) {
  const newNode = new Node(data)
  const last = this.retrieveLast()

  if (last) {
    last.next = newNode
  } else {
    this.head = newNode
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

感谢您的关注！我希望这对您有所帮助，并且您已经初步了解了链表:)！

### 参考文献

查看这个伟大的[课程](https://www.udemy.com/coding-interview-bootcamp-algorithms-and-data-structure/)!
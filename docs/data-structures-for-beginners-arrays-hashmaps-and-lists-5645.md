# JavaScript 中的数据结构:数组、哈希表和列表

> 原文：<https://dev.to/amejiarosario/data-structures-for-beginners-arrays-hashmaps-and-lists-5645>

当我们开发软件时，我们必须将数据存储在内存中。然而，有许多类型的数据结构，如数组、映射、集合、列表、树、图形等。为这项任务选择正确的方法可能会很棘手。所以，这一系列的文章将帮助你知道取舍，这样，你就可以使用正确的工具了！

在这一节中，我们将关注线性数据结构:数组、列表、集合、堆栈和队列。

您可以在 Github repo:
中找到所有这些实现以及更多内容

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [阿梅加罗萨里奥](https://github.com/amejiarosario)/[DSA . js-数据-结构-算法-javascript](https://github.com/amejiarosario/dsa.js-data-structures-algorithms-javascript)

### 🥞JavaScript + eBook 中解释和实现的数据结构和算法

<article class="markdown-body entry-content container-lg" itemprop="text">

[![image](img/56b5d4dbac1233ee93c5155b15079c68.png)T2】](https://user-images.githubusercontent.com/418605/59557258-10742880-8fa3-11e9-84fb-4d66a9d89faa.png)

# JavaScript 中的数据结构和算法

[![CircleCI](img/d5922fc0cbf322196a6f5e893fcfcf37.png)](https://app.circleci.com/pipelines/github/amejiarosario/dsa.js-data-structures-algorithms-javascript)[![NPM version](img/2c26fb04f41469582f423d88af92342e.png)](https://badge.fury.io/js/dsa.js)[![chat](img/e028ff0be0803e8e9dbdd25d5a8c6ca4.png)](https://dsajs-slackin.herokuapp.com)

> 这是 DSA.js book 的编码实现和 NPM 包的回购。

> 在这个库中，您可以找到 JavaScript 中算法和数据结构的实现。这些材料可以作为开发人员的参考手册，或者您可以在面试前刷新特定主题。还有，你可以找到更高效解决问题的思路。

[![Interactive Data Structures](img/8f63557fae7adb265acdf574fa489c65.png)T2】](https://user-images.githubusercontent.com/418605/46118890-ba721180-c1d6-11e8-82bc-6a671428b422.png)

## 目录

*   [安装](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#installation)
*   [特性](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#features)
*   [里面有什么](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#whats-inside)
    *   [<g-emoji class="g-emoji" alias="chart_with_upwards_trend" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4c8.png">📈</g-emoji>算法分析](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-algorithms-analysis)
    *   [<g-emoji class="g-emoji" alias="pancakes" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f95e.png">🥞</g-emoji>线性数据结构](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-linear-data-structures)
    *   [<g-emoji class="g-emoji" alias="evergreen_tree" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f332.png">🌲</g-emoji>非线性数据结构](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-non-linear-data-structures)
    *   [<g-emoji class="g-emoji" alias="hammer_and_pick" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2692.png">⚒</g-emoji> 算法技巧](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#%E2%9A%92-algorithms-techniques)
*   [预定](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#book)
*   [常见问题解答](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#faq)
*   [支持](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#support)
*   [执照](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#license)

## 装置

您可以克隆回购协议或安装来自 NPM 的代码:

```
npm install dsa.js
```

Enter fullscreen mode Exit fullscreen mode

然后您可以将它导入到您的程序或 CLI 中

```
const { LinkedList, Queue, Stack } = require('dsa.js');
```

Enter fullscreen mode Exit fullscreen mode

所有公开的数据结构和算法的完整列表[见](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/src/index.js)。

## 特征

算法是一种…

</article>

[View on GitHub](https://github.com/amejiarosario/dsa.js-data-structures-algorithms-javascript)

# 数据结构大样图

下表总结了我们将在此讨论的所有内容。

> 把它加入书签，别在上面或者分享，这样当你需要它的时候，它就在你的手边。

*点击* *名称* *进入该部分或点击**运行时**进入实施*

`*` =摊销运行时间

| 名字 | 插入 | 接近 | 搜索 | 删除 | 评论 |
| --- | --- | --- | --- | --- | --- |
| [数组](#array) | [O(n)](#insert-element-on-an-array) | [O(1)](#access-an-element-in-an-array) | [O(n)](#search-an-element-in-an-array) | [O(n)](#deleting-elements-from-an-array) | 插入到底是`O(1)`。[详情在此。](#array-operations-time-complexity) |
| [HashMap](#hashmaps) | [O(1)](#insert-element-on-a-hashmap-runtime) | [O(1)](#search-access-an-element-on-a-hashmap-runtime) | [O(1)](#search-access-an-element-on-a-hashmap-runtime) | [O(1)](#edit-delete-element-on-a-hashmap-runtime) | 重新散列可能会影响插入时间。[详情在此。](#hashmap-operations-time-complexity) |
| 地图(使用二叉查找树) | O(log(n)) | - | O(log(n)) | O(log(n)) | 使用二叉查找树实现 |
| [Set(使用 HashMap)](#sets) | [O(1)](#set-implementation) | - | [O(1)](#set-implementation) | [O(1)](#set-implementation) | 使用 HashMap 实现设置。[详情在此。](#set-Operations-runtime) |
| 设置(使用列表) | [O(n)](https://www.ecma-international.org/ecma-262/6.0/#sec-set.prototype.add) | - | [O(n)](https://www.ecma-international.org/ecma-262/6.0/#sec-set.prototype.has) | [O(n)](https://www.ecma-international.org/ecma-262/6.0/#sec-set.prototype.delete) | 使用二叉查找树实现 |
| 设置(使用二叉查找树) | O(log(n)) | - | O(log(n)) | O(log(n)) | 使用二叉查找树实现 |
| [链表(单个)](#singly-linked-lists) | [O(n)](//#SinglyLinkedList.addLast) | - | [O(n)](//#LinkedList.contains) | [O(n)](//#LinkedList.remove) | 添加/删除列表的开始是`O(1)`。[详情在此](#Singly-Linked-Lists-time-complexity)。 |
| [链表(双倍)](#Doubly-Linked-Lists) | [O(n)](//#DoublyLinkedList.add) | - | [O(n)](//#LinkedList.contains) | [O(n)](//#LinkedList.remove) | 从开始/结束添加/删除是`O(1)`。但是，中间删除/添加的是`O(n)`。[详情在此](#doubly-linked-lists-time-complexity) |
| [堆栈(数组实现)](#Stacks) | [O(1)](#Stacks) | - | - | [O(1)](#Stacks) | 插入/删除是后进先出(LIFO) |
| [队列(朴素数组 impl。)](#QueueNaiveImpl) | [O(n)](#QueueNaiveImpl) | - | - | [O(1)](#QueueNaiveImpl) | 插入(`Array.shift`)是 O(n) |
| [队列(数组实现)](#QueueArrayImpl) | [O(1)](#QueueArrayImpl) | - | - | [O(1)](#QueueArrayImpl) | 最差时间插入是 O(n)。然而摊销是 O(1) |
| [排队(列表实现)](#QueueListImpl) | [O(1)](#QueueListImpl) | - | - | [O(1)](#QueueListImpl) | 引用最后一个元素使用双向链表。 |

注意:**二分搜索法树**和一般意义上的树，将在下一篇文章中讨论。同样，图形数据结构。

# 原始数据类型

原始数据类型是最基本的元素，所有其他数据结构都建立在这些元素之上。一些原语是:

*   整数。例如`1`、`2`、`3`、...
*   人物。例如`a`、`b`、`"1"`、`"*"`
*   布尔人。例如`true`或`false`。
*   Float(浮点)或 doubles。如`3.14159`、`1483e-2`。
*   空值。例如`null`

特定于 JavaScript 的原语:

*   不明确的
*   标志
*   数字

注意:对象不是原语，因为它是零个或多个原语和其他对象的集合。

## 阵列

数组是零个或多个元素的集合。数组是最常用的数据结构之一，因为它的简单性和快速检索信息的方式。

你可以把一个数组想象成一个抽屉，你可以在里面的箱子上存放东西。

**Array 就像一个抽屉，把东西存放在箱子上**

[![](img/f45a47e4cf651fc1f26b8c52ec582157.png "Array is like a drawer that stores things on bins")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uMaBX6pb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/array-drawer.jpg)

当你想找东西的时候，你可以直接去找箱子号码。那是一个恒定时间操作( *`O(1)`* )。但是，如果您忘记了柜子里有什么，那么您将不得不逐个打开( *`O(n)`* )来验证它的内容，直到您找到您要找的东西。数组也是如此。

根据编程语言的不同，数组也有一些不同。对于一些动态语言，如 JavaScript 和 Ruby，数组可以包含不同的数据类型:数字、字符串、单词、对象，甚至函数。在 Java/C/C++这样的类型化语言中，您必须预先定义数组的大小和数据类型。在 JavaScript 中，它会在需要时自动增加数组的大小。

### 数组内置运算

根据编程语言的不同，实现会略有不同。

例如，在 JavaScript 中，我们可以用`push`完成添加到结尾，用`unshift`完成添加到开头。但是，我们也要从数组中移除`pop`和`shift`。让我们通过这篇文章来描述一些我们将要使用的常见操作的运行时。

**常见的 JS 数组内置函数**

| 功能 | 运行时间 | 描述 |
| --- | --- | --- |
| [array.push](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push) | O(1) | 将元素插入数组的末尾 |
| [array.pop](http://devdocs.io/javascript/global_objects/array/pop) | O(1) | 移除数组末尾的元素 |
| [array.shift](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift) | O(n) | 移除数组开头的元素 |
| [array.unshift](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift) | O(n) | 将元素插入数组的开头 |
| [array.slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice) | O(n) | 从`beginning`到`end`返回数组的副本。 |
| [array.splice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) | O(n) | 更改(添加/删除)阵列 |

### 在数组上插入元素

有多种方法可以将元素插入数组。您可以将新数据追加到集合的末尾，也可以将其添加到集合的开头。

让我们从追加到尾部开始:

```
function insertToTail(array, element) {
  array.push(element);
  return array;
}

const array = [1, 2, 3];
console.log(insertToTail(array, 4)); // => [ 1, 2, 3, 4 ] 
```

Enter fullscreen mode Exit fullscreen mode

基于[语言规范](https://tc39.github.io/ecma262/#sec-array.prototype.push)，push 只需在数组末尾设置新值。因此，

> `Array.push`运行时是一个 *O(1)*

现在让我们尝试添加到 head:

```
function insertToHead(array, element) {
  array.unshift(element);
  return array;
}

const array = [1, 2, 3];
console.log(insertToHead(array, 0)); // => [ 0, 1, 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

你认为`insertToHead`函数的运行时是怎样的？看起来和之前的一样，除了我们用了`unshift`而不是`push`。但是，有一个条件！ [unshift 算法](https://tc39.github.io/ecma262/#sec-array.prototype.unshift)通过将所有现有元素移动到数组中的下一个位置，为新元素腾出空间。因此，它将遍历所有项目并移动它们。

> `Array.unshift`运行时是一个 *O(n)*

### 访问数组中的一个元素

如果您知道要查找的元素的索引，那么您可以像这样直接访问该元素:

```
function access(array, index) {
  return array[index];
}

const array = [1, 'word', 3.14, {a: 1}];
access(array, 0); // => 1
access(array, 3); // => {a: 1} 
```

Enter fullscreen mode Exit fullscreen mode

正如您在上面的代码中看到的，访问数组中的元素有一个固定的时间:

> 数组访问运行时是 *O(1)*

*注意:你也可以在常数时间内改变给定索引的任何值。*

### 搜索数组中的一个元素

如果您不知道想要从数组中得到的数据的索引，那么您必须遍历集合中的每个元素，直到我们找到我们想要的。

```
function search(array, element) {
  for (let index = 0; index < array.length; index++) {
    if(element === array[index]) {
      return index;
    }
  }
}

const array = [1, 'word', 3.14, {a: 1}];
console.log(search(array, 'word')); // => 1
console.log(search(array, 3.14)); // => 2 
```

Enter fullscreen mode Exit fullscreen mode

给定 for 循环，我们有:

> 数组搜索运行时是 *O(n)*

### 从数组中删除元素

你认为从数组中删除一个元素的运行时间是多少？

好吧，让我们想想不同的情况:

1.  你可以从数组的末尾删除，这可能是常数时间。 *O(1)*
2.  但是，您也可以从集合的开头或中间移除。在这种情况下，您必须移动以下所有元素来缩小差距。 *O(n)*

空谈不值钱，还是做代码吧！

```
function remove(array, element) {
  const index = search(array, element);
  array.splice(index, 1);
  return array;
}

const array1 = [0, 1, 2, 3];
console.log(remove(array1, 1)); // => [ 0, 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

所以我们使用我们的`search`函数来寻找元素的索引 *O(n)* 。然后我们使用 [JS 内置的`splice`](https://tc39.github.io/ecma262/#sec-array.prototype.splice) 函数，其运行时间为 *O(n)* 。因此，我们将遍历列表两次，但不是说 *O(2n)* ，对于大 O 符号，它仍然是 *O(n)* 。请记住，从我们的第一篇文章[来看，常数并不重要。](https://dev.to/amejiarosario/how-you-can-change-the-world-by-learning-algorithms-549c#asymptotic-analysis)

我们假设最坏的情况:

> 从数组中删除一个项是 *O(n)* 。

### 阵列运算时间复杂度

我们可以将阵列的时间复杂度总结如下:

**数组时间复杂度**

| 操作 | 最差的 |
| --- | --- |
| 访问(`Array.[]`) | *T2`O(1)`* |
| 插入头部(`Array.unshift`) | *T2`O(n)`* |
| 插入尾部(`Array.push`) | *T2`O(1)`* |
| 搜索(值) | *T2`O(n)`* |
| 删除(`Array.splice`) | *T2`O(n)`* |

# 散列表

HashMap 有很多名字，比如 HashTable、HashMap、Map、Dictionary、Associative Arrays 等等。概念是相同的，但实现可能略有不同。

> 哈希表是一种数据结构，它将键映射到值

回到抽屉类比，垃圾箱有一个标签，而不是一个号码。

HashMap 就像一个抽屉，把东西放在箱子上，并贴上标签

[![](img/97f4d18e5a4207264336196b9437db80.png "HashMap is like a drawer that stores things on bins and labels them")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZPcJMpFo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/hashmap-drawer.jpg)

在这个例子中，如果你正在寻找 [DSA.js 书](https://gum.co/dsajs)，你不必打开箱子 1、2 和 3 来查看里面有什么。你直接进入标有“书籍”的容器。这是一个巨大的收获！搜索时间从 *O(n)* 到 *O(1)* 。

在数组中，使用数字索引(相对于位置)引用数据。然而，HashMaps 使用的标签可以是字符串、数字、对象或任何东西。在内部，HashMap 使用一个数组，并使用一个*散列函数*将标签映射到数组索引。

至少有两种方法可以实现映射:

1.  **数组**:使用散列函数将一个键映射到数组索引值。又名`HashMap`。最差:`O(n)`，一般:`O(1)`
2.  **二叉查找树**:用一个自我平衡的二叉查找树来寻找价值(稍后会详细介绍)。又名`TreeMap`。最差: *`O(log n)`* ，一般: *`O(log n)`* 。

我们将覆盖树木&二分搜索法树，所以现在不要担心它。地图最常见的实现是使用一个**数组**和`hash`函数。所以，这是我们要关注的一个问题。

**用数组实现 HashMap】**

[![](img/a6434b057856744f0b3e959757158c29.png "HashMap: hash function translates keys into bucket (array) indexes")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RMiN08Co--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/hash-map.jpg)

正如您在图中看到的，每个密钥都被翻译成一个**散列码**。由于数组大小有限(例如 10)，我们必须使用模数函数遍历可用的存储桶。在桶中，我们存储键/值对，如果不止一个，我们使用一个集合来保存它们。

现在，你认为详细介绍每个 HashMap 组件怎么样？让我们从**哈希函数**开始。

### 散列表与数组

你可能会问，为什么要把键转换成索引，而不是直接使用数组呢？主要的区别是数组的索引和数据没有任何关系。你必须知道你的数据在哪里。

假设你想计算一篇文章中单词的使用次数。你将如何实现它？

1.  可以用两个数组(姑且称之为`A`和`B`)。一个用于存储单词，另一个用于存储他们看过多少次(频率)。
2.  你可以使用散列表。他们 *`key`* 是词，而 *`value`* 是词的频率。

使用**两个数组**的方法#1 的运行时间是多少？如果说，文中的字数是 *`n`* 。然后我们必须对数组`A`中的单词进行`search`，然后递增数组`B`中与该索引匹配的值。对于`n`上的每个单词，我们必须测试它是否已经在数组`A`上。这个双循环使用运行时间为`O(n<sup>2</sup>)`。

使用**散列表**的方法#2 的运行时是怎样的？我们遍历文本中的每个单词一次，如果有什么，就增加这个值，如果这个单词是第一次出现，就把它设为 1。运行时将是`O(n)`，这比方法#1 的性能好得多。

HashMap 和 Array 的区别

*   对数组的搜索是 O(n) ，而对散列表的搜索是 O(1)
*   数组可以有重复的值，而 HashMap 不能有重复的键(但是它们可以有重复的值。)
*   数组有一个键(index ),它总是一个从 0 到最大值的数字，而在 HashMap 中，你可以控制这个键，它可以是你想要的任何值:数字、字符串或符号。

## 哈希函数

实现 HashMap 的第一步是拥有一个 hash 函数。这个函数将每个键映射到它的值。

> **完美散列函数**是为每个键分配一个唯一索引的函数。

理想的散列算法允许*常数时间*访问/查找。然而，在实践中很难实现完美的哈希函数。您可能会遇到两个不同的键产生同一索引的情况。这叫做*碰撞*。

当使用类似数组的底层数据结构时，HashMaps 中的冲突是不可避免的。在某种程度上，数据不能放入 HashMap，就会重用数据槽。处理冲突的一种方法是使用一个链表或另一个数组将多个值存储在同一个桶中(稍后将详细介绍)。当我们试图访问键值并找到不同的值时，我们迭代值 *O(n)* 。然而，在大多数实现中，哈希会动态调整大小，以避免过多的冲突。所以，我们可以说**摊销**查找时间为 *O(1)* 。在这篇文章的后面，我们将通过一个例子来解释摊销运行时间的含义。

### 天真的 HashMap 实现

一个简单的(也是糟糕的)散列函数应该是这样的:

```
class NaiveHashMap {

  constructor(initialCapacity = 2) {
    this.buckets = new Array(initialCapacity);
  }

  set(key, value) {
    const index = this.getIndex(key);
    this.buckets[index] = value;
  }

  get(key) {
    const index = this.getIndex(key);
    return this.buckets[index];
  }

  hash(key) {
    return key.toString().length;
  }

  getIndex(key) {
    const indexHash = this.hash(key);
    const index = indexHash % this.buckets.length;
    return index;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`buckets`而不是抽屉/垃圾箱，但你已经明白了:)

我们的初始容量为 2(桶)。但是，我们想在上面存储任意数量的元素。我们使用模数`%`来遍历可用桶的数量。

看看我们的散列函数。我们一会儿会谈到它。首先，让我们使用新的散列表！

```
// Usage:
const assert = require('assert');
const hashMap = new NaiveHashMap();

hashMap.set('cat', 2);
hashMap.set('rat', 7);
hashMap.set('dog', 1);
hashMap.set('art', 8);

console.log(hashMap.buckets);
/*
  bucket #0: <1 empty item>,
  bucket #1: 8
*/

assert.equal(hashMap.get('art'), 8); // this one is ok
assert.equal(hashMap.get('cat'), 8); // got overwritten by art 😱
assert.equal(hashMap.get('rat'), 8); // got overwritten by art 😱
assert.equal(hashMap.get('dog'), 8); // got overwritten by art 😱 
```

Enter fullscreen mode Exit fullscreen mode

这个`Map`允许我们`set`一个键和一个值，然后使用`key`来`get`该值。关键部分是`hash`功能。让我们看看多个实现，看看它如何影响地图的性能。

在看下面的答案之前，你能说出`NaiveHashMap`有什么问题吗？

`NaiveHashMap`的问题在于...

**1)** **哈希函数**生成许多重复项。例如

```
hash('cat') // 3
hash('dog') // 3 
```

Enter fullscreen mode Exit fullscreen mode

这样会造成很多碰撞。

**2)** **碰撞**根本不处理。`cat`和`dog`都将在数组的位置 3(桶#1)互相覆盖。

**3)** **数组的大小**即使我们得到了更好的哈希函数，我们也会得到重复的，因为数组的大小为 3，小于我们想要容纳的元素数量。我们希望初始容量远远超出我们需要的容量。

### 改进哈希函数

> HashMap 的主要目的是将数组的搜索/访问时间从 *`O(n)`* 减少到 *`O(1)`* 。

为此，我们需要:

1.  产生尽可能少冲突的适当散列函数。
2.  一个足够大的数组来保存所有需要的值。

让我们再试试我们的散列函数。不使用字符串的长度，让我们对每个字符 [ascii 码](https://simple.wikipedia.org/wiki/ASCII)求和。

```
 hash(key) {
    let hashValue = 0;
    const stringKey = key.toString();

    for (let index = 0; index < stringKey.length; index++) {
      const charCode = stringKey.charCodeAt(index);
      hashValue += charCode;
    }

    return hashValue;
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们再试一次:

```
hash('cat') // 312  (c=99 + a=97 + t=116)
hash('dog') // 314 (d=100 + o=111 + g=103) 
```

Enter fullscreen mode Exit fullscreen mode

这个更好！因为同样长度的单词有不同的代码。

然而，仍然有一个问题！因为`rat`和`art`都是 327，**碰撞！**💥

我们可以通过用位置:
抵消总和来解决这个问题

```
 hash(key) {
    let hashValue = 0;
    const stringKey = `${key}`;

    for (let index = 0; index < stringKey.length; index++) {
      const charCode = stringKey.charCodeAt(index);
      hashValue += charCode << (index * 8);
    }

    return hashValue;
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们再试一次，这次用十六进制数，这样我们就可以看到偏移。

```
// r = 114 or 0x72; a = 97 or 0x61; t = 116 or 0x74
hash('rat'); // 7,627,122 (r: 114 * 1 + a: 97 * 256 + t: 116 * 65,536) or in hex: 0x726174 (r: 0x72 + a: 0x6100 + t: 0x740000)
hash('art'); // 7,631,457 or 0x617274 
```

Enter fullscreen mode Exit fullscreen mode

不同类型呢？

```
hash(1); // 49
hash('1'); // 49

hash('1,2,3'); // 741485668
hash([1,2,3]); // 741485668

hash('undefined') // 3402815551
hash(undefined) // 3402815551 
```

Enter fullscreen mode Exit fullscreen mode

休斯顿，我们还有一个问题！！不同的值类型不应该返回相同的哈希代码！

我们如何解决这个问题？

一种方法是将关键字`type`考虑到散列函数中。

```
 hash(key) {
    let hashValue = 0;
    const stringTypeKey = `${key}${typeof key}`;

    for (let index = 0; index < stringTypeKey.length; index++) {
      const charCode = stringTypeKey.charCodeAt(index);
      hashValue += charCode << (index * 8);
    }

    return hashValue;
  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们再测试一次:

```
console.log(hash(1)); // 1843909523
console.log(hash('1')); // 1927012762

console.log(hash('1,2,3')); // 2668498381
console.log(hash([1,2,3])); // 2533949129

console.log(hash('undefined')); // 5329828264
console.log(hash(undefined)); // 6940203017 
```

Enter fullscreen mode Exit fullscreen mode

耶！！！🎉我们有一个更好的散列函数！

我们还可以更改阵列的初始容量，以最大限度地减少冲突。让我们在下一节将所有这些放在一起。

## 体面散列表实现

使用我们优化的散列函数，我们现在可以做得更好。

我们仍然可能有冲突，所以让我们实现一些东西来处理它们

让我们对 HashMap 实现进行以下改进:

*   **哈希函数**，检查类型和字符顺序以最小化冲突。
*   **通过向列表追加值来处理冲突**。我们还添加了一个计数器来跟踪它们。

```
class DecentHashMap {

  constructor(initialCapacity = 2) {
    this.buckets = new Array(initialCapacity);
    this.collisions = 0;
  }

  set(key, value) {
    const bucketIndex = this.getIndex(key);
    if(this.buckets[bucketIndex]) {
      this.buckets[bucketIndex].push({key, value});
      if(this.buckets[bucketIndex].length > 1) { this.collisions++; }
    } else {
      this.buckets[bucketIndex] = [{key, value}];
    }
    return this;
  }

  get(key) {
    const bucketIndex = this.getIndex(key);
    for (let arrayIndex = 0; arrayIndex < this.buckets[bucketIndex].length; arrayIndex++) {
      const entry = this.buckets[bucketIndex][arrayIndex];
      if(entry.key === key) {
        return entry.value
      }
    }
  }

  hash(key) {
    let hashValue = 0;
    const stringTypeKey = `${key}${typeof key}`;

    for (let index = 0; index < stringTypeKey.length; index++) {
      const charCode = stringTypeKey.charCodeAt(index);
      hashValue += charCode << (index * 8);
    }

    return hashValue;
  }

  getIndex(key) {
    const indexHash = this.hash(key);
    const index = indexHash % this.buckets.length;
    return index;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用它，看看它表现如何:

```
// Usage:
const assert = require('assert');
const hashMap = new DecentHashMap();

hashMap.set('cat', 2);
hashMap.set('rat', 7);
hashMap.set('dog', 1);
hashMap.set('art', 8);

console.log('collisions: ', hashMap.collisions); // 2
console.log(hashMap.buckets);
/*
  bucket #0: [ { key: 'cat', value: 2 }, { key: 'art', value: 8 } ]
  bucket #1: [ { key: 'rat', value: 7 }, { key: 'dog', value: 1 } ]
*/

assert.equal(hashMap.get('art'), 8); // this one is ok
assert.equal(hashMap.get('cat'), 2); // Good. Didn't got overwritten by art
assert.equal(hashMap.get('rat'), 7); // Good. Didn't got overwritten by art
assert.equal(hashMap.get('dog'), 1); // Good. Didn't got overwritten by art 
```

Enter fullscreen mode Exit fullscreen mode

这个`DecentHashMap`完成了工作，但是，仍然有一些问题。我们正在使用一个体面的哈希函数，不会产生重复的值，这很好。然而，我们在`bucket#0`中有两个值，在`bucket#1`中还有两个值。这怎么可能呢？

因为我们使用的是有限的桶大小 2，所以我们使用模数`%`来遍历可用桶的数量。因此，即使散列码不同，所有值都适合数组的大小:bucket#0 或 bucket#1。

```
hash('cat') => 3789411390; bucketIndex => 3789411390 % 2 = 0
hash('art') => 3789415740; bucketIndex => 3789415740 % 2 = 0
hash('dog') => 3788563007; bucketIndex => 3788563007 % 2 = 1
hash('rat') => 3789411405; bucketIndex => 3789411405 % 2 = 1 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们自然增加了初始容量，但增加了多少呢？让我们看看初始大小是如何影响哈希映射性能的。

如果我们的初始容量为`1`。所有的值都会进入一个桶(`bucket#0`)，不会比在一个简单的数组 *`O(n)`* 中搜索一个值好多少。

假设我们从初始容量设置为 10 开始:

```
const hashMapSize10 = new DecentHashMap(10);

hashMapSize10.set('cat', 2);
hashMapSize10.set('rat', 7);
hashMapSize10.set('dog', 1);
hashMapSize10.set('art', 8);

console.log('collisions: ', hashMapSize10.collisions); // 1
console.log('hashMapSize10\n', hashMapSize10.buckets);
/*
  bucket#0: [ { key: 'cat', value: 2 }, { key: 'art', value: 8 } ],
            <4 empty items>,
  bucket#5: [ { key: 'rat', value: 7 } ],
            <1 empty item>,
  bucket#7: [ { key: 'dog', value: 1 } ],
            <2 empty items>
*/ 
```

Enter fullscreen mode Exit fullscreen mode

换个角度来看

[![](img/a6434b057856744f0b3e959757158c29.png "HashMap: hash function translates keys into bucket (array) indexes")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RMiN08Co--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/hash-map.jpg)

如您所见，我们通过增加哈希表的初始容量减少了冲突的数量(从 2 个减少到 1 个)。

让我们试试更大的容量💯:

```
const hashMapSize100 = new DecentHashMap(100);

hashMapSize100.set('cat', 2);
hashMapSize100.set('rat', 7);
hashMapSize100.set('dog', 1);
hashMapSize100.set('art', 8);

console.log('collisions: ', hashMapSize100.collisions); // 0
console.log('hashMapSize100\n', hashMapSize100.buckets);
/*
            <5 empty items>,
  bucket#5: [ { key: 'rat', value: 7 } ],
            <1 empty item>,
  bucket#7: [ { key: 'dog', value: 1 } ],
            <32 empty items>,
  bucket#41: [ { key: 'art', value: 8 } ],
            <49 empty items>,
  bucket#90: [ { key: 'cat', value: 2 } ],
            <9 empty items>
*/ 
```

Enter fullscreen mode Exit fullscreen mode

耶！🎊没有碰撞！

拥有一个更大的桶大小对于避免冲突是非常好的，但是它消耗了**太多的内存**，并且可能大部分的桶都不会被使用。

如果我们能有一个根据需要自动增加大小的散列表，那不是很好吗？嗯，那叫**老调重弹**，我们接下来要做的！

## 最优 HashMap 实现

如果我们有一个足够大的桶，我们将不会有碰撞，因此搜索时间将是 *`O(1)`* 。但是，我们怎么知道一个 hash 图的容量应该有多大呢？100?1,000?一百万？

分配大量内存是不切实际的。所以，我们能做的就是让哈希表根据负载系数自动调整大小。这个操作叫做**重散列**。

**加载因子**是对哈希映射有多满的度量。我们可以通过将物品的数量除以桶的大小来得到装载系数。

这将是我们最新最棒的哈希映射实现:

**优化的哈希映射实现**

[github . com/amejiarosario/DSA . js/blob/master/src/data-structures/maps/hash-maps/hash-map . js](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/maps/hash-maps/hash-map.js)

特别注意[的再散列方法](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/maps/hash-maps/hash-map.js#L198-L217)。这就是奇迹发生的地方。我们创建了一个容量翻倍的新散列表。

所以，**测试**我们的新实现来自上面的^

```
const assert = require('assert');
const hashMap = new HashMap();

assert.equal(hashMap.getLoadFactor(), 0);
hashMap.set('songs', 2);
hashMap.set('pets', 7);
hashMap.set('tests', 1);
hashMap.set('art', 8);
assert.equal(hashMap.getLoadFactor(), 4/16);

hashMap.set('Pineapple', 'Pen Pineapple Apple Pen');
hashMap.set('Despacito', 'Luis Fonsi');
hashMap.set('Bailando', 'Enrique Iglesias');
hashMap.set('Dura', 'Daddy Yankee');

hashMap.set('Lean On', 'Major Lazer');
hashMap.set('Hello', 'Adele');
hashMap.set('All About That Bass', 'Meghan Trainor');
hashMap.set('This Is What You Came For', 'Calvin Harris ');

assert.equal(hashMap.collisions, 2);
assert.equal(hashMap.getLoadFactor(), 0.75);
assert.equal(hashMap.buckets.length, 16);

hashMap.set('Wake Me Up', 'Avicii'); // <--- Trigger REHASH

assert.equal(hashMap.collisions, 0);
assert.equal(hashMap.getLoadFactor(), 0.40625);
assert.equal(hashMap.buckets.length, 32); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在我们添加第 12 个项目后，负载系数超过了 0.75，因此触发了一次重散列，使容量加倍(从 16 增加到 32)。此外，您可以看到碰撞数是如何从 2 提高到 0 的！

这个实现足够好，可以帮助我们搞清楚插入/搜索/删除/编辑等常见操作的运行时。

总而言之，散列表的性能将由下式给出:

1.  每个键为不同的输出产生的散列函数。
2.  保存数据的存储桶的大小。

我们两个都搞定了🔨。我们有一个不错的哈希函数，可以为不同的数据产生不同的输出。两个不同的数据永远不会返回相同的代码。此外，我们还有一个重新散列功能，可以根据需要自动增加容量。太好了！

### 在 HashMap 运行时插入元素

在 HashMap 上插入元素需要两件事:一个键和一个值。我们可以使用我们开发的 [DecentHashMap](#DecentHashMap) 数据结构或者使用内置的如下:

```
function insert(object, key, value) {
  object[key] = value;
  return object;
}

const object = {};
console.log(insert(hash, 'word', 1)); // => { word: 1 } 
```

Enter fullscreen mode Exit fullscreen mode

在现代 JavaScript 中，可以使用`Map` s.

```
function insertMap(map, key, value) {
  map.set(key, value);
  return map;
}

const map = new Map();
console.log(insertMap(map, 'word', 1)); // Map { 'word' => 1 } 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**我们将使用`Map`而不是常规的`Object`，因为地图的键可以是任何东西，而对象的键只能是字符串或数字。另外，`Map` s 保持插入顺序。

在幕后，`Map.set`只是将元素插入到一个数组中(看看 [`DecentHashMap.set`](#DecentHashMap) )。所以，类似于`Array.push`我们有这样的说法:

> 在 HashMap 运行时插入元素是 *O(1)* 。如果需要重新散列，那么将需要 *O(n)*

我们用[再散列](#HashMapWithRehash)功能的实现将冲突保持在最小。再散列操作需要 *`O(n)`* ，但它并不是一直发生，只是在需要的时候才会发生。

### 在 HashMap 运行时搜索/访问元素

这是我们用来获取与键相关的值的`HashMap.get`函数。让我们从 [`DecentHashMap.get`](#DecentHashMap) 来评价一下实现吧:

```
 get(key) {
    const hashIndex = this.getIndex(key);
    const values = this.array[hashIndex];
    for (let index = 0; index < values.length; index++) {
      const entry = values[index];
      if(entry.key === key) {
        return entry.value
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果没有冲突，那么`values`将只有一个值，访问时间将是 *`O(1)`* 。但是，我们知道会有碰撞。如果初始容量太小，哈希函数很糟糕，就像 [NaiveHashMap.hash](#NaiveHashMap) 一样，那么大部分元素将在几个桶 *`O(n)`* 中结束。

> HashMap 访问操作平均运行时间为 *`O(1)`* ，最差运行时间为 *`O(n)`* 。

**高级注意:**减少从 *O(n)* 到 *O(log n)* 获取元素时间的另一个思路是用一个*二叉查找树*代替一个数组。实际上， [Java 的 HashMap 实现](http://hg.openjdk.java.net/jdk9/jdk9/jdk/file/f08705540498/src/java.base/share/classes/java/util/HashMap.java#l145)在一个 bucket 拥有超过 [8 个元素](http://hg.openjdk.java.net/jdk9/jdk9/jdk/file/f08705540498/src/java.base/share/classes/java/util/HashMap.java#l257)时会从数组切换到树。

### 在 HashMap 运行时编辑/删除元素

编辑(`HashMap.set`)和删除(`HashMap.delete`)键/值对有一个 *`O(1)`* 的**分摊**运行时。在多次碰撞的情况下，我们可能会面临一个 *`O(n)`* 的最坏情况。然而，通过我们的再散列操作，我们可以降低这种风险。

> HashMap 的编辑和删除操作平均运行时间为 *`O(1)`* ，最差运行时间为 *`O(n)`* 。

## HashMap 操作时间复杂度

我们可以将阵列的时间复杂度总结如下:

**HashMap 时间复杂度**

| 操作 | 最差的 | 分期偿还 | 评论 |
| --- | --- | --- | --- |
| 访问/搜索(`HashMap.get`) | *T2`O(n)`* | *T2`O(1)`* | *`O(n)`* 是碰撞太多的极端情况 |
| 插入/编辑(`HashMap.set`) | *T2`O(n)`* | *T2`O(1)`* | *`O(n)`* 只有当 hash 为 0.75 满时才会发生再散列 |
| 删除(`HashMap.delete`) | *T2`O(n)`* | *T2`O(1)`* | *`O(n)`* 是碰撞太多的极端情况 |

## 集

集合与数组非常相似。不同的是，他们不允许重复。

如何实现一个集合(没有重复的数组)？我们可以使用一个数组，在插入一个新的元素之前检查它是否存在。但是检查一个项目是否已经存在的运行时间是 *`O(n)`* 。我们能做得更好吗？我们开发了分摊运行时间为 *`O(1)`* 的`Map`！

### 设定实现

我们可以使用内置的 JavaScript】。但是，如果我们自己实现的话，扣除运行时就更符合逻辑了。我们将使用具有再散列功能的[优化散列表](#HashMapWithRehash)。

```
const HashMap = require('../hash-maps/hash-map');

class MySet {
  constructor() {
    this.hashMap = new HashMap();
  }

  add(value) {
    this.hashMap.set(value);
  }

  has(value) {
    return this.hashMap.has(value);
  }

  get size() {
    return this.hashMap.size;
  }

  delete(value) {
    return this.hashMap.delete(value);
  }

  entries() {
    return this.hashMap.keys.reduce((acc, key) => {
      if(key !== undefined) {
        acc.push(key.content);
      }
      return acc
    }, []);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`HashMap.set`来添加没有重复的集合元素。我们使用键作为值，因为哈希映射键是惟一的，所以我们都设置好了。

检查一个元素是否已经存在可以使用`hashMap.has`来完成，它有一个分摊的运行时间 *`O(1)`* 。除了得到`entries`即 *`O(n)`* 之外，大多数操作都是摊余常数时间。

注意:JS 内置的`Set.has`有一个运行时为 *O(n)* ，因为它使用一个常规的元素列表，并且一次检查一个元素。你可以在这里看到`Set.has`算法

下面是一些如何使用它的例子:

```
const assert = require('assert');
// const set = new Set(); // Using the built-in
const set = new MySet(); // Using our own implementation

set.add('one');
set.add('uno');
set.add('one'); // should NOT add this one twice

assert.equal(set.has('one'), true);
assert.equal(set.has('dos'), false);

assert.equal(set.size, 2);
// assert.deepEqual(Array.from(set), ['one', 'uno']);

assert.equal(set.delete('one'), true);
assert.equal(set.delete('one'), false);
assert.equal(set.has('one'), false);
assert.equal(set.size, 1); 
```

Enter fullscreen mode Exit fullscreen mode

对于这些示例，您应该能够互换使用`MySet`和内置`Set`。

### 设定操作运行时

从我们使用 HashMap 的 Set 实现中，我们可以将时间复杂度总结如下(非常类似于 HashMap):

**设定时间复杂度**

| 操作 | 最差的 | 分期偿还 | 评论 |
| --- | --- | --- | --- |
| 访问/搜索(`Set.has`) | *T2`O(n)`* | *T2`O(1)`* | *`O(n)`* 是碰撞太多的极端情况 |
| 插入/编辑(`Set.add`) | *T2`O(n)`* | *T2`O(1)`* | *`O(n)`* 只在 hash 为 0.75 满的情况下与*再散列*发生 |
| 删除(`Set.delete`) | *T2`O(n)`* | *T2`O(1)`* | *`O(n)`* 是碰撞太多的极端情况 |

## 链表

链表是一种数据结构，其中每个元素都与下一个元素相连。

[![](img/62a675447b14e97d682d56983ac300c1.png "LinkedList")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cTAuB0Cw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/linked-list.jpg)

链表是我们要实现的第一个不使用数组的数据结构。相反，我们将使用一个`node`来保存一个`value`并指向下一个元素。

```
class Node {
  constructor(value) {
    this.value = value;
    this.next = null;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们有一个节点链，每个节点指向下一个节点时，我们就有了一个**单链表**。

## 单链表

对于一个单链表，我们只需要担心每个元素对下一个元素的引用。

我们从构造根元素或头元素开始。

```
class LinkedList {
  constructor() {
    this.root = null;
  }

  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在每个链表中，我们可以进行四种基本操作:

1.  `addLast`:将一个元素追加到列表的末尾(尾部)
2.  `removeLast`:删除列表末尾的元素
3.  `addFirst`:在列表的开头添加一个元素(head)
4.  `removeFirst`:从列表的开头删除一个元素(head/root)

**添加/删除链表末尾的元素**

主要有两种情况:

1.  如果 list first (root/head)还没有任何元素，我们将这个节点作为列表的头部。
2.  相反，如果列表已经有了条目，那么我们必须迭代，直到找到最后一个条目，并将新节点追加到末尾。

```
 addLast(value) { // similar Array.push
    const node = new Node(value);

    if(this.root) {
      let currentNode = this.root;
      while(currentNode && currentNode.next) {
        currentNode = currentNode.next;
      }
      currentNode.next = node;
    } else {
      this.root = node;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

这段代码的运行时是怎样的？如果是第一个元素，那么加到根就是 *O(1)* 。但是，找到最后一项是 *O(n)* 。

现在，从列表末尾删除一个元素有类似的代码。我们必须在最后之前找到电流，并使其`next`参考`null`。

```
 removeLast() {
    let current = this.root;
    let target;

    if(current && current.next) {
      while(current && current.next && current.next.next) {
        current = current.next;
      }
      target = current.next;
      current.next = null;
    } else {
      this.root = null;
      target = current;
    }

    if(target) {
      return target.value;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

运行时也是 *O(n)* ，因为我们必须迭代到倒数第二个元素，并删除对最后一个元素的引用(第 10 行)。

**从链表的开头添加/删除元素**

向列表的头部添加一个元素如下所示:

```
 addFirst(value) {
    const node = new Node(value);
    node.next = this.first;
    this.first = node;
  } 
```

Enter fullscreen mode Exit fullscreen mode

从头开始添加和删除元素是一个恒定的时间，因为我们持有对第一个元素的引用:

```
 addFirst(value) {
    const node = new Node(value);
    node.next = this.first;
    this.first = node;
  } 
```

Enter fullscreen mode Exit fullscreen mode

正如所料，从链表中移除/添加第一个元素的运行时间总是常数 *O(1)*

**从链表中移除任意位置的元素**

移除列表中任意位置的元素会利用`removeLast`和`removeFirst`。但是，如果删除是在中间进行的，那么我们将前一个节点分配给下一个节点。从当前节点移除任何引用的；这将从列表中删除:

```
 remove(index = 0) {
    if(index === 0) {
      return this.removeFirst();
    }

    for (let current = this.first, i = 0; current;  i++, current = current.next) {
      if(i === index) {
        if(!current.next) { // if it doesn't have next it means that it is the last
          return this.removeLast();
        }
        current.previous = current.next;
        this.size--;
        return current.value;
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

注意`index`是一个从零开始的索引:0 将是第一个元素，1 秒以此类推。

> 移除列表中任何位置的元素都是 *O(n)* 。

**在链表中搜索元素**

在链表中搜索一个元素与`remove`非常相似:

```
 contains(value) {
    for (let current = this.first, index = 0; current;  index++, current = current.next) {
      if(current.value === value) {
        return index;
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

该函数查找具有给定值的第一个元素。

> 在链表中搜索元素的运行时间是 *O(n)*

### 单链表时间复杂度

每个函数的单链表时间复杂度如下。

| 操作 | 运行时间 | 评论 |
| --- | --- | --- |
| [T2`addFirst`](//#DoublyLinkedList.addFirst) | *O(1)* | 在列表的开头插入元素 |
| [T2`addLast`](//#SinglyLinkedList.addLast) | *O(n)* | 在列表末尾插入元素 |
| [T2`add`](//#DoublyLinkedList.add) | *O(n)* | 在列表中的任意位置插入元素。 |
| [T2`removeFirst`](//#DoublyLinkedList.removeFirst) | *O(1)* | 移除列表开头的元素 |
| [T2`removeLast`](//#SinglyLinkedList.removeLast) | *O(n)* | 移除列表末尾的元素 |
| [T2`remove`](//#LinkedList.remove) | *O(n)* | 从列表中删除任何元素 |
| [T2`contains`](//#LinkedList.contains) | *O(n)* | 从列表中搜索元素 |

请注意，每次我们从最后一个位置添加/删除时，操作都需要 *O(n)* ...

> 但是我们可以将`addLast` / `removeLast`从 *O(n)* 减少到一个平坦的 *O(1)* ，如果我们保留最后一个元素的引用！

我们将在下一节添加最后一个引用！

### 双向链表

当我们有一个节点链，每个节点都指向下一个节点时，我们就有了一个**单链表**。当我们有一个链表时，每个节点都通向下一个的**和上一个**元素的**时，我们就有了一个**双重链表****

[![](img/9988482058bc41e1b01cd9660db5da40.png "Doubly Linked List")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dM-ocdsR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/doubly-linked-list.jpg)

双向链表节点有双重引用(next 和 previous)。我们还将跟踪列表的第一个和最后一个元素。

```
class Node {
  constructor(value) {
    this.value = value;
    this.next = null;
    this.previous = null;
  }
}

class LinkedList {
  constructor() {
    this.first = null; // head/root element
    this.last = null; // last element of the list
    this.size = 0; // total number of elements in the list
  }

  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

**从列表开始处添加和删除**

从列表开始添加和删除很简单，因为我们有`this.first`引用:

```
 addFirst(value) {
    const node = new Node(value);

    node.next = this.first;

    if(this.first) {
      this.first.previous = node;
    } else {
      this.last = node;
    }

    this.first = node; // update head
    this.size++;

    return node;
  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们必须非常小心，更新以前的，大小和最后。

```
 removeFirst() {
    const first = this.first;

    if(first) {
      this.first = first.next;
      if(this.first) {
        this.first.previous = null;
      }

      this.size--;

      return first.value;
    } else {
      this.last = null;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

运行时间是多少？

> 从(单/双)链表中添加和删除元素有一个恒定的运行时间 *O(1)*

**在列表末尾添加和删除**

从列表的末尾添加和删除*有点棘手。如果你检查了单链表，两个操作都需要花费 *O(n)* ，因为我们必须遍历链表来找到最后一个元素。现在，我们有了`last`参考:*

```
 addLast(value) {
    const node = new Node(value);

    if(this.first) {
      let currentNode = this.first;
      node.previous = this.last;
      this.last.next = node;
      this.last = node;
    } else {
      this.first = node;
      this.last = node;
    }

    this.size++;

    return node;
  } 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们必须小心更新引用和处理特殊情况，比如只有一个元素时。

```
 removeLast() {
    let current = this.first;
    let target;

    if(current && current.next) {
      current = this.last.previous;
      this.last = current;
      target = current.next;
      current.next = null;
    } else {
      this.first = null;
      this.last = null;
      target = current;
    }

    if(target) {
      this.size--;
      return target.value;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

使用双向链表，我们不再需要遍历整个链表来获得倒数第二个元素。我们可以直接用`this.last.previous`和 is `O(1)`。

你还记得对于队列，我们必须使用两个数组吗？现在，我们可以改变实现，使用一个双向链表来代替，它有一个 *O(1)* 用于在开始时插入，在结束时删除。

**在链表的任意位置添加元素**

在列表的任意位置添加元素利用了我们的`addFirst`和`addLast`函数，如下所示:

```
 add(value, index = 0) {
    if(index === 0) {
      return this.addFirst(value);
    }

    for (let current = this.first, i = 0; i <= this.size;  i++, current = (current && current.next)) {
      if(i === index) {
        if(i === this.size) { // if it doesn't have next it means that it is the last
          return this.addLast(value);
        }
        const newNode = new Node(value);
        newNode.previous = current.previous;
        newNode.next = current;

        current.previous.next = newNode;
        if(current.next) { current.next.previous = newNode; }
        this.size++;
        return newNode;
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在数组中间插入一个元素，那么我们必须更新周围元素的`next`和`previous`引用。

> 在列表中的任意位置添加元素是 *O(n)* 。

## 双链表时间复杂度

每个函数的双向链表时间复杂度如下:

| 操作 | 运行时间 | 评论 |
| --- | --- | --- |
| [T2`addFirst`](//#DoublyLinkedList.addFirst) | *O(1)* | 在列表的开头插入元素。 |
| [T2`addLast`](//#DoublyLinkedList.addLast) | *O(1)* | 在列表末尾插入元素。 |
| [T2`add`](//#DoublyLinkedList.add) | *O(n)* | 在列表中的任意位置插入元素。 |
| [T2`removeFirst`](//#DoublyLinkedList.removeFirst) | *O(1)* | 移除列表开头的元素。 |
| [T2`removeLast`](//#DoublyLinkedList.removeLast) | *O(1)* | 移除列表末尾的元素。 |
| [T2`remove`](//#LinkedList.remove) | *O(n)* | 从列表中删除任何元素 |
| [T2`contains`](//#LinkedList.contains) | *O(n)* | 从列表中搜索任何元素 |

与单链表相比，双向链表是一个显著的改进！我们通过以下方式从 *O(n)* 改进为 *O(1)* :

*   添加对前一个元素的引用。
*   持有对列表中最后一项的引用。

移除第一个/最后一个可以在恒定时间内完成；但是在数组中间消去还是 *O(n)* 。

# 书库

Stacks 是一种数据结构，其中最后输入的数据首先出现。也称为后进先出(LIFO)。

[![](img/ef9cfe25952daa86455349fb884e7c9e.png "Stack: push and pop")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kd2-Ir7Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/stack.jpg)

让我们从头开始实现一个堆栈！

```
class Stack {
  constructor() {
    this.input = [];
  }

  push(element) {
    this.input.push(element);
    return this;
  }

  pop() {
    return this.input.pop();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这很容易，因为我们使用了内置的`Array.push`和`Array.pop`。两者都有运行期的 *`O(1)`* 。

让我们来看一些它的用法的例子:

```
 const stack = new Stack();

  stack.push('a');
  stack.push('b');
  stack.push('c');

  stack.pop(); // c
  stack.pop(); // b
  stack.pop(); // a 
```

Enter fullscreen mode Exit fullscreen mode

第一个进来(`a`)作为最后一个出去。我们也可以用链表代替数组来实现堆栈。运行时间是一样的。

仅此而已！

# 队列

队列是一种数据结构，其中第一个进入的数据也是第一个出去的数据。又称先进先出(FIFO)。
这就像看电影的一队人，第一个进来的就是第一个出来的。

[![](img/3120e260d583ec05d31e033fb4f2cc44.png "Queue: enqueue and dequeue")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zVi7Aoom--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/queue.jpg)

我们可以使用数组来实现队列，这与我们实现堆栈的方式非常相似。

## 用数组实现的队列

一个简单的实现是使用`Array.push`和`Array.shift`:

```
class Queue {
  constructor() {
    this.input = [];
  }

  add(element) {
    this.input.push(element);
  }

  remove() {
    return this.input.shift();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Queue.add`和`Queue.remove`的时间复杂度是多少？

*   `Queue.add`使用具有恒定运行时间的`array.push`。赢了！
*   `Queue.remove`使用具有线性运行时间的`array.shift`。我们能比 *`O(n)`* 做得更好吗？

想一个方法，你可以只用`Array.push`和`Array.pop`来实现一个队列。

```
class Queue {
  constructor() {
    this.input = [];
    this.output = [];
  }

  add(element) {
    this.input.push(element);
  }

  remove() {
    if(!this.output.length) {
      while(this.input.length) {
        this.output.push(this.input.pop());
      }
    }
    return this.output.pop();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们使用两个数组而不是一个。

```
const queue = new Queue();

queue.add('a');
queue.add('b');

queue.remove() // a
queue.add('c');
queue.remove() // b
queue.remove() // c 
```

Enter fullscreen mode Exit fullscreen mode

当我们第一次删除某个东西时，`output`数组是空的。所以，我们把`input`的内容像`['b', 'a']`一样向后插入。然后我们从`output`数组中弹出元素。正如你所看到的，使用这个技巧，我们以相同的插入顺序(FIFO)得到输出。

运行时间是多少？

如果输出已经有了一些元素，那么移除操作就是常量 *`O(1)`* 。当输出数组需要重新填充时，需要 *`O(n)`* 来完成。重新填充后，每个操作将再次保持不变。摊销时间为 *`O(1)`* 。

如果我们使用 LinkedList，我们可以用一个纯常量实现一个`Queue`。让我们看看下一节是什么！

## 用双向链表实现队列

我们可以使用链表而不是数组来获得最佳的性能。

```
const LinkedList = require('../linked-lists/linked-list');

class Queue {
  constructor() {
    this.input = new LinkedList();
  }

  add(element) {
    this.input.addFirst(element);
  }

  remove() {
    return this.input.removeLast();
  }

  get size() {
    return this.input.size;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用带有最后一个元素引用的双向链表，我们实现了一个 *O(1)* 的`add`。这就是为正确的工作使用正确的工具的重要性💪

# 总结

我们探索了大多数线性数据结构。我们看到，根据我们实现数据结构的方式，会有不同的运行时。转到顶部，那里有一个表格，列出了我们在此探索的所有示例。
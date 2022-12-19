# 酏剂中的列表与元组

> 原文：<https://dev.to/appsignal/lists-vs-tuples-in-elixir-17ln>

欢迎来到另一个版本的[仙丹炼金术](https://appsignal.com/elixir-alchemy)。今天，我们将探讨[列表](https://hexdocs.pm/elixir/List.html)和[元组](https://hexdocs.pm/elixir/Tuple.html)在仙丹中的用法。我们将看看如何使用这些数据结构中的每一种，并看看何时适合使用其中的一种。

# 历数仙丹中的元祖

列表和元组共享一些相似性，这使得很难辨别每个数据结构何时适合使用。它们都可以保存任意类型的几个值。从视觉上看，它们甚至看起来很相似，唯一的区别是各自使用的括号:

```
iex> list = [1, 2, true, 3]
iex> tuple = {1, 2, true, 3} 
```

Enter fullscreen mode Exit fullscreen mode

但是，在决定使用哪一种存储数据时，它们有一些重要的基本区别。

列表意味着保存可变长度的数据，而元组意味着具有固定数量元素的单个数据点。

从概念上讲，你可以认为这是存储一个数字序列(一个*列表*的数字)相对于存储几个对应于某个值的数字，例如，三个数字对应于空间中的一个 3D 点(即由 3 个数字组成的单个值)。

## 列表

列表在内部表示为*链表*。它们具有以下特征:

*   它们支持添加/删除元素
*   内存的使用随着列表的大小而增加。列表的元素越多，需要的内存就越多
*   获取元素有时可能会很慢

想象上面的列表(`[1, 2, true, 3]`)放在你电脑的内存里。它是任意分布的，没有特定的顺序，每个元素都包含对下一个元素的引用:

[![](../Images/0b15f00a92fd390790615c95f993568d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yY9l9HT1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.appsignal.com/images/blog/2018-08/list.svg%3F)

为了到达第三个元素，系统需要知道列表从哪里开始(这几乎是它所知道的全部)，然后从那里开始遍历。这意味着要到达第 100 个元素，必须遍历之前的所有 99 个元素。

为了添加一个新元素，一个新的内存槽被保留**在任何有空闲内存可用的地方**，然后一个对它的引用被添加到前面的最后一个元素。

当然，你的编译器可以做一些优化来优化这一切。但是总的原则仍然成立。

## 元组

元组的行为类似于静态数据结构，只是它们没有每个元素的显式名称。除此之外，它们所有的其他特征都非常相似:

*   它们应该保存固定数量的元素
*   由于上一点，内存使用也是静态的，并且是提前分配的。
*   数据结构不支持添加/删除元素。任何改变都需要重新创建结构的全新版本。

在内存中，这看起来与列表有很大不同:

[![](../Images/f7c4f6b8cdfab2c383fb32fd9999f128.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IP2tV9Ze--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.appsignal.com/images/blog/2018-08/tuple.svg%3F)

您可以看到内存使用的可预测性要高得多(实际上是 100%可预测的)。

访问一个元素只需要:

*   对元组内存开始位置的引用
*   您正在访问的元素的索引

如果您来自其他编程语言，您可能会认为这与访问静态数组非常相似。你是对的。元组只不过是静态大小数组的抽象。区别在于赋予两者的语义。

数组倾向于保存我们可以添加或删除的数据。虽然旧的 C 风格数组通常有固定的大小，但最近的语言很容易为我们提供推送、弹出、移动和取消移动元素的方法。支持这一点的内存处理方式因语言而异。

另一方面，元组并不意味着改变。它们提供一条信息，而不是一系列信息。

适合放入元组的项目示例:

### 姓名&电子邮件对

当你看到`"Miguel Palhas <miguel@example.com>"`时，你可以在大多数电子邮件客户端中看到这一点。在仙丹中，这可能是`{"Miguel Palhas", "miguel@example.com"}`。实际上有一个[药剂库](https://github.com/thoughtbot/bamboo)可以做到这一点。

### 2D 分

如`{ 1, 2 }`。这也可以表示为地图`%{ x: 1, y: 2 }`。无论您选择使用哪一种，这主要是个人选择的问题，是特定用例的问题。

### 键值对

键和值之间的任何映射都可以是二元元组:

```
a = { :name, "Miguel Palhas" }
b = { :email, "miguel@example.com" } 
```

Enter fullscreen mode Exit fullscreen mode

这就引出了一个有趣的问题:关键词列表。在《长生不老药》中，你可能已经遇到过这样的东西:

```
list1 = [ name: "Miguel Palhas", email: "miguel@example.com" ] 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，这只是一个列表的语法糖，其中每个值是一个 2 元素元组。它相当于:

```
 list2 = [
      { :name, "Miguel Palhas"},
      { :email, "miguel@example.com" }
    ] 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过从这个列表中获取一个元素来验证这一点:

```
 iex> Enum.at(list1, 0)
    {:name, "Miguel Palhas"} 
```

Enter fullscreen mode Exit fullscreen mode

看看它在内存中的样子，我们会看到这样的内容:

[![](../Images/2885e83c2752c6060fc7128f9f20d35f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--89tT6SeU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.appsignal.com/images/blog/2018-08/keyword.svg%3F)

## 可以添加到元组吗？

是的，你可以。在 Elixir 中，你可以使用`Tuple.append/1`、`Tuple.insert_at/1`或`Tuple.delete_at/1`等功能。

看看它们的作用，你可能会像对待列表一样使用它们，随意添加和删除元素。

然而，很少使用这些函数，因为它们实际上是用内存中其他地方的更新结果创建一个全新的元组。正如您可能看到的，这并没有很好地扩展。不断地重新创建元组来添加一些元素很快就会失控。

对于列表，添加/删除元素是一种廉价的操作，不需要扰乱现有的内存，这就是为什么它们更适合动态集合。

## 总结

我们谈到了列表和元组在数据结构上的差异。并且了解了这在内存中是如何工作的。从元组中获取数据更便宜，向列表中添加数据更便宜。

我们很想知道您对这篇文章的看法，或者您是否有任何问题。我们总是在寻找可以研究和解释的话题，所以如果你有任何关于长生不老药的东西想看，不要犹豫，留下你的评论。

*本文由客座作者[米盖尔·帕哈斯](http://naps62.github.io/)撰写。米盖尔是专业的总工程师[@子视觉](https://twitter.com/subvisual)，组织 [@rubyconfpt](https://twitter.com/rubyconfpt) 和 [@MirrorConf](https://twitter.com/mirrorconf) 。*
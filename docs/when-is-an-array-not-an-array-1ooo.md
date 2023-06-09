# 什么时候是数组，不是数组？

> 原文：<https://dev.to/_codingblocks/when-is-an-array-not-an-array-1ooo>

[![Solar Array Picture, Photo by Andreas Gücklhorn on Unsplash](img/552b1dd7b4c8d8a3acc76a5d78dd1a3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---3FUkdkP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tpkhtxyqzz4agt1fywn1.jpg)

TL；DR:不要对 JavaScript 数组做奇怪的事情。

*咳咳*

## 什么是数组？

在计算机科学中数组数据结构的标准定义中，数组是相似元素的集合，其中任何成员都可以被索引访问。这个定义听起来与[散列表](https://en.wikipedia.org/wiki/Hash_table)或[之类的没有太大区别，但是这里的关键区别是数组的元素必须在内存中占据相同的大小，并且数组索引需要是数字的，以便任何单个元素的逻辑地址都可以从该数字中导出。](https://en.wikipedia.org/wiki/Associative_array)

> 数组通常是从连续内存中创建的。这是一个简单的计算，就像如果我们知道一个数组位于内存地址 1024，并且数组中的每个元素都有一个对对象的 32 位引用，那么我们可以通过一个简单的计算来查找任何单独的项:
> 
> element _ address =(1024+(32 * index))
> 
> 你想要索引 14 的元素？给你，这是从地址 1472 开始的 32 位！

## 是什么让数组变得重要？

索引必须是数字，这很重要，因为这意味着我们可以在[常数时间](https://en.wikipedia.org/wiki/Time_complexity#Constant_time)内*计算*数组中任何元素的内存地址。这使得任何[数据结构](https://en.wikipedia.org/wiki/Data_structure)中最快的[随机存取](https://en.wikipedia.org/wiki/Random-access_memory)——即使在最坏的情况下。哈希表很棒，但是有与它们的[哈希和冲突处理](http://interactivepython.org/runestone/static/pythonds/SortSearch/Hashing.html)相关的开销。

查看 [Big-O 算法复杂度备忘单](http://bigocheatsheet.com/)以获得更好的视觉对比。

所以，回到我们最初的问题…

## 什么时候是数组，不是数组？

JavaScript 是出了名的松散，它的数组类型也不例外。

当然，我们可以做所有正常的数组类型操作。但是我们也可以做一些奇怪的事情。

这里有一个简单的数组定义，但是你认为这个操作最终分配了多少内存？

```
var myArray = [0,1,2]; 
```

Enter fullscreen mode Exit fullscreen mode

类似的 C 语言代码会分配一个 3 倍大小的数组。在大多数语言中，试图在内存块之外设置一个值(比如 100000)会很糟糕，但是 JavaScript 根本不介意。事实上，JavaScript 在需要什么方面非常宽松。让我们继续这个例子……

```
myArray[100000] = 100000; // JS is cool with this
myArray[-1] = -1.33333; // and this
myArray['mmm...need more coffee'] = 'go get it, lazy bones!'; // this too 
```

Enter fullscreen mode Exit fullscreen mode

上述例子显示了几种违反自然的罪行。超出原始声明的界限，负索引，非数字索引，设置不同的值类型…罪大恶极。这些操作与上面的定义不相容。

给定上面关于数组数据结构的关键定义特性的信息 JavaScript 数组怎么可能是数组呢？朋友，答案是风吹草动，数组数据结构和 T2 数组类型是有区别的。

> JavaScript 数组总是数组类型，但它们并不总是数组数据结构。

JavaScript 没有*和*实现，它有很多。 [V8、TraceMonkey 和 Chakra](https://en.wikipedia.org/wiki/JavaScript_engine) 都是流行的 JavaScript 引擎的例子。这使得当涉及到技术实现时，很难真正说出“JavaScript 做什么”。即使我们设法找到了特定引擎的相关代码点，明天也可能全部改变。也就是说，事情相当稳定，所以(举例来说)在 V8 中彻底改造数组实现是不太可能的。鉴于引擎的规模，代码潜水是困难的，但对我们来说幸运的是，一些真正聪明的人选择了引导我们通过最显着的位。

在 [V8](https://github.com/v8/v8/tree/master/src/base) (以及大多数其他实现)中，用于表示内存中 JavaScript 数组的底层数据结构可以根据您的使用情况而改变。如果你从一个整数数组开始，那么 V8 将用一个整数的[动态数组](https://en.wikipedia.org/wiki/Dynamic_array)来表示。如果超出了数组的界限，那么 V8 要么会给你分配一个足够大的新数组来容纳你的新索引，要么在某些情况下甚至会**动态地将你的数组转换成一个新的数据结构**，这对于[处理稀疏值](https://en.wikipedia.org/wiki/Sparse_matrix)来说更加有效。如果你在数组中添加了一个与 V8 开始时的数据类型不匹配的值，那么它将为你分配一个更通用类型的新数组。在上面的代码中，我们从整数开始，但是一旦我们添加了一个小数，类型就改变了，并且当我们添加字符串时会再次改变，除了我使用的索引也是一个字符串，所以 V8 会再次让**动态转换底层数据结构**以适应。

> 用于在内存中表示 JavaScript 数组的底层数据结构可以根据您的使用情况而改变。

JavaScript 是如何将它们的数组称为数组的呢？正式定义并不总是与“真实”世界完全一致，数组数据结构和数组类型之间也有区别。数组类型有时是用其他数据结构实现的，就像上面列出的情况一样，这没问题。JavaScript 不是唯一松散的语言，至少它的索引[是从零](http://xahlee.info/comp/comp_lang_array_index_start_0_or_1.html)开始的！

## 那么，我们学到了什么？

不要对 JavaScript 数组做奇怪的事情。JS 是宽松而复杂的，除了上面显示的例子引起的正常可读性噩梦之外，还有性能影响。阅读这篇文章，如果你想了解更多，请观看视频。

感谢阅读，请在评论中告诉我你的想法！

* * *

喜欢这个帖子？这是本期播客关于数组和其他类似数据结构的研究结果，如果你对这样的话题感兴趣，可以订阅 T2 代码块。

安德烈亚斯·古尔霍恩在 [Unsplash](/search/photos/array?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片
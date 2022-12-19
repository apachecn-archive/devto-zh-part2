# 替换 Array.slice 的情况

> 原文：<https://dev.to/robinheghan/the-case-for-replacing-arrayslice-4gfc>

我最近做了一些工作，需要我创建一个`Array`的子部分。这可以通过使用`Array.slice`来完成，但是这样做总是给我一种有改进空间的感觉。

为了解释原因，我们先来看一个小例子:

`Array.slice 2 5 someArray`

对我来说，这有什么作用还不是很明显。我知道它创建了一个子部分，但是当我读这个的时候，我总是不得不停下来计算结果会是什么。通常，因为我不经常使用这个函数，所以我还必须查阅文档来检查 from 和 to 索引是互斥的还是包含的。

结论是这个特定的代码将创建一个新的数组，包含`someArray`的索引 2、3 和 4 的值。当您实际上想从某个索引中提取数组的一个子部分并将其放入(但不包括)另一个索引中时，这可能看起来不太糟糕，但以我的经验来看，很少会出现这种情况。

通常你——嗯，我——想要的要么是删除前几个元素，要么是删除最后几个元素。因为`slice`是我们得到的全部，我们总是要考虑对两个参数使用什么值，这增加了一点点，但是不必要的精神开销。

例如，删除前两个元素的实现如下:

`Array.slice 2 (Array.length someArray) someArray`

这不像`Array.drop 2 someArray`那么容易读，但是另一个问题是它不太适合管道。删除最后一个元素更容易，因为`slice`支持负索引，尽管它比它应该有的更隐晦一些:

`Array.slice 0 -1 someArray`

即使你真的想要一个真正的子部分，我也认为如果你把它表达成两个独立的操作会更容易理解，就像这样:

```
someArray
  |> Array.drop 2
  |> Array.take 3 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
someArray
  |> Array.drop 2
  |> Array.dropLast 2 
```

Enter fullscreen mode Exit fullscreen mode

总之，我认为`slice`不适合函数式编程。使用具有清晰意图和直观用法的功能更合适，因此我认为`slice`应该替换为`take`、`takeLast`、`drop`和`dropLast`。
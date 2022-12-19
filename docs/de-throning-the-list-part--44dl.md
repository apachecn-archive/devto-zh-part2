# 去列表的喉咙:π部分

> 原文：<https://dev.to/robinheghan/de-throning-the-list-part--44dl>

在[的上一篇文章](https://dev.to/skinney/de-throning-the-list-part-deux-4idm)中，我们看到了如何改变`Array`的实现，使其比现在更通用(比如五星)。在这篇文章中，我们将看看我们是否能使它更具扩展性。

可扩展？那是什么意思？

让我们看看如何为`Array`实现类似于`find`的函数。`find`接受一个谓词函数和一个集合，并返回集合中谓词返回 true 的第一项，如果谓词从不返回 true，则返回`Nothing`。

为`Array`实现这个的唯一方法是使用`foldl`或`foldr` :

```
find : (a -> Bool) -> Array a -> Maybe a
find pred array =
    Array.foldl
        (\item result ->
            if result == Nothing && pred item then
                Just item
            else
                result
        )
        Nothing
        array 
```

Enter fullscreen mode Exit fullscreen mode

虽然这个实现做了我们想要的，但它不是那么高效。当这个实现找到我们要找的东西时，它将继续遍历集合中所有剩余的项目。根据阵列的大小，这可能会非常慢。

`List`好点了吗？没错没错就是:

```
find : (a -> Bool) -> List a -> Maybe a
find pred list =
    case list of
        [] ->
            Nothing

        x :: xs ->
            if pred x then
                Just x
            else
                find pred xs 
```

Enter fullscreen mode Exit fullscreen mode

这个实现正是我们想要的。这就是`List`作为一个简单数据结构的好处，很容易对它进行模式匹配以获得我们想要的功能。我在第一个帖子中简单解释了为什么`Array`的内部结构没有这样暴露，但我还是要再说一遍:这简直不简单，而且很容易出错，会导致难以发现的 bug。现在，在你带着`Array`的头离开之前，让我们探索一下如何为`Array`获得同样的可扩展性。

看看`find`的`List`版本，我们需要的是一种迭代集合的方法，但是能够说嘿！那是我的马！并缩短迭代。我们可以借鉴 Common Lisp 的思想，给`Array` API 添加一个新函数，它允许我们发出信号，表明我们已经完成了迭代。这样一个函数，在下面的例子中称为`stoppableFoldl`，将允许我们像这样实现`find`:

```
find : (a -> Bool) -> Array a -> Maybe a
find pred array =
    Array.stoppableFoldl
        (\item result ->
            if pred item then
                Done (Just item)
            else
                Continue result
        )
        Nothing
        array 
```

Enter fullscreen mode Exit fullscreen mode

看起来没那么糟。让我们看看这些实现在性能方面如何相互比较。以下基准测试是在 2012 年年中的 Macbook Air 上运行的，使用的是最新版本的 Chrome。基准测试创建了一个列表和一个包含 100 个元素的数组，并试图在集合的中间找到一个值。代码可以在[这里](https://github.com/Skinney/elm-array-exploration/blob/stoppable-foldl/bench/Main.elm)找到。

```
List:   402,870 ops/sec
Array:  227,745 ops/sec
% diff: -43.47% 
```

Enter fullscreen mode Exit fullscreen mode

哦。看来`List`还是更快。这可能是为`Array`中的每一项调用一个函数，以及为每一项执行额外分配(Done/Continue)的组合。

“但是罗宾，”你说。“这个基准不就证明了`List`比`Array`好吗？这篇博文花了这么长时间才发表，是因为你一直在镇上游荡，用酒精来抚慰你受伤的自尊吗？”

是的。我是说不！我在本系列中的观点是，`Array`应该是默认的，因为与`List`相比，它是一个更通用的数据结构。这并不意味着`Array`将在所有基准测试中击败`List`，但它将在大多数操作中有不错的性能。

例如，我们也可以提供一个`stoppableFoldr`函数来实现`findLast`。`find`和`findLast`的性能应该是差不多的。对于`List`来说，情况并非如此，对于`findLast`来说，情况会更糟。

这就是我想说的观点:`List`在少数事情上很擅长，`Array`在大多数事情上很好。甚至`Array`在其内部实现中也使用了`List`(看看`Array.filter`是如何实现的)。对于大多数应用来说，`Array`是一个更好的默认值。所以也许`Array`不应该完全取代`List`，而只是使用字面语法时构造的数据类型(就像`[1, 2, 3]`)。甚至将`List`重命名为`Stack`也不失为一个好主意，以表明它真正擅长的是什么。

# 还剩下什么？

还有一些事情是你不能用`Array`做的，但却可以用`List`做。在本系列的[下一部分](https://dev.to/skinney/de-throning-the-list-part-sc4k-4e3n)中，我们将比较`Array`和`List`模块公开的 API，看看我们需要在哪些地方弥合差距，甚至是不应该在哪些地方弥合差距。
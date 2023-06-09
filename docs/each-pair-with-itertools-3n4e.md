# 每对都带有 Itertools

> 原文：<https://dev.to/rpalo/each-pair-with-itertools-3n4e>

*封面照片由[丹金](https://unsplash.com/photos/hXaHghBkEMQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/pears?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄。*

快速提示时间！

本周，我在 Python 中的 Rosalind 上处理一些编码挑战。其中一个问题是，我需要遍历字符串中连续的字符组，并对它们进行处理。例如，如果字符串是“ACGTACAGTACTGACAGATCA”，我想对以下子字符串进行操作:

```
"ACGT"
"CGTA"
"GTAC"
"TACA"
... 
```

Enter fullscreen mode Exit fullscreen mode

如果我是用 Ruby 编写的，这将是 [each_cons](https://ruby-doc.org/core-2.5.1/Enumerable.html#method-i-each_cons) 方法的完美用例！不幸的是，Python 内置了一个类似的函数，就没有这么幸运了。我最终选择了稍微不太漂亮的:

```
def each_cons(n, iterable):
    """Returns every n consecutive items in an iterable.
    Example: each_cons(4, range(10)) => [0, 1, 2, 3], [1, 2, 3, 4], [2, 3, 4, 5]...
    """
    return (iterable[i: i + n] for i in range(len(iterable) - n + 1)) 
```

Enter fullscreen mode Exit fullscreen mode

任何时候你写`range(len(...`的时候，你都可能有更好的方式来完成事情。如果你知道一个更巧妙、更简单的方法，请分享，因为肯定有更好的方法。我不能像野蛮人一样手动索引东西。

然而，这不是这篇文章的内容。在我寻找更好的方法时，我发现了一个对`itertools`模块的巧妙使用，它太棒了，不能不分享。

## 每对用 Itertools

只有当你只需要两个连续的条目时，这种方法才真正有效，但是对我来说，这种方法感觉很不自然。我给你看代码，然后指出关键部分。

```
from itertools import tee

def each_pair(iterable):
    """Returns each consecutive pair of items in an iterable"""
    first, second = tee(iterable)
    next(second, None)
    return zip(first, second)

each_pair(range(10))
# => [0, 1], [1, 2], [2, 3], [3, 4], [4, 5]... 
```

Enter fullscreen mode Exit fullscreen mode

Ahhhh *太好了。*

## 魔解释道

那么，它是如何工作的呢？

首先，我们使用`itertools.tee`，它返回原始 iterable 的两个副本(这是一个有趣的词，表示可以迭代的任何东西:字符串、列表、生成器等。).如果你想制作`n`的副本，而不是只有 2 个副本，那么`n`也需要一个可选参数。

现在我们有了原始 iterable 的两个相同的副本。然后我们在第二个项目上调用`next`,以便向前移动一个项目。注意第二个参数`next`，`None`。`next`函数返回(并用完)iterable 中的下一项，如果你给它提供了一个*默认值*并且它不能返回另一项，因为 iterable 是空的，它就返回默认值。

```
# next only works on Iterator objects a = iter([1, 2, 3])
next(a, "EMPTY")
# => 1 next(a, "EMPTY")
# => 2 next(a, "EMPTY")
# => 3 next(a, "EMPTY")
# => "EMPTY" next(a)
# => Error!  StopIteration! 
```

Enter fullscreen mode Exit fullscreen mode

这只是一种简洁的说法“向前循环一个项目，但是如果你已经空了，不用担心。不要抛出错误就好。”你可能也注意到了，我们没有对调用`next`的结果做任何事情。我们只是扔掉一个项目，如果它存在的话。

最后一部分是将两个 iterables 压缩在一起。`zip`是一个复杂的函数，看起来很简单，但结果往往让人难以理解。它将每个迭代器的第 n 项传递到一个列表中。

在一个例子中更容易看出:

```
a = [1, 2, 3, 4]
b = ["Apple", "Banana", "Pear", "Soup"]
c = [True, False, True, False]

zip(a, b, c)
# => [
#        [1, "Apple", True],
#        [2, "Banana", False],
#        [3, "Pear", True],
#        [4, "Soup", False],
# ] 
```

Enter fullscreen mode Exit fullscreen mode

诀窍是，如果我们的列表有不同的长度，它将只使用最短的一个。

```
a = [1, 2, 3, 4, 5, 6, 7, 8]
b = [1, 2, 3]
zip(a, b)
# => [[1, 1], [2, 2], [3, 3]]  See how it drops the last 5 elements of 'a'? 
```

Enter fullscreen mode Exit fullscreen mode

> `itertools`模块还有一个`zip_longest`函数，如果 zip 的一些参数太短，该函数将接受一个默认值来填充。
> 
> ```
> a = [1, 2, 3, 4, 5, 6]
> b = [1, 2]
> itertools.zip_longest(a, b, fillvalue=":)")
> # => [
> #     [1, 1],
> #     [2, 2],
> #     [3, ":)"]
> #     [4, ":)"]
> #     [5, ":)"]
> #     [6, ":)"]
> # ] 
> ```

## 总结起来

不管怎样，我觉得这很棒。我喜欢在不使用任何强力索引的情况下从 iterable 中得到我想要的东西。看起来它减少了一个接一个的错误。

就像我之前说的，如果你有更好的方法来制作上面的`each_cons`函数，我想看看。请务必评论或发推特给我。

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/06/09/itertools-each-pair/)*
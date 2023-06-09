# 高效计算排列

> 原文：<https://dev.to/mariosangiorgio/efficiently-compute-permutations-2phk>

> 排列:一组或若干事物可以被排序或安排的几种可能方式中的每一种。

# 问题

给定几个项目，找出所有可能的排列方式会很有趣。例如，字符串`ABC`有六种排列:`ABC`、`ACB`、`BAC`、`BCA`、`CAB`、`CBA`。注意，置换数是项目数的[阶乘](https://en.wikipedia.org/wiki/Factorial)。发生这种情况是因为我们可以选择任何一个项目作为第一个项目，我们有`#items - 1`个选项用于第二个项目，以此类推，直到我们用一个选项到达最后一个项目。

# 递归求解

我们可以递归地计算所有的排列，如这个 Python 函数所示:

```
def permutations(items):
  if len(items) == 1:
    return [items]
  result = []
  # We keep only the distinct items to avoid returning duplicates
  for item in list(set(items)):
    others = items[:]
    others.remove(item)
    for permutation in permutations(others):
      # Note: if we want lexicographical order we should insert in front
      # assuming that items are sorted
      permutation.append(item)
      result.append(permutation)
  return result 
```

Enter fullscreen mode Exit fullscreen mode

我们可以执行这个并得到我们期望的结果:

```
>>> permutations([1,2,3])
[[3, 2, 1], [2, 3, 1], [3, 1, 2], [1, 3, 2], [2, 1, 3], [1, 2, 3]]
>>> permutations([1,2,1])
[[2, 1, 1], [1, 2, 1], [1, 1, 2]] 
```

Enter fullscreen mode Exit fullscreen mode

# 更好的方法

这个解决方案是可行的，它很好也很简单，但是效率不是很高:它要求我们创建大量条目列表的副本，以便我们可以将它们传递到下一个递归步骤。另一个缺点是这个函数计算(并保存在内存中)所有的排列，如果我们只需要其中的几个，这是一种浪费。

我们可以从另一个角度来看这个问题，问我们自己是否有任何方法可以获得一个项目列表，并且只找出下一个排列，不管这意味着什么。

如果我们谈论下一个排列，我们暗示它们之间有一个顺序。原则上，排列的定义并不意味着任何顺序，但我们总是可以自己添加一个约束，并要求某种顺序。使用[字典顺序](https://en.wikipedia.org/wiki/Lexicographical_order)是一个很好的选择，它允许我们[为给定的条目列表生成](https://en.wikipedia.org/wiki/Permutation#Generation_in_lexicographic_order)下一个排列。

回到我们最初的例子，我们可以从`ABC`开始，重新组织项目以获得`ACB`，下次再这样做以获得`BAC`，等等。这将允许我们计算所有的排列，但这一次一次，没有不必要的列表副本。

按字典顺序排列的第一个列表以*非递减*顺序排列所有条目:`forall i: items[i] <= items[i+1]`。在我们的例子中，我们有`ABC`、`A < B`和`B < C`。相反，最后一项的所有项目按*非递增*顺序排列:`forall i: items[i] >= items[i+1]`。在我们的例子中，我们有`CBA`和`C > B`和`B > A`。

我们的算法需要通过重新排列元素来使我们从这两个极端过渡，使得每个排列以最小的量“增加”序列。关键的观察是，我们想要在*非递减*顺序中寻找最长的后缀(即，这已经是最大的可能)。姑且称之为`suffix`。为了取得一些进展，我们需要考虑`suffix`中的所有项目以及紧接在它前面的项目，我们称之为`pivot`。如果我们从`0125430`开始，我们可以将弦分成`prefix = 01`、`pivot = 2`和`suffix = 5430`。

我们正在寻找最小的变化，所以我们不应该碰`prefix`。如果我们这样做，我们会得到比我们想要的更大的变化。我们只能移动`pivot`和`suffix`里的物品。我们还必须满足这两个条件，以便能够按字典顺序前进到下一个排列:

*   我们需要用尽可能小的值替换`pivot`；
*   我们必须重新排列物品，使新的`suffix`最小化。

我们可以通过用大于`pivot`本身的`suffix`中最小的项目替换`pivot`来满足第一个条件。取较小的值意味着倒退，取较大的值意味着走得太远。在我们的例子中，我们将得到`prefix = 01`、`pivot = 3`和`suffix = 5420`。

这是朝着正确方向迈出的一步，但我们还必须满足第二个条件。我们有一个最大后缀，我们可以通过简单地反转它来使它最小，这具有将*非递减*阶变为我们所追求的*非递增*阶的效果。在我们的例子中，我们将得到`prefix = 01`、`pivot = 3`和`suffix = 0245`。我们完成了，下一个排列是`0130245`

## 算法

实现这一点的算法包括以下步骤:

1.  找到最大的`k`使得`a[k] < a[k+1]`。如果我们找不到它，我们正在寻找的项目已经是字典顺序中的最后一个。我们可以通过*回绕*找到下一项，并按字典顺序生成第一个排列。
2.  找到比`k`大的最大指数`l`，使得`a[k] < a[l]`。
3.  交换`a[k]`和`a[l]`。
4.  颠倒从`a[k + 1]`到最后一个元素`a[n]`的顺序。

在代码中，这看起来像:

```
def next_permutation(items):
  n = len(items)
  # Find pivot
  k = None
  for i in range(n-1):
    if(items[i] < items[i+1]):
      k = i
  if k is None:
    # items is maximal. We need to wrap around
    return None
  # Find new pivot
  for i in range(k, n):
    if(items[k] < items[i]):
      l = i
  # Swap pivot
  items[k], items[l] = items[l], items[k]
  # Reverse suffix
  for i in range(k+1, int((k+1+n)/2)):
    items[i], items[n-i] = items[n-i], items[i]
  return items 
```

Enter fullscreen mode Exit fullscreen mode

我们可以这样使用它:

```
>>> permutation = [1,2,3]
>>> while permutation != None:
... print(permutation)
... permutation = next_permutation(permutation)
...
[1, 2, 3]
[1, 3, 2]
[2, 1, 3]
[2, 3, 1]
[3, 1, 2]
[3, 2, 1] 
```

Enter fullscreen mode Exit fullscreen mode

# 其他方法

这不是产生排列的唯一可能的方法。事实上还有其他几种算法。我发现[堆的算法](https://en.wikipedia.org/wiki/Heap%27s_algorithm)特别有趣，因为它设法通过交换一对元素来生成下一个排列。

感谢 [Project Nayuki](https://www.nayuki.io/page/next-lexicographical-permutation-algorithm) 的帖子启发我研究这个算法。
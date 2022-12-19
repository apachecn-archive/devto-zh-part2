# 无栈快速排序

> 原文：<https://dev.to/scotchka/stackless-quicksort-202h>

快速排序是递归算法的一个著名例子。下面是一个次优实现:

```
def qsort(lst, start=0, end=None):
    if end is None:
        end = len(lst)
    if end - start < 2:
        return

    pivot_position = partition(lst, start, end)

    qsort(lst, start, pivot_position)
    qsort(lst, pivot_position + 1, end) 
```

分区函数选择一个枢纽值，并将其放在正确的位置，较小的值放在左边，较大的值放在右边。它还返回透视值的最终位置。

```
def partition(lst, start, end):
    pivot = lst[start]
    rest = lst[start + 1 : end]

    left = [item for item in rest if item <= pivot]
    right = [item for item in rest if item > pivot]

    lst[start:end] = left + [pivot] + right
    return start + len(left) 
```

为了简洁起见，我们使用列表切片而不是交换，但是讨论并不依赖于分区是如何完成的。

在上面的实现中，观察到每个递归调用都是独立的，只是对列表的一部分进行排序。正如这篇[文章](https://bertrandmeyer.com/2014/12/07/lampsort/)所指出的，递归调用栈仅仅用于确保列表被分成更小的片段，直到每个项目都是一个枢纽或者属于一个项目的一个片段。

因为列表不同部分的排序顺序无关紧要，所以我们不需要递归，甚至不需要堆栈。下面是一个快速排序的实现，它使用一个集合来跟踪哪些数据段仍然需要排序:

```
def qsort_stackless(lst):
    not_sorted = {(0, len(lst))}

    while not_sorted:
        start, end = not_sorted.pop()
        pivot_position = partition(lst, start, end)
        if pivot_position - start > 0:
            not_sorted.add((start, pivot_position))
        if end - (pivot_position + 1) > 0:
            not_sorted.add((pivot_position + 1, end)) 
```

集合`not_sorted`包含仍待排序的段的开始和结束索引。注意，pop 方法返回一个集合的任意元素，当没有未排序的段剩余时，该元素变为空。然后对列表进行排序。让我们检查一个测试用例:

```
>>> lst = [3, 2, 1, 4, 5]
>>> qsort_stackless(lst)
>>> lst[1, 2, 3, 4, 5] 
```
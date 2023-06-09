# 快速排序

> 原文：<https://dev.to/annlin/quick-sort-3fh7>

嘿，陈格，这是给你的。

[https://www.youtube.com/embed/stWj4rowLAc](https://www.youtube.com/embed/stWj4rowLAc)

以下是我对快速排序的看法。动画进行到一半时，我意识到我没有最好的例子。已经太晚了。在上班的火车上所有的绘画和动画...就当是吸取了教训。

下面是代码:

```
 def quicksort(start, end, array):
    if start < end:
        pivot = partition(start, end, array)
        quicksort(start, pivot - 1, array)
        quicksort(pivot + 1, end, array)

def partition(start, end, array):
    actualPivotPosition = start
    pivotValue = array[end]
    for i in range(start,end):
        if array[i] < pivotValue:
            array[i], array[actualPivotPosition] = array[actualPivotPosition], array[i]
            actualPivotPosition += 1

    array[actualPivotPosition], array[end] = array[end], array[actualPivotPosition]
    return actualPivotPosition

array = [5,7,8,1,3,2,4,6]

quicksort(0, len(array) - 1, array)
print ("Array:", array)
# Array: [1, 2, 3, 4, 5, 6, 7, 8] 
```

步骤:

1.  给定一个数组，设置最后一个位置为支点
2.  将透视移动到数组中的实际排序位置
3.  继续使用步骤 1 对透视前后的子数组进行排序

最坏的情况是支点总是最小或最大。为了防止这种情况发生，你可以选择一个随机的位置，与最后一个头交换随机位置，然后继续同样的排序。

在 https://www.geeksforgeeks.org/quick-sort/查看更好的教程
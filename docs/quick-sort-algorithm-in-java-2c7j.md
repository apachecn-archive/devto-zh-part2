# Java 中的快速排序算法

> 原文：<https://dev.to/code2bits/quick-sort-algorithm-in-java-2c7j>

快速排序，也称为分区交换排序，是一种高效的分治排序算法。该算法可以利用循环或递归来实现。该算法就地执行排序。快速排序是一种比较排序，这意味着它可以对定义了“小于”关系的任何类型的项目进行排序。

该算法将阵列分成两个更小的子阵列。为了将算法分成两个数组，选择数组中的一个枢轴或元素。分区阶段包括对数组进行重新排序，使所有值小于轴心的元素出现在轴心之前，而所有值大于轴心的元素出现在轴心之后。在分割阶段之后，枢轴处于其最终位置。枢纽在逻辑上将原始数组分成两个子数组。然后，快速排序算法通过选择一个新的中枢对子数组进行递归排序，并相应地移动值。每个元素都将被选为轴心，并被放置在正确的位置。

### 算法分类

下表包含有关快速排序算法分析的信息。它定义了时间复杂度方面的最坏情况、一般情况和最好情况，以及空间复杂度方面的最坏情况。

| 属性 | 价值 |
| --- | --- |
| 班级 | 分类算法 |
| 分类 | 内部、就地、不稳定的算法 |
| 数据结构 | 排列 |
| 时间复杂度:最佳 | Ω(n log(n)) |
| 时间复杂度:平均 | Θ(n log(n)) |
| 时间复杂度:最差 | O(n <sup>2</sup> |
| 空间复杂度:最差 | O(n log(n)) |

请使用下面的[链接](http://www.bigocheatsheet.com/img/big-o-cheat-sheet-poster.png)了解 Big-O 符号的解释以及什么是好的、一般的和坏的。

### Java 中的快速排序

```
public final class QuickSort {

    public void sort(int[] collection) {
        if (collection != null) {
            quickSort(collection, 0, collection.length-1);
        } else {
            throw new IllegalArgumentException("Input paramenter for array to sort is null.");
        }
    } 

    private void quickSort(int[] collection, int firstPosition, int lastPosition) {
        if (firstPosition >= lastPosition) {
            return;
        } else {            
            int pivotIndex = partition(collection, firstPosition, lastPosition);
            quickSort(collection, firstPosition, pivotIndex-1);
            quickSort(collection, pivotIndex+1, lastPosition);
        }       
    } 

    private int partition(int[] collection, int firstPosition, int lastPosition) {  
        int pivotIndex = selectPivot(firstPosition, lastPosition);
        swap (collection, pivotIndex, lastPosition);
        int store = firstPosition;
        pivotIndex = lastPosition;
        for (int i = firstPosition; i <= lastPosition-1 ; i++) {
            if (collection[i] <= collection[pivotIndex]) {
                swap (collection, i, store);
                store++;
            }
        }
        swap (collection, store, pivotIndex);
        pivotIndex = store;
        return pivotIndex;
    }   

    private void swap(int[] collection, int x, int y) {
        int temp = collection[x];
        collection[x] = collection[y];
        collection[y] = temp;
    } 

    private int selectPivot(int first, int last) {
        return (first+last)/2;
    } 

} 
```

#### 示例代码(GitHub)

快速分类的细节可以在这里查看[。

快速排序 JUnit 测试类的详细内容可以在](https://github.com/Code2Bits/Algorithms-in-Java/blob/master/sort/src/main/java/com/code2bits/algorithm/sort/QuickSort.java)[这里](https://github.com/Code2Bits/Algorithms-in-Java/blob/master/sort/src/test/java/com/code2bits/algorithm/sort/QuickSortTest.java)查看。

### 结论

快速排序算法是更大的排序算法组的一部分。通过经验学习是我写这篇关于快速排序算法在 Java 中的实现的文章的原因。我学到了很多关于其他人如何用其他语言解决快速排序算法的知识，包括用 Java 的不同实现。

Java 中的 post [快速排序算法](https://www.code2bits.com/quick-sort-algorithm-in-java/)最早出现在 [Code2Bits](https://www.code2bits.com) 上。
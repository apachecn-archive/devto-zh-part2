# Java 中的归并排序算法

> 原文：<https://dev.to/code2bits/merge-sort-algorithm-in-java-5gfd>

合并排序是一种分治算法，因为该算法将原始数组分割成更小的逻辑部分。该算法可以利用循环或递归来实现。这两个不同的阶段是分裂阶段，其次是合并阶段。

*分裂阶段*:将数组分成两个未排序的数组。每个分割阵列被连续分割成更小的阵列，直到只存在多个单元素阵列。对单元素数组进行排序是因为它们只有一个元素。

*合并阶段*:每个单元素数组现在合并回一个更大的数组。在合并阶段，两个数组被合并，以便元素在新的更大的排序数组中排序。重复这个过程，直到剩下单个排序的数组。由于使用了临时数组，因此这不是一种就地算法。

### 算法分类

下表包含有关合并排序算法分析的信息。它定义了时间复杂度方面的最坏情况、一般情况和最好情况，以及空间复杂度方面的最坏情况。

| 属性 | 价值 |
| --- | --- |
| 班级 | 分类算法 |
| 分类 | 内部的、不适当的、稳定的算法 |
| 数据结构 | 排列 |
| 时间复杂度:最佳 | Ω(n log(n)) |
| 时间复杂度:平均 | Θ(n log(n)) |
| 时间复杂度:最差 | O(n log(n)) |
| 空间复杂度:最差 | O(n) |

请使用下面的[链接](http://www.bigocheatsheet.com/img/big-o-cheat-sheet-poster.png)了解 Big-O 符号的解释以及什么是好的、一般的和坏的。

### Java 中的合并排序

```
public final class MergeSort {

    public void sort(int[] collection) {
        if (collection != null) {
            mergeSort(collection, 0 , collection.length);
        } else {
            throw new IllegalArgumentException("Input paramenter for array to sort is null.");
        }
    }

    public  void mergeSort(int[] collection, int minIndex, int maxIndex) {
        if (maxIndex - minIndex < 2) {
            return;
        }

        int centre = (minIndex + maxIndex) / 2;
        mergeSort(collection, minIndex, centre);
        mergeSort(collection, centre, maxIndex);
        mergeBack(collection, minIndex, centre, maxIndex);       
    }

    public void mergeBack(int[] collection, int minIndex, int centre, int maxIndex) {
        if (collection[centre - 1] <= collection[centre]) {
            return;
        }
        int tempMinIndex = minIndex;
        int tempCentre = centre;

        int tempIndex = 0;
        int[] tempArray = new int[maxIndex - minIndex];

        while ((tempMinIndex < centre) && (tempCentre < maxIndex)) {
            if(collection[tempMinIndex] <= collection[tempCentre]) {
                tempArray[tempIndex++] = collection[tempMinIndex++];    
            } else {
                tempArray[tempIndex++] = collection[tempCentre++];
            }
        }

        System.arraycopy(collection, tempMinIndex, collection, minIndex + tempIndex, centre - tempMinIndex);
        System.arraycopy(tempArray, 0, collection, minIndex, tempIndex);
    }   
} 
```

#### 示例代码(GitHub)

MergeSort 类的细节可以在这里查看。

merge sort JUnit 测试类的详细信息可以在这里查看。

### 结论

合并排序算法是更大的排序算法组的一部分。通过经验学习是我写这篇关于用 Java 实现合并排序算法的文章的原因。我学到了很多关于其他人如何用其他语言解决合并排序算法的知识，包括不同的 Java 实现。

Java 中的 post [合并排序算法](https://www.code2bits.com/merge-sort-algorithm-in-java/)最早出现在 [Code2Bits](https://www.code2bits.com) 上。
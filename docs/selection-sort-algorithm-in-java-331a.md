# Java 中的选择排序算法

> 原文：<https://dev.to/code2bits/selection-sort-algorithm-in-java-331a>

选择排序是一种排序算法，特别是一种就地比较排序，用于对整数数组进行排序。该算法将输入列表分为两部分，已排序的和未排序的。该算法在未排序的部分中寻找最大的元素，并将其与未排序的分区中的最后一个位置交换。

### 算法分类

下表包含有关选择排序算法分析的信息。它定义了时间复杂度方面的最坏情况、一般情况和最好情况，以及空间复杂度方面的最坏情况。

| 分类 | 价值 |
| --- | --- |
| 班级 | 分类算法 |
| 数据结构 | 排列 |
| 时间复杂度:最佳 | ω(n<sup>2</sup> |
| 时间复杂度:平均 | θ(n<sup>2</sup> |
| 时间复杂度:最差 | O(n <sup>2</sup> |
| 空间复杂度:最差 | O(1) |

请使用下面的[链接](http://www.bigocheatsheet.com/img/big-o-cheat-sheet-poster.png)了解 Big-O 符号的解释以及什么是好的、一般的和坏的。

### 在 Java 中选择排序

```
public final class SelectionSort {

    public void sort(int[] collection) {
        if (collection != null) {
            selectionSort(collection);
        } else {
            throw new IllegalArgumentException("Input paramenter for array to sort is null.");
        }
    }

    private void selectionSort(int[] collection) {
        int arrayLength = collection.length;

        for (int unsortIndex = arrayLength - 1; unsortIndex > 0; unsortIndex--) {
            int largest = 0;
            for (int i = 1; i <= unsortIndex; i++) {
                if (collection[i] > collection[largest]) {
                    largest = i;
                }
            }
            swap(collection, largest, unsortIndex);
        }
    } 

    private void swap(int[] collection, int x, int y) {
        int temp = collection[x];
        collection[x] = collection[y];
        collection[y] = temp;
    }   
} 
```

#### 示例代码(GitHub)

SelectionSort 类的细节可以在这里查看[。

SelectionSort JUnit 测试类的详细内容可以查看](https://github.com/Code2Bits/Algorithms-in-Java/blob/master/sort/src/main/java/com/code2bits/algorithm/sort/SelectionSort.java)[这里](https://github.com/Code2Bits/Algorithms-in-Java/blob/master/sort/src/test/java/com/code2bits/algorithm/sort/SelectionSortTest.java)。

### 结论

选择排序算法是更大的排序算法组的一部分。通过经验学习是我写这篇关于选择排序算法在 Java 中的实现的文章的原因。我学到了很多关于其他人如何用其他语言解决选择排序算法的知识，包括在 Java 中的不同实现。

Java 中的 post [选择排序算法](https://www.code2bits.com/selection-sort-algorithm-in-java/)最早出现在 [Code2Bits](https://www.code2bits.com) 上。
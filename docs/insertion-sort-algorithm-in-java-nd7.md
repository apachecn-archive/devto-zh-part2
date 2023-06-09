# Java 中的插入排序算法

> 原文：<https://dev.to/code2bits/insertion-sort-algorithm-in-java-nd7>

插入排序是一种排序算法，它一次构建一个最终排序的数组(或列表)。该算法遍历列表并移除当前元素，在列表的排序部分中找到位置，并将其插入到该位置。重复该过程，直到整个列表被排序。

### 算法分类

下表包含有关插入排序算法分析的信息。它定义了时间复杂度方面的最坏情况、一般情况和最好情况，以及空间复杂度方面的最坏情况。

| 属性 | 价值 |
| --- | --- |
| 班级 | 分类算法 |
| 分类 | 内部、就地、稳定的算法 |
| 数据结构 | 排列 |
| 时间复杂度:最佳 | Ω(n) |
| 时间复杂度:平均 | θ(n<sup>2</sup> |
| 时间复杂度:最差 | O(n <sup>2</sup> |
| 空间复杂度:最差 | O(1) |

请使用下面的[链接](http://www.bigocheatsheet.com/img/big-o-cheat-sheet-poster.png)了解 Big-O 符号的解释以及什么是好的、一般的和坏的。

### 在 Java 中插入排序

```
public final class InsertionSort {

    public void sort(int[] collection) {
        if (collection != null) {
            insertionSort(collection);
        } else {
            throw new IllegalArgumentException("Input parameter for array to sort is null.");
        }
    } 

    private void insertionSort(int[] collection) {
        int arrayLength = collection.length;

        for (int unsortIndex = 1; unsortIndex < arrayLength;  unsortIndex++) {
            int newElement = collection[unsortIndex];
            int iterator = 0;

            for (iterator = unsortIndex; iterator > 0 && collection[iterator - 1] > newElement; iterator--) {
                collection[iterator] = collection[iterator - 1];
            }
            collection[iterator] = newElement;
        }
    }
} 
```

#### 示例代码(GitHub)

InsertionSort 类的细节可以在这里查看[。

InsertionSort JUnit 测试类的详细内容可以在](https://github.com/Code2Bits/Algorithms-in-Java/blob/master/sort/src/main/java/com/code2bits/algorithm/sort/InsertionSort.java)[这里](https://github.com/Code2Bits/Algorithms-in-Java/blob/master/sort/src/test/java/com/code2bits/algorithm/sort/InsertionSortTest.java)查看。

### 结论

插入排序算法是更大的排序算法组的一部分。通过经验学习是我写这篇关于 Java 中插入排序算法实现的文章的原因。我学到了很多关于其他人如何用其他语言解决插入排序算法的知识，包括 Java 中的不同实现。

Java 中的 post [插入排序算法](https://www.code2bits.com/insertion-sort-algorithm-in-java/)最早出现在 [Code2Bits](https://www.code2bits.com) 上。
# Java 中的 Shell 排序算法

> 原文：<https://dev.to/code2bits/shell-sort-algorithm-in-java-4hek>

Shell 排序是一种就地比较排序算法。外壳排序是插入排序的推广，它允许交换相距很远的项目。该算法通过对彼此远离元素对进行排序来执行初步工作。该算法逐渐减小要比较的元素之间的间隙，因为目标是减小元素在阵列上的移位量。随着间距减少到 1，该算法变得与插入排序算法相同。

### 算法分类

下表包含有关 Shell 排序算法分析的信息。它定义了时间复杂度方面的最坏情况、一般情况和最好情况，以及空间复杂度方面的最坏情况。

| 属性 | 价值 |
| --- | --- |
| 班级 | 分类算法 |
| 分类 | 内部、就地、不稳定的算法 |
| 数据结构 | 排列 |
| 时间复杂度:最佳 | Ω(n log(n)) |
| 时间复杂度:平均 | θ(n 个日志 <sup>2 个</sup> n 个) |
| 时间复杂度:最差 | O(n <sup>2</sup> |
| 空间复杂度:最差 | O(1) |

请使用下面的[链接](http://www.bigocheatsheet.com/img/big-o-cheat-sheet-poster.png)了解 Big-O 符号的解释以及什么是好的、一般的和坏的。

### Java 中的 Shell 排序

```
public final class ShellSort {

    public void sort(int[] collection) {
        if (collection != null) {
            shellSort(collection);
        } else {
            throw new IllegalArgumentException("Input paramenter for array to sort is null.");
        }
    }

    private void shellSort(int[] collection) {
        int arrayLength = collection.length;

        for (int gap = arrayLength / 2; gap > 0; gap /= 2) {
            for (int i = gap; i < arrayLength; i++) {
                int newElement = collection[i];

                int j = i;
                while (j >= gap && collection[j - gap] > newElement) {
                    collection[j] = collection[j - gap];
                    j -= gap;
                }
                collection[j] = newElement;
            }
        }
    } 
} 
```

#### 示例代码(GitHub)

Shell 排序类的细节可以在这里查看[。

Shell Sort JUnit 测试类的详细内容可以查看](https://github.com/Code2Bits/Algorithms-in-Java/blob/master/sort/src/main/java/com/code2bits/algorithm/sort/ShellSort.java)[这里](https://github.com/Code2Bits/Algorithms-in-Java/blob/master/sort/src/test/java/com/code2bits/algorithm/sort/ShellSortTest.java)。

### 结论

外壳排序算法是更大的排序算法组的一部分。通过经验学习是我写这篇关于 Java 中 Shell 排序算法实现的文章的原因。我学到了很多关于其他人如何用其他语言解决 Shell 排序算法的知识，包括 Java 中的不同实现。

Java 中的 post [Shell Sort 算法最早出现在](https://www.code2bits.com/shell-sort-algorithm-in-java/) [Code2Bits](https://www.code2bits.com) 上。
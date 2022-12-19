# Java 中的计数排序

> 原文：<https://dev.to/code2bits/counting-sort-in-java-1jkg>

计数排序是一种整数排序算法。计数排序与其他排序算法不同，它对数据做出某些假设。它计算具有不同键值的对象的数量，并使用算术来确定每个键的位置。该算法不利用比较来对值进行排序。简单地说，该算法计算每个值出现的次数，以便对其进行排序。

算法的初始阶段是开始计算数组中值的出现次数，并递增与计数键相关的值。第二阶段是利用每个计数键的出现次数写出新的排序数组。

### 算法分类

下表包含有关计数排序算法分析的信息。它定义了时间复杂度方面的最坏情况、一般情况和最好情况，以及空间复杂度方面的最坏情况。

| 属性 | 价值 |
| --- | --- |
| 班级 | 分类算法 |
| 分类 | 内部算法，非就地算法 |
| 数据结构 | 排列 |
| 时间复杂度:最佳 | Ω(n+k) |
| 时间复杂度:平均 | Θ(n+k) |
| 时间复杂度:最差 | O(n+k) |
| 空间复杂度:最差 | O(k) |

请使用下面的[链接](http://www.bigocheatsheet.com/img/big-o-cheat-sheet-poster.png)了解 Big-O 符号的解释以及什么是好的、一般的和坏的。

### Java 中的计数排序

```
public final class CountingSort {

    public void sort(int[] collection) {
        if (collection != null) {
            int maxValue = findMaxValue(collection);
            countingSort(collection, maxValue);
        } else {
            throw new IllegalArgumentException("Input paramenter for array to sort is null.");
        }
    }

    private void countingSort(int[] collection, int maxValue) {
        int[] countArray = countOccurrences(collection, maxValue);
        reconstructArray(collection, countArray, maxValue);
    }

    private int findMaxValue(int[] collection) {
        int highest = collection[0];
        for (int index = 1; index < collection.length; index ++) {
            if (collection[index] > highest) {
                highest = collection [index];
            }
        }
        return highest;
    }

    private int[] countOccurrences(int[] collection, int maxValue) {
        int[] tempArray = new int[maxValue + 1];
        for (int i = 0; i < collection.length; i++) {
            int key = collection[i];
            tempArray[key]++;
        }    
        return tempArray;
    }

    private void reconstructArray(int[] collection, int[] countArray, int maxValue) {
        int j = 0;
        for (int i = 0; i <= maxValue; i++) {
            while (countArray[i] > 0) {
                collection[j++] = i;
                countArray[i]--;
            }
        }
    }
} 
```

#### 示例代码(GitHub)

计数排序类的详细信息可在处查看[。

计数排序 JUnit 测试类的详细内容可以在这里](https://github.com/Code2Bits/Algorithms-in-Java/blob/master/sort/src/main/java/com/code2bits/algorithm/sort/CountingSort.java)查看[。](https://github.com/Code2Bits/Algorithms-in-Java/blob/master/sort/src/test/java/com/code2bits/algorithm/sort/CountingSortTest.java)

### 结论

计数排序算法是更大的排序算法组的一部分。从经验中学习是我写这篇关于用 Java 实现计数排序算法的文章的原因。我学到了很多关于其他人如何用其他语言解决计数排序算法的知识，包括在 Java 中的不同实现。

Java 中的[计数排序这个帖子最早出现在](https://www.code2bits.com/counting-sort-in-java/) [Code2Bits](https://www.code2bits.com) 上。
# 比较排序第 1 部分

> 原文：<https://dev.to/brightdevs/comparison-sorting-part-1-1h9e>

整理数据是计算机自发明以来一直在做的最重要的任务之一。这些年来，开发人员已经找到了很多方法。这些方法有些很快，有些涉及有趣的方法，最后还有一些实际上没有用，但知道得多总比知道得少好，所以最好在空闲时间学习。

在这一系列的博客文章中，我将按照复杂度来划分排序算法:

**第一组:**其中平均案件复杂度为 n^2，如冒泡，插入排序

**第二组:**其平均案例复杂度为 nlog(n)，但最差的是 n^2，如 quick，library sort

**第三组:**最差情况的复杂度是 nlog(n)，比如合并、堆排序

今天我将向你们展示第一组中的几个例子。它们大多被初学者使用，因为它们是最简单的。不幸的是，它们的简单是有代价的——长时间的工作。当我们谈论像 100 个元素这样的小数据块时，N^2 复杂性听起来并不坏——它只是 10，000 次运算。但是，当我们的程序必须对一百万个元素进行排序时，需要进行一万亿次运算。现在让我们开始真正的编码。我准备用 Kotlin，但是把每个算法翻译成其他语言很容易。

### 1)气泡

一个非常简单的算法，通常是年轻程序员学习的第一个。它的想法是将每个元素与下一个元素进行比较，并将较大的元素向右移动，在一次循环结束时，找到数组中最大的元素。然后一遍又一遍地重复，排除在前面的循环中已经找到的元素。

```
fun bubbleSort(array: IntArray) {
   var maxIndex = array.size-1
   for(i in 0..maxIndex) {
       for(j in 1..maxIndex) {
           if(array[j-1] > array[j]) {
               var temp = array[j-1]
               array[j-1] = array[j]
               array[j] = temp
           }
       }
       maxIndex--
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 2)插入

另一个基本算法。它所做的是一个接一个地获取所有元素，并通过将每个元素与之前检查过的每个元素进行比较，将它们放在正确的位置。

```
fun insertionSort(array: IntArray) {
   val maxIndex = array.size-1
   var j: Int
   for(i in 1..maxIndex) {
       var temp = array[i]
       j = i - 1
       while(j >= 0 && array[j] > temp) {
           array[j+1] = array[j]
           j--
       }
       array[j+1] = temp
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 3)选择

顾名思义，它选择集合中最小的元素，并将其放在开头。它在没有先前找到的元素的情况下重复，直到我们的集合被排序。

```
fun selectionSort(array: IntArray) {
   val maxIndex = array.size-1
   var minimum: Int
   var indexOfMinimum: Int
   for(i in 0..maxIndex) {
       minimum = array[i]
       indexOfMinimum = i
       for(j in i+1..maxIndex) {
           if(array[j] < minimum) {
               minimum = array[j]
               indexOfMinimum = j
           }
       }
       if(i != indexOfMinimum) {
           var temp = array[i]
           array[i] = array[indexOfMinimum]
           array[indexOfMinimum] = temp
       }
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 4)侏儒

这就像把插入和冒泡排序结合起来。我们检查单个循环中的每个元素，只要它不在正确的位置，就与前一个元素交换。

```
fun gnomeSort(array: IntArray) {
   val maxIndex = array.size-1
   var pos = 0
   while(pos < maxIndex) {
       if(pos == 0 || array[pos] >= array[pos-1]){
           pos++
       } else {
           var temp = array[pos]
           array[pos] = array[pos - 1]
           array[pos - 1] = temp
           pos--
       }
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 5)鸡尾酒

它也被称为双向冒泡排序——向前它比较元素，寻找最大的一个，向后它寻找最小的一个。

```
fun cocktailSort(array: IntArray) {
   var maxIndex = array.size-1
   for(i in 0..maxIndex) {
       for(j in 1..maxIndex) {
           if(array[j-1] > array[j]) {
               var temp = array[j-1]
               array[j-1] = array[j]
               array[j] = temp
           }
       }
       for(g in maxIndex downTo 1) {
           if(array[g-1] > array[g]) {
               var temp = array[g-1]
               array[g-1] = array[g]
               array[g] = temp
           }
       }
       maxIndex--
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，现在当我们完成了代码，让我们看看结果。

**样本#1:** 100 个由 1000 个整数组成的数组:

```
Avg of bubble sort: 1.59
Avg of insertion sort: 0.18
Avg of selection sort: 0.38
Avg of gnome sort: 1.08
Avg of cocktail sort: 1.65 
```

Enter fullscreen mode Exit fullscreen mode

**样本#2:** 100 组 10000 个整数:

```
Avg of bubble sort: 156.39
Avg of insertion sort: 13.01
Avg of selection sort: 30.25
Avg of gnome sort: 104.22
Avg of cocktail sort: 169.53 
```

Enter fullscreen mode Exit fullscreen mode

数据量增加 10 倍导致了这些时间倍增:

```
Bubble: ~98 times longer
Insertion: ~72 times longer
Selection: ~79 times longer
Gnome: ~97 times longer
Cocktail: ~103 times longer 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，对于所有算法，数据量增加 10 倍导致时间增加约 100 倍，这证实了它们的复杂性是 n^2.插入和选择排序稍微好一点，但它仍然是 n^2，所以我们应该把它作为最后的手段。

这部分到此为止。很快我将准备第二组的算法比较。

**PS。这篇文章让我确信了一件事——网络谎言。大多数消息来源说，梳状排序平均和最坏情况的复杂性是 n^2，但我的测试证明不同。经过长时间的研究，我甚至找到了一本关于 nlog(n)复杂性的书。你很快就会读到更多的相关内容。**

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

软件开发人员@光明发明
[电子邮件](//grzegorz.cie%C5%9Bla@beightinventions.pl)
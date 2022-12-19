# 数据结构:排序(基本)

> 原文：<https://dev.to/rinsama77/data-structure-sorting-basic-1pkn>

排序基本上意味着按照一定的顺序排列数据，这样我们就更容易使用这些数据。在这篇文章中，我将谈论一些基本的排序算法；冒泡排序、选择排序和插入排序。

##### 排序算法及其复杂性

| 算法 | 复杂性 |
| --- | --- |
| 冒泡排序 | O(n <sup>2</sup> |
| 选择排序 | O(n <sup>2</sup> |
| 插入排序 | O(n <sup>2</sup> |
| 外壳排序 | O(n <sup>2</sup> |
| 快速排序 | O(n <sup>2</sup> |
| 基数排序 | O(nk) |
| 堆排序 | o(国家和地区) |
| 合并排序 | o(国家和地区) |

在我们进入主要内容之前，我想推荐一个[网站](https://visualgo.net/en/sorting)，它有助于可视化所有这些排序算法是如何工作的。(另外，它对其他东西也有帮助，比如可视化哈希表、二进制堆等。).还有[这只](https://www.toptal.com/developers/sorting-algorithms)也很有帮助！

## 冒泡排序

在冒泡排序中，我们一次比较两个项目，我们或者交换它们，或者继续比较下一对。众所周知，它非常慢，但是从概念上来说，它是排序算法中最简单的。
T3![](../Images/eb3fe395cda229de67b65bd757e54013.png)T5】

一段伪代码，展示了如何对大小为 *n*
的数组进行排序

```
1\. Repeat i) to ii) for n times
    i) Compare the leftmost element in array with the next element
        a. If the leftmost element is bigger, swap them
        b. Else, go to 2.
    ii) Move to next position, set that element to be the leftmost element. Is there next element?
        a. Yes, go back to i)
        b. No, go back to 1.
2\. Done 
```

这是一个冒泡排序的 java 代码

```
public void bubbleSort() {
    int out, in, temp;
    int[] a = new int[nElems];      // nElems is the number of elements we'll input later ^^||
    for(out=nElems-1; out>1; out--) {  
        for(in=0; in<out; in++) {
            if( a[in] > a[in+1] ) { // out of order?
                temp = a[in];       // swap them
                a[in] = a[in+1];
                a[in+1] = temp;
            }
        }
    }
} 
```

#### 冒泡排序的效率

*   比较操作:

    > 对于 N 个项目:(N-1) + (N-2) + (N-3) +...+1
    > = N *(N-1)/2
    > ≈N<sup>2</sup>/2

*   交换操作:

    > 通常小于比较运算
    > ≈ N <sup>2</sup> /4

*   这意味着它的复杂度≈ O(N <sup>2</sup> )，相当慢

## 选择排序

在选择排序中，我们沿着数据移动，选择最小的项，将选中的项交换到位置 0，依此类推。
[![](../Images/e88cfcf622b028c2a54ea0885c73fe29.png)](http://www.cs.rmit.edu.au/online/blackboard/chapter/05/documents/contribute/chapter/09/array-sorting.html) 
一个大小为 *n*
的数组的伪代码

```
1\. Repeat i) to ii) for (n-1) rounds
    i) Go through every unsorted element
    ii) Pick the smallest one and swap with element at the first position (but next to those already been sorted)
2\. Done 
```

用于选择排序的 java 代码

```
public void selectionSort() {
    int out, in, min, temp;
    int[] a = new int[nElems];        // nElems is the number of elements we'll input later ^^||
    for(out=0; out<nElems-1; out++) {
        min = out;
        for(in = out+1; in<nElems; in++) {
            if(a[in] < a[min]) {      // if min is greater,
                min = in;             // we have a new min
                temp = a[out];        // swap 'em
                a[out] = a[min];
                a[min] = temp;
            }
        }
    }
} 
```

#### 选择排序的效率

*   比较操作:

    > 同冒泡排序
    > ≈ N <sup>2</sup> /2

*   交换操作:

    > 通常小于 N

*   这样我们得到 O(N<sup>2</sup>)；慢速:/

## 插入排序

我们把数据分成 *2 个列表*(排序和未排序)。在每次迭代中，未排序列表的第一个元素被插入到排序列表的适当位置。
[![](../Images/2084f65f7453ea6965eab877622515df.png)](http://www.cs.rmit.edu.au/online/blackboard/chapter/05/documents/contribute/chapter/09/array-sorting.html) 
伪代码

```
1\. Repeat i) and ii) for (n-1) rounds
    i) insert the first unsorted element, to the sorted
    ii) compare the newly inserted element to all the sorted, and put it in place
2\. Done 
```

java 代码！

```
public void insertionSort() {
    int in, out;
    int[] a = new int[nElems];         // nElems is the number of elements we'll input later ^^||
    for(out=1; out<nElems; out++) {    // out is dividing line
        long temp = a[out];            // remove marked item
        in = out;                      // start shifts at out
        while(in>0 && a[in-1]>=temp) { // until one is smaller,
            a[in] = a[in-1];           // shift item to right
            --in;                      // go left one position
        }
        a[in] = temp; // insert marked item
    }
} 
```

#### 选择排序的效率

*   比较操作:

    > N*(N-1)/4
    > ≈ N <sup>2</sup> /4

*   复制操作:

    > 通常类似于比较操作

*   其复杂度≈O(N<sup>2</sup>)；仍然很慢..

*   如果对数据进行排序，算法的运行时间几乎为 O(N)。

*   如果数据是逆序的，它的运行速度就像冒泡排序一样慢

# 比较 3 种简单排序

它们只需要它们的初始数据数组加上一点额外的内存，但是它们所有的算法都在 O(N <sup>2</sup> 时间内执行。

*   **冒泡排序**很简单，但是效率最低。如果数据量很小，它是实用的
*   **选择排序**最小化了交换的数量，但是比较的数量仍然很高
*   **插入排序**是三种排序中最通用的，并且在大多数情况下是最好的，假设数据量很小或者数据几乎被排序。
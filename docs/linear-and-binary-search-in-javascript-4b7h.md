# JavaScript 中的线性和二分搜索法

> 原文：<https://dev.to/stephjs/linear-and-binary-search-in-javascript-4b7h>

本周，我开始阅读 Grokking 的[算法](https://www.manning.com/books/grokking-algorithms)，这是一本针对程序员和其他好奇者的图解指南。到目前为止，这是一本很棒的读物——充满了实用的例子和有趣的图画，以可理解的方式解释了技术概念。书中的代码示例都是用 Python 写的。我主要是一名 JavaScript 开发人员，所以我想我应该通过这本书向您展示我的 JavaScript 代码。

## 在数组中搜索

您正在列表中搜索某个内容。您不确定它是否真的在列表中，但是如果在，您想知道它在哪里。在这种情况下，我们有一个彩虹，我们正在寻找一个特定的颜色。

```
var rainbow = ["red", "orange", "yellow", "green", "blue", "indigo", "violet"]; 
```

Enter fullscreen mode Exit fullscreen mode

#### ~~易~~ ~~难~~线性方式

你可能在想，“简单！我将遍历数组的每个元素并返回匹配结果！”这种方法有效，被称为线性搜索。

```
function linearSearch(arr, elToFind) {
  for (var i=0; i<arr.length; i++) {
    if (arr[i] == elToFind) {
      return i;
    }
  } return null;
}

linearSearch(rainbow, "green"); // returns 3
linearSearch(rainbow, "white"); // returns null 
```

Enter fullscreen mode Exit fullscreen mode

Buttttttt，(它的大小取决于数据集的大小)这里有一个性能折衷。你必须遍历每一个元素才能发现你的元素不在数组中。当我们只谈论 7 种颜色时，这是 nbd，但如果我们要处理成千上万条记录呢？算了吧。

#### 二分搜索法

一个[二分搜索法](https://en.wikipedia.org/wiki/Binary_search_algorithm)接受一个排序后的数组并寻找一个特定的元素。如果元素出现在数组中，搜索返回元素的索引；否则返回 null。因为数组已经排序，所以搜索可以将目标搜索元素与数组中间的元素进行比较，一次消除一半的搜索范围。把它想象成一个更热更冷的游戏。

[![alt text](img/85557cbe5b8036c9d5be73b2f4433c55.png "binary search image from Wikipedia")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TSNjmS8t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/Binary_Search_Depiction.svg/470px-Binary_Search_Depiction.svg.png)

#### 与二分搜索法再举彩虹的例子

你我都明白前面提到的彩虹的罗伊·g·BIV 排序，但是你的浏览器没有上幼儿园。为了在彩虹上执行二分搜索法，它需要(按字母顺序)排序。幸运的是，我们有 JavaScript 内置的数组排序方法。

```
var rainbow = ["red", "orange", "yellow", "green", "blue", "indigo", "violet"];
var sortedRainbow = rainbow.sort(); 
// returns ["blue", "green", "indigo", "orange", "red", "violet", "yellow"]; 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们有东西可以传给二分搜索法了。

```
function binarySearch(sortedArray, elToFind) {
  var lowIndex = 0;
  var highIndex = sortedArray.length - 1;
  while (lowIndex <= highIndex) {
    var midIndex = Math.floor((lowIndex + highIndex) / 2);
    if (sortedArray[midIndex] == elToFind) {
      return midIndex;
    } else if (sortedArray[midIndex] < elToFind) {
      lowIndex = midIndex + 1;
    } else {
      highIndex = midIndex - 1;
    }
  } return null;
}

var sortedRainbow = ["blue", "green", "indigo", "orange", "red", "violet", "yellow"];
binarySearch(sortedRainbow, "green"); // returns 1
binarySearch(sortedRainbow, "white") // returns null 
```

Enter fullscreen mode Exit fullscreen mode

好吧，太多了。或者你是一个搜索高手，你完全理解这一点。让我们一行一行地看二分搜索法。

*   **binarySearch** 函数接受一个 **sortedArray** 和一个您正在搜索的元素( **elToFind** )。

    *   在搜索过程中，您将跟踪您正在搜索的范围，起始值为 0 的**low index**和起始值为排序数组中元素数量的**high index**。搜索开始时，范围将跨越整个数组。
    *   执行 **while** 循环，直到搜索范围缩小到一个元素

        *   为了找到在 **lowIndex** 和 **highIndex** 之间的元素的索引，平均这两个值(注意:使用 Math.floor 将这个值向下舍入，因为 **midIndex** 必须是一个整数)
        *   如果找到了元素，返回索引
        *   如果当前元素小于(按字母顺序在前)正在搜索的元素，则将 **lowIndex** 增加到比 **midIndex** 大 1
        *   如果当前元素大于您正在搜索的元素(按字母顺序排在后面)，则将 **highIndex** 减少到比 **midIndex** 小一
    *   如果数组中不存在该元素，则返回 null

### 接下来

既然我们已经看了两种搜索方法(线性和二进制)，我们需要一种方法来衡量它们的性能。在我的下一篇文章中，我将着眼于对数(回到代数 2)和大 O 符号。敬请期待！
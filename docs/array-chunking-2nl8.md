# 阵列重庆

> 原文：<https://dev.to/ryanfarney3/array-chunking-2nl8>

所谓数组分块，我的意思是获取整个数组并创建一个包含原始数组元素的较小子数组的数组。这不仅是一个在技术访谈中经常被问到的概念，而且我还可以看到在排序或组织数据集时如何使用它。

在这里，我将处理一个相对简单的“数组分块”问题，并介绍几种不同的解决方法。这绝不是做这件事的唯一方法！

#### 问题

给定一个数组和块的大小，将数组分成许多子数组，每个子数组的长度为。

我相信，当我们可以看到一些预期的输出时，概念化这个问题就更容易了...

```
chunk([1, 2, 3, 4], 2)  //→ [[1, 2], [3, 4]]
chunk([1, 2, 3, 4, 5], 2)  //→ [[1, 2], [3, 4], [5]]
chunk([1, 2, 3, 4, 5, 6, 7, 8], 3)  //→ [[1, 2, 3],  [4, 5,6], [7, 8]]
chunk([1, 2, 3, 4, 5], 4)  //→ [[1, 2, 3, 4], [5]]
chunk([1, 2, 3, 4, 5], 10)  //→ [[1, 2, 3, 4, 5]] 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们可以看到“大小”是指每个子数组中有多少个元素。正如我们所看到的，它们并不总是均匀的，所以我们希望确保原始数组中的额外元素能够插入到最终的更小的子数组中。

好吧。我会一行一行地解释，但是...让我们编码已经☺.

#### 先解

这可能是一个更明显的解决方案，特别是对于不熟悉 JavaScript 的人来说。或者，也许你的面试官要求你不用一些花哨的 JS 方法来解决问题。你永远不知道！

```
function chunk(array, size) {
  //declaring variable 'chunked' as an empty array
  let chunked = [];

  //for loop iterating through every element of our input array
  for (let ele of array) {
    //declaring variable 'last' as the last index of our 'chunked' array
    const last = chunked[chunked.length-1];

    //checking if last is undefined or if the last subarray is equal to the size
    if (!last || last.length === size) {
      //then we push the element to be a new subarray in 'chunked'
      chunked.push([ele])
    } else {
      //if not, then we add the element to the 'last' subarray
      last.push(ele)
    }
  }
  //return the array of subarrays
  return chunked
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 秒解

在第二个解决方案中(可能是对我来说最自然的一个),我们利用。切片法。如果您不熟悉，请参考文档。切[这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)！这里要记住的关键是召唤。slice 将返回一个新的数组！

```
function chunk(array, size) {
  //declaring variable 'chunked' as an empty array
  let chunked = []

  //setting our start point for our while loop at index 0
  let i = 0;
  //looping through the array until we have reached the final index
  while (i < array.length) {
    //push the sliced subarray of length 'size' into our new 'chunked' array
    chunked.push(array.slice(i, i + size))
    //increment by the size as to avoid duplicates
    i += size;
  }
  //return the array of subarrays
  return chunked
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 第三种方案

最后一个解决方案可能是最奇特的(也是最短的)。就我个人而言，我还不太熟悉。拼接方法，但我知道它偶尔会在这类问题中派上用场。同样，请在此参考文档[！具体针对这个解决方案的需求，我将向下滚动一点，以参考在删除元素时如何使用它...但是它也可以做一些其他的事情。这里要记住的关键是。切，切。拼接方法对原数组进行了变异！](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) 

```
function chunk(array, size) {
  //declaring variable 'chunked' as an empty array
  let chunked = []

  //looping through the array until it has been entirely "manipulated" or split into our subarrays
  while(array.length > 0) {
    //taking the spliced segments completely out of our original array
    //pushing these subarrays into our new "chunked" array
    chunked.push(array.splice(0, size))
  }
  //returning the new array of subarrays
  return chunked
} 
```

Enter fullscreen mode Exit fullscreen mode

### 感谢光临！
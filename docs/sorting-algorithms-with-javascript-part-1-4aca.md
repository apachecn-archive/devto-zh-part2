# Javascript 排序算法(第 1 部分)

> 原文：<https://dev.to/wangonya/sorting-algorithms-with-javascript-part-1-4aca>

最近，我对数据结构和算法有了很多了解，我在阅读中注意到，并没有太多的例子展示了 Javascript 中算法的实现。你会发现大多数例子在 Java，Python，C，C++等。比起 Javascript，我们更喜欢这些语言可能是有原因的？我不确定。

在第一部分中，我将展示三种排序算法的 Javascript 实现:

*   合并排序
*   插入排序
*   冒泡排序

这并不是要深入解释算法如何工作及其性能的来龙去脉。如果你更想了解这些，我找到了一个不错的资源:[排序算法](https://brilliant.org/wiki/sorting-algorithms/)

为了简单起见，我将对一个只有 5 个元素的简单列表进行排序。

## 合并排序

合并排序使用分治法对数组中的元素进行排序。基本上，这意味着它不是把数组作为一个整体来处理，而是不断地把它分成两半，直到这两半都被排序，然后这两半被合并成一个已求解的列表。

#### 目测

[![merge-sort](../Images/e8fd38b2b38b81d451a34de48b42004a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--deJq_0u1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1npt37z4g0zjxiicljlm.gif)

#### 代码

```
function mergeSort(list) {
  const len = list.length
  // an array of length == 1 is technically a sorted list
  if (len == 1) {
    return list
  }

  // get mid item
  const middleIndex = Math.ceil(len / 2)

  // split current list into two: left and right list
  let leftList = list.slice(0, middleIndex)
  let rightList = list.slice(middleIndex, len)

  leftList = mergeSort(leftList)
  rightList = mergeSort(rightList)

  return merge(leftList, rightList)
}

// Solve the sub-problems and merge them together
function merge(leftList, rightList) {
  const sorted = []
  while (leftList.length > 0 && rightList.length > 0) {
    const leftItem = leftList[0]
    const rightItem = rightList[0]
    if (leftItem > rightItem) {
      sorted.push(rightItem)
      rightList.shift()
    } else {
      sorted.push(leftItem);
      leftList.shift()
    }
  }

  // if left list has items, add what is left to the results
  while (leftList.length !== 0) {
    sorted.push(leftList[0])
    leftList.shift()
  }

  // if right list has items, add what is left to the results
  while (rightList.length !== 0) {
    sorted.push(rightList[0])
    rightList.shift()
  }

  // merge the left and right list
  return sorted
}

const list = [4, 2, 3, 1, 5]

const sorted = mergeSort(list)

console.log(sorted) 
```

Enter fullscreen mode Exit fullscreen mode

## 插入排序

插入排序一次一个元素地构建最终的排序列表。它通过获取一个元素，将它与列表中的其余元素进行比较，找到它的正确位置，然后将它放在那里。

这就是所谓的基于比较的排序。

#### 目测

[![insertion-sort](../Images/028f768ed329733fe3c5bf5d9b7db632.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TnANY--p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g01s69r1ppo9kifien2v.gif)

#### 代码

```
function insertionSort(list) {
  const len = list.length
  for (let i = 1; i < len; i++) 
  {
    if (list[i] < list[0]) 
    {
      // move current element to the first position
      list.unshift(list.splice(i,1)[0])
    } 
    else if (list[i] > list[i-1]) 
    {
      // maintain element position
      continue
    } 
    else {
      // find where element should go
      for (let j = 1; j < i; j++) {
        if (list[i] >= list[j-1] && list[i] <= list[j]) 
        {
          // move element
          list.splice(j, 0, list.splice(i,1)[0])
        }
      }
    }
  }
  return list
}

const list = [4, 2, 3, 1, 5]

const sorted = insertionSort(list)

console.log(sorted) 
```

Enter fullscreen mode Exit fullscreen mode

## 冒泡排序

基于比较的排序算法的另一个例子，冒泡排序比较列表中每对元素的顺序，如果它们是无序的，就交换它们，直到列表被排序。

#### 目测

[![bubble-sort](../Images/d8a80d7e92dac68478437c759bc9ef60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HigHGFxD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m4zwhvxf6ujdrvt9xoq5.gif)

#### 代码

```
function bubbleSort(list)
{
    let swapped
    let n = list.length-1
    do {
        swapped = false
        for (let i=0; i < n; i++)
        {
            // compare pairs of elements
            // if left element > right element, swap
            if (list[i] > list[i+1])
            {
               const temp = list[i]
               list[i] = list[i+1]
               list[i+1] = temp
               swapped = true
            }
        }
    } 
  // continue swapping until sorted
  while (swapped) 

  return list
}

const list = [4, 2, 3, 1, 5]

const sorted = bubbleSort(list)

console.log(sorted) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！😊而且，如果你想知道的话，我用这个网站制作了视觉效果。

在下一部分，我将介绍:

*   快速排序
*   堆排序
*   计数排序
# Javascript 排序算法(第 2 部分)

> 原文：<https://dev.to/wangonya/sorting-algorithms-with-javascript-part-2-3g51>

正如承诺的，这是帖子的第二部分。这里可以看第一部[。](https://dev.to/wangonya/sorting-algorithms-with-javascript-part-1-4aca)

我将展示另外三种排序算法的 Javascript 实现:

*   快速排序
*   堆排序
*   计数排序

同样，这并不是要深入解释算法的工作原理及其性能。如果你更想了解这些，我找到了一个不错的资源:[排序算法](https://brilliant.org/wiki/sorting-algorithms/)

为了简单起见，我将对一个只有 5 个元素的简单列表进行排序。

## 快速排序

像合并排序一样，该算法采用分治法。它的工作方式是选择一个轴心，并根据该轴心划分列表:所有大于轴心的元素位于轴心的右侧，所有小于或等于轴心的元素位于轴心的左侧。两边的元素都是快速排序的，这一过程一直重复，直到整个列表排序完毕。

#### 目测

这个视频并没有很清楚地说明算法实际上是如何工作的，所以这里有一个视频来代替。

#### 代码

```
function quickSort(list) {
    if (list.length <= 1) { 
        return list
    } else {
        const left = []
        const right = []
        const sorted = []
        const pivot = list.pop() // we're picking the last item to act as the pivot
        const length = list.length

        for (let i = 0; i < length; i++) {
            if (list[i] <= pivot) {
                left.push(list[i])
            } else {
                right.push(list[i])
            }
        }

        return sorted.concat(quickSort(left), pivot, quickSort(right))
    }
}

const list = [4, 2, 3, 1, 5]

const sorted = quickSort(list)

console.log(sorted) 
```

Enter fullscreen mode Exit fullscreen mode

## 堆排序

这种算法利用了一种叫做[二进制堆](https://www.cs.cmu.edu/~adamchik/15-121/lectures/Binary%20Heaps/heaps.html)的数据结构。堆排序的工作方式是创建一个最大堆，以升序对元素进行排序，然后用最后一个节点交换根节点，并在每次完成后从堆中删除排序后的节点。

#### 目测

[![heap-sort](img/fa45c760923b49edebd9a24c46e25897.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rsx1kRWY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/36sw7hiikhyv1mrwmz4g.gif)

#### 代码

```
// create max heap
function maxHeap(input, i) {
    const left = 2 * i + 1
    const right = 2 * i + 2
    let max = i

    if (left < arrLength && input[left] > input[max]) {
        max = left
    }

    if (right < arrLength && input[right] > input[max])     {
        max = right
    }

    if (max != i) {
        swap(input, i, max)
        maxHeap(input, max)
    }
}

function swap(input, indexA, indexB) {
    const temp = input[indexA]

    input[indexA] = input[indexB]
    input[indexB] = temp
}

function heapSort(input) {   
    arrLength = input.length

    for (let i = Math.floor(arrLength / 2); i >= 0; i -= 1)      {
        maxHeap(input, i)
      }

    for (i = input.length - 1; i > 0; i--) {
        swap(input, 0, i)
        arrLength--

        maxHeap(input, 0)
    }
    return
}

let arrLength

const list = [4, 2, 3, 1, 5]

const sorted = heapSort(list)

console.log(list) 
```

Enter fullscreen mode Exit fullscreen mode

## 计数排序

你会发现，与我们到目前为止讨论的算法相比，计数排序是相当独特的。这是因为它在排序时不比较元素。它基于数字键工作。它通过创建一个计数数组，然后用它来确定一个元素的正确位置。

#### 目测

[![counting-sort](img/d0d50a19d008b7c48c6e560688cb92ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FRDgdR3J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w4jk13diiokecdhny33z.gif)

#### 代码

```
function countingSort(list, min, max)
  {
      let i
      let z = 0
      const count = []

      for (i = min; i <= max; i++) {
          count[i] = 0
      }

      for (i=0; i < list.length; i++) {
          count[list[i]]++
      }

      for (i = min; i <= max; i++) {
          while (count[i]-- > 0) {
              list[z++] = i
          }
      }
      return list
  }

const list = [4, 2, 3, 1, 5]
const min = Math.min(...list)
const max = Math.max(...list)
const sorted = countingSort(list, min, max)

console.log(sorted) 
```

Enter fullscreen mode Exit fullscreen mode

编码快乐！

[![happy](img/f6e75394d8c1b8ad30c16a2317cd95f7.png)T2】](https://i.giphy.com/media/K3raI0cXTkzNC/giphy.gif)
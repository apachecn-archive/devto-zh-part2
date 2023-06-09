# 矮胖猴子算法挑战

> 原文：<https://dev.to/akonobrath/chunky-monkey-algorithm-challenge-ekg>

今天我能够解决实际上是一个基本的 Javascript 算法。对于那些对编码更有经验的人来说，这个算法很简单，但对我来说，这是一个有趣的挑战。在这篇文章中，我将尝试解释我(最终)解决这个问题的步骤。所以，系好安全带，和我一起踏上编码之旅吧。

[![enter image description here](img/e1850297ade7597b4c1b2294558b3ae2.png)T2】](https://i.giphy.com/media/VNhrzGZDa8mZy/giphy.gif)

矮胖猴子算法是 [FreeCodeCamp 前端 Web 开发认证](https://www.freecodecamp.org)的一部分。它要求我编写一个函数，将一个数组(第一个参数， **arr** )分成长度等于第二个参数( **size** )的组，然后将它们作为一个二维数组( **newArr** )返回。

请参见下面使用各种参数的预期输出:

**代码片段 1**

```
function chunkArrayInGroups(arr, size) {
  return newArr;
}
chunkArrayInGroups(["a", "b", "c", "d"], 2); 
// newArr = [["a", "b"], ["c", "d"]]

chunkArrayInGroups([0, 1, 2, 3, 4, 5], 3); 
// newArr = [[0, 1, 2], [3, 4, 5]]

chunkArrayInGroups([0, 1, 2, 3, 4, 5], 2); 
// newArr = [[0, 1], [2, 3], [4, 5]]

chunkArrayInGroups([0, 1, 2, 3, 4, 5], 4); 
// newArr = [[0, 1, 2, 3], [4, 5]]

chunkArrayInGroups([0, 1, 2, 3, 4, 5, 6], 3); 
// newArr = [[0, 1, 2], [3, 4, 5], [6]]

chunkArrayInGroups([0, 1, 2, 3, 4, 5, 6, 7, 8], 4); 
// newArr = [[0, 1, 2, 3], [4, 5, 6, 7], [8]]

chunkArrayInGroups([0, 1, 2, 3, 4, 5, 6, 7, 8], 2); 
// newArr = [[0, 1], [2, 3], [4, 5], [6, 7], [8]] 
```

我注意到的第一件事是有两类输出:

*   前三个函数调用都产生子数组，每个子数组包含相同数量的元素。
*   其他四个函数调用产生的子数组并不都具有相同数量的元素。

然而，所有函数调用的第一个子数组都有 length =**size**。这些观察给了我一个想法💡，也许函数参数之间有某种关系，我可以利用它来构造想要的输出。除了数据类型(这没有什么区别)之外， **arr** 唯一明显不同的属性是它的长度(arr.length)。当然**的尺寸**也因人而异。

为了找到这种关系，我决定写一个简单的函数，用**数组长度**除以**大小**，看看这些输出会产生什么:

**代码片段 2**

```
function test(arr, size){
    console.log(arr.length / size);
}
test(["a",  "b",  "c",  "d"],  2); //Output:  2
test([0,  1,  2,  3,  4,  5],  3); //Output: 2
test([0,  1,  2,  3,  4,  5],  2); //Output: 3
test([0,  1,  2,  3,  4,  5],  4); //Output: 1.5
test([0,  1,  2,  3,  4,  5,  6],  3); //Output: 2.33
test([0,  1,  2,  3,  4,  5,  6,  7,  8],  4); //Output: 2.25
test([0,  1,  2,  3,  4,  5,  6,  7,  8],  2); //Output: 4.5 
```

函数调用 1-3 都产生整数，其中输出表示在 **newArr** 中出现的子数组的数量，而 **size** 表示当调用**chunkarraygroups**时每个子数组中的元素数量(参见**代码片段 3** )。

**代码片段 3**

```
function chunkArrayInGroups(arr, size) {
  return newArr;
}

chunkArrayInGroups([0, 1, 2, 3, 4, 5], 3); 
// Output: [[0, 1, 2], [3, 4, 5]] // arr.length / size = 2
// 2 sub-arrays each containing 3 (size) elements

chunkArrayInGroups([0, 1, 2, 3, 4, 5], 2); 
//Output: [[0, 1], [2, 3], [4, 5]] // arr.length / size = 3
// 3 sub-arrays each containing 2 (size) elements 
```

函数调用 4-7 都产生了分数。我注意到的是，无论我需要创建什么函数，都必须创建尽可能多的包含**大小**个元素的子数组，然后将剩余的元素添加到最终的子数组中。对于 arr . length/size = floatin point 的函数调用，最终的子数组将包含一小部分**大小**数量的元素(**参见代码片段 4** )

**代码片段 4**

```
function chunkArrayInGroups(arr, size) {
  return newArr;
}

chunkArrayInGroups([0, 1, 2, 3, 4, 5], 4); 
//Output: [[0, 1, 2, 3], [4, 5]] // arr.length / size= 1.5
// 2 sub-arrays, one containing size number of elements.
// The other containing (0.5 * size) elements

chunkArrayInGroups([0, 1, 2, 3, 4, 5, 6], 3); 
//Output: [[0, 1, 2], [3, 4, 5], [6]] // arr.length / size = 2.33
// 3 sub-arrays, two containing size number of elements
// Final array containing (0.33 * size) elements 
```

有了这些线索，我开始构建和测试各种函数。我知道我必须使用 for 循环遍历 **arr** 。随着循环的每次迭代，我需要从 **arr** 中提取元素，然后将提取的元素添加到一个新的二维数组中。我可以通过使用[推送](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)和[切片](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)方法来实现这一点。迭代次数将决定 **newArr** 中子数组的数量。从我之前的实验中，我知道子数组的个数=(arr . length/size)；至少对于**代码片段 2** 中的前三个函数调用。

**代码片段 5**

```
function chunkArrayInGroups(arr, size){
    var newArr =  [];
    for(var i =  0; i < arr.length/size; i++){
        newArr.push(arr.slice(?, ?));
    }
return newArr;
} 
```

正如您在上面看到的，我需要确定 slice 方法的有效参数。第一个参数表示要传递到子数组中的第一个元素的索引。第二个参数表示子数组中切片到的元素的索引；该元素本身不包含在子数组中。

我决定对来自**代码片段 1** 的函数调用 1-3 进行逆向工程，以确定这些参数需要如何变化才能得到我想要的结果:

**代码片段 6**

```
function chunkArrayInGroups(arr, size){
    var newArr =  [];
    for(var i =  0; i < arr.length/size; i++){
        newArr.push(arr.slice(beginIndex,endIndex));
    }
return newArr;
}

//Function Call 1
chunkArrayInGroups(["a", "b", "c", "d"], 2); // [["a", "b"], ["c", "d"]]

//Function Call 2
chunkArrayInGroups([0, 1, 2, 3, 4, 5], 3); // Output: [[0, 1, 2], [3, 4, 5]]

//Function Call 3
chunkArrayInGroups([0, 1, 2, 3, 4, 5], 2); //Output: [[0, 1], [2, 3], [4, 5]] 
```

## **功能调用 1**

**size = 2**

| 循环迭代 | beginIndex | endIndex |
| --- | --- | --- |
| one | Zero | Two |
| Two | Two | four |

## **函数调用 2**

**size = 3**

| 循环迭代 | beginIndex | endIndex |
| --- | --- | --- |
| one | Zero | three |
| Two | three | six |

## **功能调用 3**

**size = 2**

| 循环迭代 | beginIndex | endIndex |
| --- | --- | --- |
| one | Zero | Two |
| Two | Two | four |
| three | four | six |

从上表可以得出两个结论:

1.  在每个 for 循环迭代期间， **beginIndex** 和 **endindex** 增加**大小**。

2.  **end index**=**begin index**+**size**

使用这些信息，我创建了一个变量， **count** ，它在 for 循环的每次迭代中以**大小**递增，并作为开始索引。基于以上结论中描述的关系， **endIndex** 因此变成**计数** + **大小**。

**代码片段 7**

```
function chunkArrayInGroups(arr, size){
    var newArr =  [];
    var count = 0;
    for(var i =  0; i < arr.length/size; i++){
        newArr.push(arr.slice(count,count + size));
        count = count + size;
    }
return newArr;
} 
```

* * *

下面这个功能管用！🎉🎉你甚至不必相信我的话，在类似 [replit](https://repl.it/repls) 或 [CodePen](https://.codepen.io) 这样的网站上试试吧🙏:

```
function chunkArrayInGroups(arr, size){
    var newArr =  [];
    var count = 0;
    for(var i =  0; i < arr.length/size; i++){
        newArr.push(arr.slice(count,count + size));
        count = count + size;
    }
return newArr;
} 
```

* * *

您可能已经注意到，这个函数也适用于最终输出的子数组与前面的子数组长度不同的函数调用。这对我来说实际上有点神秘，直到我解释了 for 循环的最后一次迭代在做什么。

**函数调用 5**
**size = 3**

**代码片段 8**

```
chunkArrayInGroups([0,  1,  2,  3,  4,  5,  6],  3);
//Output: [ [ 0, 1, 2 ], [ 3, 4, 5 ], [ 6 ] ] 
```

函数调用 5 的 for 循环的最终迭代

| 循环迭代 | beginIndex | endIndex |
| --- | --- | --- |
| three | six | nine |

for 循环的最后一次迭代提取索引为 6 的元素，直到但不包括索引为 9 的元素。在这种情况下， **arr** 不包含索引为 9 的元素。因此，slice 方法只是将所有剩余的元素提取到最终的子数组中。更多信息请参见 [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)。

原来如此！我们已经解决了矮胖猴子算法的挑战。🎆🎆我希望你喜欢这次旅行，并且学到了一些东西😉
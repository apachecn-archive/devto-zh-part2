# 让我们创建我们的映射方法

> 原文：<https://dev.to/machy44/lets-create-our-map-method-25j7>

如果你想进一步了解 JS 是如何工作的，以及我们是如何创建自己的过滤函数的，你可以看看我之前的帖子:

[![machy44 image](img/ed7881beba32b873b6e40d5a6749bf2f.png)](/machy44) [## 让我们在 JS 中创建我们自己的过滤方法

### machy 44 Feb 26 ' 183 分钟读取

#javascript #filter #webdev](/machy44/lets-create-our-own-filter-method-in-js--5gh4)

我认为这两篇文章是了解标准数组方法在 JS 中如何工作的好方法。

知道 Array.prototype.map()返回一个新数组意味着旧数组没有变异是非常重要的。(当您开始使用 React 和 Redux 库时，突变是一个非常重要的概念)。说得够多了，现在让我们编码吧。下面是一个简单的地图工作原理的例子。

```
var arr = [1,2,3];

var newArr = arr.map(function(element){
  return element + 1;
});

console.log('arr: ', arr);
// arr is not mutated and returns [1,2,3]

console.log('newArr: ', newArr );
//newArr returns [2,3,4] 
```

Enter fullscreen mode Exit fullscreen mode

#### 现在，让我们编写自己的地图函数

在这个例子中，我们不会检查第一个参数是数组还是第二个参数是回调函数，等等。地图功能的详细语法你可以在 developer.mozilla.org 上找到。

```
let arr = [1,2,3];

Array.prototype.ownMap = function(callback) {
  let mappedArr = [];
  for(let i=0; i < this.length; i++) {
    mappedArr.push(callback(this[i]))
  }
  return mappedArr;
}

let newArr = arr.ownMap(function(element) {
  return element + 1;
});

console.log('arr: ', arr);
// not mutated and returns [1, 2, 3]
console.log('newArr: ', newArr);
// returns [2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们来玩一下索引，因为索引可以作为 map 回调函数中的第二个参数发送。

```
let arr = [1,2,3,4];

Array.prototype.ownMap = function(callback) {
  let mappedArr = [];
  for(let i=0; i < this.length; i++) {
    mappedArr.push(callback(this[i], i)) // we defined i as second argument
  }
  return mappedArr;
}

let newArr = arr.ownMap(function(element, index) {
  return element + index;
});

console.log('arr: ', arr);
// not mutated and returns [1, 2, 3, 4]
console.log('newArr: ', newArr);
// returns [1, 3, 5, 7] 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

当你在学习的时候，最好知道事情是如何进行的。请让我知道，如果有什么我没有提到的，知道就好了。
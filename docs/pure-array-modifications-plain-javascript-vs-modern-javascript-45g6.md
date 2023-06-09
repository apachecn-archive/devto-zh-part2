# 纯数组修改:普通 JavaScript 与现代 JavaScript

> 原文：<https://dev.to/gsto/pure-array-modifications-plain-javascript-vs-modern-javascript-45g6>

帖子[纯数组修改:普通 JavaScript 与现代 JavaScript](https://glennstovall.com/pure-array-modificationsin-es2018-vs-es5/) 首先出现在[的格伦·斯托瓦尔——成长工程师](https://glennstovall.com)上。

当使用 Redux 或其他状态管理器时，您希望保持代码的纯净。这意味着没有副作用。您不是更新数组，而是希望返回一个应用了更改的数组的新副本。对于 JavaScript 数组来说，这很棘手，因为不清楚哪些本机函数改变了数组，哪些返回了新的副本。

**突击测验:** `array.slice()`和`array.splice()`。其中有多少影响了原数组？

a)切片是纯的，拼接不纯。

b)切片不纯，拼接纯。

c)两者都是纯的。

d)两者都不纯。

滚动查看结果:

...

...

...

a) Slice 是一个纯函数，但是 splice 改变了数组。

很困惑，对吧？

为了保证数组编辑的安全，让我们来看看一些对数组执行纯操作的函数。ES2018 和旧版本的 JavaScript 中都包含了示例。这些例子大量使用了 spread 操作符，所以如果您不熟悉它，这也可以帮助您更好地理解它是如何工作的。

## 1。如何在不修改原始元素的情况下将元素添加到数组末尾:

```
//ES 2018
const purePush = (arr, el) => [...arr,el]

//ES5
function purePushClassic(arr, el) {
  return arr.concat(el)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 2。如何在不修改原始元素的情况下将元素添加到数组的开头:

```
//ES 2018
const pureUnshift = (arr, el) => [el, ...arr]

//ES5
function pureUnshiftClassic(arr, el) {
  return [el].concat(arr)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 3。如何将两个数组合并成第三个数组

```
//ES2018
const pureCombine = (arr1, arr2) => [...arr1, ...arr2]

//ES5
function pureCombineClassic(arr1, arr2) {
  return arr1.concat(arr2)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 4。从数组末尾移除一个元素，而不修改原始元素:

```
//ES 2018
const pureShift = (arr) => arr.slice(1)

//ES5
function pureShiftClassic(arr){ return arr.slice(1) } 
```

Enter fullscreen mode Exit fullscreen mode

## 5。从数组的开头删除一个元素，而不修改原始元素:

```
//ES2018
const purePop = (arr) => arr.slice(0,-1)

//ES5
function purePopClassic(arr) { return arr.slice(0,-1) } 
```

Enter fullscreen mode Exit fullscreen mode

## 6。在不改变数组的情况下，在特定索引处插入元素:

```
//ES2018
const pureInsert = (arr, el, i) => [...arr.slice(0,i), el, ...arr.slice(i)]

//ES5
function pureInsertClassic(arr, el, i) {
return arr.slice(0,i)
  .concat(el)
  .concat(arr.slice(i++))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 7。在不改变数组的情况下替换特定索引处的元素:

```
//ES2018
const pureReplace = (arr, el, i) => [...arr.slice(0, i), el, ...arr.slice(++i)]

//ES5
function pureReplaceClassic(arr, el, i) {
  var copy = arr.slice()
  copy[i] = el
  return copy
} 
```

Enter fullscreen mode Exit fullscreen mode

## 8。删除特定索引处的元素，而不改变数组:

```
//ES2018
const pureDelete = (arr, i) => [...arr.slice(0,i),...arr.slice(i+1)]

//ES5
function pureDeleteClassic(arr, i) {
  return arr.slice(0,i).concat(arr.slice(i+1))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 额外提示:将任何不纯的数组方法变为纯数组方法

你所要做的就是首先复制一个数组。你在上面的例子中已经看到了。在 ES2018 中，您可以使用扩展运算符。在旧的 JavaScript 中，可以使用 splice。例如，这里有一种将逆向方法转换为纯操作的方法:

## 想了解更多？这是一些进一步的阅读材料

*   [MDN 网络文档展开语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
*   [array . prototype . slice 上的 MDN Web docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
*   [为什么 Redux reducers 需要是纯函数](https://medium.freecodecamp.org/why-redux-needs-reducers-to-be-pure-functions-d438c58ae468)
*   或者，通过在 Dev.to 上关注我或订阅我的[周信](https://glennstovall.com/newsletter/?utm_campaign=dev.to)来了解最新的 JavaScript 教程。
# 什么是 Redux 减速器？

> 原文：<https://dev.to/dceddia/what-is-a-redux-reducer-1mbo>

> **reducer** ，*n .*——一个杜撰出来迷惑你的词 Redux。

为了编写 Redux 代码，您需要了解一些事情。其中之一就是**减速器**是什么以及它的作用。这可能看起来有点可怕和奇怪，但是在这篇短文之后，我想你会同意，正如俗话所说，“只是一个函数。”

首先,“减速器”这个名字从何而来？Redux 其实也不是编的(这个我是开玩笑的)。如果你熟悉函数式编程和 JavaScript 的`Array.reduce`函数，它可能不会显得太陌生。如果你了解`Array.reduce`，你就会知道它需要一个带有签名`(accumulatedValue, nextItem) => nextAccumulatedValue`的函数(有人可能称之为“缩减器”函数)。

## Array.reduce 就像是 Redux 的姐妹

如果你还不熟悉`Array.reduce`，这里有一些事情:

JavaScript 的数组有一个名为`reduce`的内置函数。

(*技术上来说*我应该把它写成`Array.prototype.reduce`，因为它是在*数组实例*上的函数，而不是在 capital-A `Array`构造函数上。)

它将一个函数作为参数，并为数组的每个元素调用一次您提供的函数，类似于`Array.map`的工作方式(或者一个`for`循环)。调用函数时有两个参数:最后一次迭代的结果和当前数组元素。举个例子会更有意义:

```
var letters = ['r', 'e', 'd', 'u', 'x'];

// `reduce` takes 2 arguments:
// - a function to do the reducing (you might say, a "reducer")
// - an initial value for accumulatedResult
var word = letters.reduce(
  function(accumulatedResult, arrayItem) {
    return accumulatedResult + arrayItem;
  },
''); // <-- notice this empty string argument: it's the initial value

console.log(word) // => "redux" 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，reducer 将被调用 5 次(因为数组中有 5 个元素)。电话是这样的:

*   首先调用`('', 'r')` = >返回`'r'`
    *   空字符串`''`来自`reduce`的第二个参数，`'r'`是数组的第一个元素
*   然后`('r', 'e')` = >返回`'re'`
    *   “r”来自前一个返回值，“e”是数组的下一个元素
*   然后`('re', 'd')` = >返回`'red'`
    *   “re”是前一个返回值，“d”是第三个数组元素
*   然后`('red', 'u')` = >返回`'redu'`
    *   现在你已经感觉到了一种模式
*   然后`('redu', 'x')` = >返回`'redux'`
    *   这种模式现在再清楚不过了

最后一个返回值`'redux'`作为最终结果返回，并存储在`word`变量中。

## Redux 还原器

现在你知道了`Array.reduce`是如何工作的，我可以告诉你 Redux 是*基本上是*一个奇特的 Array.reduce 函数(好吧好吧，这是一个巨大的简化，但是请原谅我)。

Redux reducer 函数具有以下特征:

`(state, action) => newState`

比如:它接受当前的`state`和一个`action`，并返回`newState`。看起来很像数组的签名。reduce reducer，嗯？请记住:

`(accumulatedValue, nextItem) => nextAccumulatedValue`

简单地说，Redux reducer 决定每个动作如何影响状态。我们来看一个例子一:

```
function wordReducer(state = '', action) {
  switch(action.type) {
    case 'ADD_LETTER':
      return state + action.letter;
    case 'RESET':
      return '';
    default:
      return state;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

快速测验:这里有 Redux 特有的代码吗？任何依赖 Redux 库工作的东西？去吧，好好想想，我等着。

…

…

…

回答:没有！这是一个普通的旧函数。当然，它接受`(state, action)`参数并返回一个新状态。它希望`action`看起来有点像`{type: 'ADD_LETTER', letter: 'r'}`。但是这些都不是与 Redux 特别相关的。

### 工作原理

但不管怎样，它实际上是做什么的？让我们试着用几个东西调用它，看看它返回什么。

```
let state = '';
console.log(wordReducer(state, {type: 'ADD_LETTER', letter: 'y'}));
  // => y
console.log(wordReducer(state, {type: 'ADD_LETTER', letter: 'y'}));
  // => y
console.log(wordReducer(state, {type: 'ADD_LETTER', letter: 'y'}));
  // => y 
```

Enter fullscreen mode Exit fullscreen mode

首先:注意到`wordReducer`不记得*任何事情。它没有内在的状态。* 

```
let state = '';
console.log(wordReducer(state, {type: 'ADD_LETTER', letter: 'a'}));
  // => a
console.log(wordReducer(state, {type: 'ADD_LETTER', letter: 'b'}));
  // => b
console.log(state)
  // => '' 
```

Enter fullscreen mode Exit fullscreen mode

接下来:注意`wordReducer`没有*改变*的状态。它只是返回一个新的。它将状态视为不可变的 T4。这很重要，因为通过以不可变的方式更新状态，Redux 能够判断哪些状态发生了变化，并优化应用程序的重新呈现方式。

还有一件事:

```
console.log(wordReducer(undefined, {type: 'UNHANDLED'}));
  // => ''
console.log(wordReducer('existing state', {type: 'UNHANDLED'}));
  // => 'existing state' 
```

Enter fullscreen mode Exit fullscreen mode

注意，reducer 有一个初始状态(当给定`undefined`时，它无论如何都会返回一个空字符串)，并且它有一个`default` case 来处理它不理解的任何动作(当它看到这样的动作时，它会返回现有的状态，不变)。

### 需要飞行员

我可以告诉你不认为这是非常有用的。一个什么都不记得，什么都不改变的函数有什么用？

我告诉你:这个函数很好，因为它是*可预测的*。如果你用相同的参数调用它，每次都会得到相同的输出。无论你的应用程序发生了什么变化，这个功能都是一样的。

通过阅读它的代码很容易就能知道它做了什么(并且很容易调试！)因为都是自带的。

现在，像这样的函数的缺点是它需要各种各样的驱动程序。有些东西需要保持中间状态，否则应用程序不会真正做很多事情。

在这种情况下，驱动程序是 Redux。具体来说就是 Redux 店。它是这样做的:

```
let state = '';
state = wordReducer(state, {type: 'ADD_LETTER', letter: 'a'}));
state = wordReducer(state, {type: 'ADD_LETTER', letter: 'b'}));
state = wordReducer(state, {type: 'ADD_LETTER', letter: 'c'})); 
```

Enter fullscreen mode Exit fullscreen mode

商店维护一个内部的`state`变量。当一个动作被分派时，存储调用 reducer，并用 reducer 返回的内容替换它的内部`state`。每次存储调用 reducer 时，它都以最后已知的状态传递。

周而复始:Redux 坐在那里等待一个动作，处理那个动作，更新状态，重新渲染你的应用程序，等等。

原来如此！简而言之，Redux reducers 就是这样工作的。不算太坏？

[什么是 Redux 减速器？](https://daveceddia.com/what-is-a-reducer/)最初由戴夫·塞迪亚于 2018 年 3 月 19 日在[戴夫·塞迪亚](https://daveceddia.com)发布。

[代码项目](http://www.codeproject.com)
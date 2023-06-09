# 函数式编程:IF # Functional # JavaScript #函子的替代方案

> 原文：<https://dev.to/joelnet/functional-programming-how-would-you-if-no-else-javascript-59ai>

有几次我被问到“在函数式编程中，你会怎么做 X？”我绝对喜欢这类问题。

我尽我所能回答每个问题，但我认为有许多问题足够有趣，值得他们自己的文章。

因此，在这篇文章中，我想展示如何以一种更实用的方式重新创建这个命令式函数。

这个函数有一个没有`else`的`if`语句。因此，虽然一个`ternary`操作符可以工作，但并不理想。

```
// A basic redux-thunk action that only dispatches when value exists
const someAction = value => dispatch => {
  const item = getItem(value)
  if (item != null) {
    dispatch({ type: 'ACTION', item })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们只需要在有`value`的时候运行`dispatch`，否则我们什么都不做。

一种选择是使用短路运算符:

```
// short circuit
const someAction = value => dispatch => {
  const item = getItem(value)
  item && dispatch({ type: 'ACTION', item })
}

// ternary
const someAction = value => dispatch => {
  const item = getItem(value)
  item ? dispatch({ type: 'ACTION', item }) : null
} 
```

Enter fullscreen mode Exit fullscreen mode

短路和三进制，这两种方法都可以解决这个问题，但是让我们再看几个。

# 方案一:ifVal 辅助函数

为了用一种更实用的方式来做这件事，我将创建一个助手函数。我会先写这个旧学校的帮助者，然后一点一点地把它浓缩，这样每个人都能容易地理解它。

```
// 1: old school
function ifVal (x, f) {
  if (x == null) {
    return null
  } else {
    return f(x)
  }
}

// 2: convert to arrow function
const ifVal = (x, f) => {
  if (x == null) {
    return null
  } else {
    return f(x)
  }
}

// 3: convert if/else to a ternary operator
const ifVal = (x, f) => {
  return x == null ? null : f(x)
}

// 4: voilà!
const ifVal = (x, f) => x == null ? null : f(x) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以修改我们的`someAction`函数，使用`ifVal`代替传统的`if`块。

```
// functional alternative
const someAction = value => dispatch =>
  ifVal(getItem(value), item => dispatch({ type: 'ACTION', item })) 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个简单快捷的 twitter 截图对比:

```
/**
 * execute the function if the value is not null or undefined
 * @param {Object} val - the value to test
 * @param {Function} fn - the function to execute.
 * @returns {Object} - null or the value of the executed function.
 */
const ifVal = (val, fn) => val == null ? null : fn(val)

// imperative example
const someAction = value => dispatch => {
  const item = getItem(value)
  if (item!= null) {
    dispatch({ type: 'ACTION', item })
  }
}

// functional example
const someAction = value => dispatch =>
  ifVal(getItem(value), item => dispatch({ type: 'ACTION', item })) 
```

Enter fullscreen mode Exit fullscreen mode

## 进一步阅读

看看拉姆达的[当](http://ramdajs.com/docs/#when)、[除非](http://ramdajs.com/docs/#unless)、 [ifelse](http://ramdajs.com/docs/#ifElse) 其他有用的类似函数。

# 解 B:函子

我们也可以使用**也许是**型。Maybe 类型包含一个保存值的`Just`类型或一个确切表示值的`Nothing`类型。

对于这个例子，我将使用来自 [Sanctuary](https://sanctuary.js.org) 库的 Maybe 类型。

看起来有点像这样:

```
/* Examples of Sanctuary's Maybe */

toMaybe(null) //=> Nothing
toMaybe(undefined) //=> Nothing
toMaybe(0) //=> Just(0)
toMaybe(false) //=> Just(false)
toMaybe(123) //=> Just(123)
toMaybe({ name: 'joel' }) //=> Just({ name: 'joel' }) 
```

Enter fullscreen mode Exit fullscreen mode

也许类型非常简单，我之所以想用它是因为`map`和`Maybe`配合得非常好，它被赋予了一个特殊的名字，一个**仿函数**。

一些库，如 [ramda-fantasy](https://github.com/ramda/ramda-fantasy) 有流畅的语法，但本文使用的是 Sanctuary。不过，很高兴知道这两者是等价的。

```
const double = x => x * 2

// ramda-fantasy
Maybe.toMaybe(333).map(double) //=> Just(666)

// Sanctuary
map(double, toMaybe(333)) //=> Just(666) 
```

Enter fullscreen mode Exit fullscreen mode

例子:

```
const double = x => x * 2

// map is ignored
map(double, toMaybe(null)) //=> Nothing

// no more null exceptions!
map(val => val.something(), toMaybe(null)) //=> Nothing

// map is mapped
map(double, toMaybe(333)) //=> Just(666) 
```

Enter fullscreen mode Exit fullscreen mode

所有加在一起的`doSomething`也许会是这样的:

```
import { toMaybe, map } from 'sanctuary'

const someAction = value => dispatch => {
  const item = getItem(value)
  const maybeValue = toMaybe(item)
  map(item => dispatch({ type: 'ACTION', item }))(maybeValue)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有三个函数，`getItem`、`toMaybe`和`map`，它们可以组合成一个新的函数。我们可以将值传递给这个新函数，它将首先流入`getItem`，然后是`toMaybe`，最后这个值将流入我们的`map`。

```
const someAction = value => dispatch => pipe([
  getItem,
  toMaybe,
  map(value => dispatch({ type: 'ACTION', value })),
], value) 
```

Enter fullscreen mode Exit fullscreen mode

最后一张完美的推文:

```
import { toMaybe, map, pipe } from 'sanctuary'

// imperative example
const someAction = value => dispatch => {
  const item = getItem(value)
  if (item != null) {
    dispatch({ type: 'ACTION', item })
  }
}

// maybe functor example
const someAction = value => dispatch => pipe([
  getItem,
  toMaybe,
  map(value => dispatch({ type: 'ACTION', value })),
], value)

someAction(666)(dispatch) // Action is dispatched!
someAction(null)(dispatch) // Nothing happens 
```

Enter fullscreen mode Exit fullscreen mode

# 结束

我希望你喜欢这个小小的功能之旅。如果你这样做了，如果你迷路了，如果你有任何问题，或者如果你想要更多像这样的文章，请在评论中告诉我。如果你真的想激励我，请在这里关注我，或者在推特上关注我。如果你**喜欢这篇文章，请传阅！**

干杯！
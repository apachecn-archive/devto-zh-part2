# 如何构建计算器—第 3 部分

> 原文：<https://dev.to/zellwk/how-to-build-a-calculatorpart-3-3cng>

在前两节课中，您学习了如何构建一个强大的计算器。不幸的是，我们一起创造的代码令人困惑。如果你试图再读一遍代码，你肯定会迷路。

这就是为什么我们需要重构。在本课中，您将学习如何使用一些 JavaScript 最佳实践来重构计算器。

## 先决条件

在开始本课之前，请确保您已经完成了前两节课。它们的链接如下:

1.  [第一部分——快乐之路](https://zellwk.com/blog/calculator-part-1)
2.  [第 2 部分—边缘案例](https://zellwk.com/blog/calculator-part-2)

您还需要了解一些半高级的 Javascript 实践:

1.  [提前回报](http://blog.timoxley.com/post/47041269194/avoid-else-return-early)
2.  [三元运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)
3.  [纯函数](https://medium.com/@jamesjefferyuk/javascript-what-are-pure-functions-4d4d5392d49c)
4.  [ES6 破坏](https://zellwk.com/blog/es6#destructuring)

就这样，我们开始吧！

## 重构计算器

当你重构时，你通常会从最明显的改进开始。既然这样，那就从`calculate`说起吧。

## 重构计算函数

这是我们目前掌握的情况。

```
const calculate = (n1, operator, n2) => {
  let result = ''
  if (operator === 'add') {
    result = firstNum + parseFloat(n2)
  } else if (operator === 'subtract') {
    result = parseFloat(n1) - parseFloat(n2)
  } else if (operator === 'multiply') {
    result = parseFloat(n1) * parseFloat(n2)
  } else if (operator === 'divide') {
    result = parseFloat(n1) / parseFloat(n2)
  }

  return result
} 
```

Enter fullscreen mode Exit fullscreen mode

你知道我们应该尽可能减少重新分配。在这里，如果我们在`if`和`else if`语句:
中返回计算结果，我们可以删除赋值

```
const calculate = (n1, operator, n2) => {
  if (operator === 'add') {
    return firstNum + parseFloat(n2)
  } else if (operator === 'subtract') {
    return parseFloat(n1) - parseFloat(n2)
  } else if (operator === 'multiply') {
    return parseFloat(n1) * parseFloat(n2)
  } else if (operator === 'divide') {
    return parseFloat(n1) / parseFloat(n2)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们返回所有的值，所以我们可以使用**早期返回**。如果我们这样做，就不需要任何条件。

```
const calculate = (n1, operator, n2) => {
  if (operator === 'add') {
    return firstNum + parseFloat(n2)
  }

  if (operator === 'subtract') {
    return parseFloat(n1) - parseFloat(n2)
  }

  if (operator === 'multiply') {
    return parseFloat(n1) * parseFloat(n2)
  }

  if (operator === 'divide') {
    return parseFloat(n1) / parseFloat(n2)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

由于每个`if`条件有一个语句，我们可以去掉括号。(注意:一些开发人员用大括号发誓)。下面是代码的样子:

```
const calculate = (n1, operator, n2) => {
  if (operator === 'add') return parseFloat(n1) + parseFloat(n2)
  if (operator === 'subtract') return parseFloat(n1) - parseFloat(n2)
  if (operator === 'multiply') return parseFloat(n1) * parseFloat(n2)
  if (operator === 'divide') return parseFloat(n1) / parseFloat(n2)
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们在函数中调用了`parseFloat`八次。我们可以通过创建两个变量来包含浮点值来简化它:

```
const calculate = (n1, operator, n2) => {
  const firstNum = parseFloat(n1)
  const secondNum = parseFloat(n2)
  if (operator === 'add') return firstNum + secondNum
  if (operator === 'subtract') return firstNum - secondNum
  if (operator === 'multiply') return firstNum * secondNum
  if (operator === 'divide') return firstNum / secondNum
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在完成了`calculate`。你不觉得比以前更好读了吗？

## 重构事件监听器

我们为事件监听器创建的代码非常庞大。以下是我们目前拥有的:

```
keys.addEventListener('click', e => {
  if (e.target.matches('button')) {

    if (!action) { /* ... */ }

    if (action === 'add' ||
      action === 'subtract' ||
      action === 'multiply' ||
      action === 'divide') {
      /* ... */
    }

    if (action === 'clear') { /* ... */ }
    if (action !== 'clear') { /* ... */ }
    if (action === 'calculate') { /* ... */ }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

你如何开始重构这段代码？如果你不知道任何编程的最佳实践，你可能会尝试通过将每一种动作分解成一个更小的函数来进行重构

```
// Don't do this!
const handleNumberKeys = (/* ... */) => {/* ... */}
const handleOperatorKeys = (/* ... */) => {/* ... */}
const handleDecimalKey = (/* ... */) => {/* ... */}
const handleClearKey = (/* ... */) => {/* ... */}
const handleCalculateKey = (/* ... */) => {/* ... */} 
```

Enter fullscreen mode Exit fullscreen mode

不要这样。它没有帮助，因为你仅仅是分割代码块。当你这样做的时候，函数变得难以理解。

更好的方法是将代码分成纯函数和不纯函数。如果您这样做，您将得到如下所示代码:

```
keys.addEventListener('click', e => {
  // Pure function
  const resultString = createResultString(/* ... */)

  // Impure stuff
  display.textContent = resultString
  updateCalculatorState(/* ... */)
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里，`createResultString`是一个纯函数，返回需要在计算器上显示的内容。`updateCalculatorState`是一个不纯的函数，改变了计算器的视觉外观和自定义属性。

## 制作 createResultString

如前所述，`createResultString`应该返回需要在计算器上显示的值。
你可以通过代码中写着`display.textContent = 'some value`的部分得到这些值。

```
display.textContent = 'some value' 
```

Enter fullscreen mode Exit fullscreen mode

我们希望返回每个值，而不是`display.textContent = 'some value'`，以便以后使用。

```
// replace the above with this
return 'some value' 
```

Enter fullscreen mode Exit fullscreen mode

让我们一起从数字键开始，一步一步来。

### 制作数字键的结果字符串

这是我们数字键的代码:

```
if (!action) {
  if (
    displayedNum === '0' ||
    previousKeyType === 'operator' ||
    previousKeyType === 'calculate'
  ) {
    display.textContent = keyContent
  } else {
    display.textContent = displayedNum + keyContent
  }
  calculator.dataset.previousKeyType = 'number'
} 
```

Enter fullscreen mode Exit fullscreen mode

第一步，把写着`display.textContent = 'some value'`的部分复制到`createResultString`里。当你这样做的时候，确保你把`display.textContent =`变成了`return`。

```
const createResultString = () => {
  if (!action) {
    if (
      displayedNum === '0' ||
      previousKeyType === 'operator' ||
      previousKeyType === 'calculate'
    ) {
      return keyContent
    } else {
      return displayedNum + keyContent
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以将`if/else`语句转换为三元运算符:

```
const createResultString = () => {
  if (action!) {
    return displayedNum === '0' ||
      previousKeyType === 'operator' ||
      previousKeyType === 'calculate'
      ? keyContent
      : displayedNum + keyContent
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当你重构时，记得记下你需要的变量列表。我们稍后将回到这个列表。

```
const createResultString = () => {
  // Variables required are:
  // 1\. keyContent
  // 2\. displayedNum
  // 3\. previousKeyType
  // 4\. action

  if (action!) {
    return displayedNum === '0' ||
      previousKeyType === 'operator' ||
      previousKeyType === 'calculate'
      ? keyContent
      : displayedNum + keyContent
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 制作结果字符串为十进制键

这是十进制键的代码:

```
if (action === 'decimal') {
  if (!displayedNum.includes('.')) {
    display.textContent = displayedNum + '.'
  } else if (
    previousKeyType === 'operator' ||
    previousKeyType === 'calculate'
  ) {
    display.textContent = '0.'
  }

  calculator.dataset.previousKeyType = 'decimal'
} 
```

Enter fullscreen mode Exit fullscreen mode

和以前一样，我们想把任何改变`display.textContent`的东西移到`createResultString`中。

```
const createResultString = () => {
  // ...

  if (action === 'decimal') {
    if (!displayedNum.includes('.')) {
      return = displayedNum + '.'
    } else if (previousKeyType === 'operator' || previousKeyType === 'calculate') {
      return = '0.'
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

由于我们想要返回所有的值，我们可以将`else if`语句转换成早期返回。

```
const createResultString = () => {
  // ...

  if (action === 'decimal') {
    if (!displayedNum.includes('.')) return displayedNum + '.'
    if (previousKeyType === 'operator' || previousKeyType === 'calculate') return '0.'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里一个常见的错误是当两个条件都不匹配时忘记返回当前显示的数字。我们需要这样做，因为我们将用从`createResultString`返回的值替换`display.textContent`。如果我们错过了，`createResultString`将返回`undefined`，这不是我们所希望的。

```
const createResultString = () => {
  // ...

  if (action === 'decimal') {
    if (!displayedNum.includes('.')) return displayedNum + '.'
    if (previousKeyType === 'operator' || previousKeyType === 'calculate') return '0.'
    return displayedNum
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

像往常一样，记下需要的变量。此时，所需的变量仍然和以前一样:

```
const createResultString = () => {
  // Variables required are:
  // 1\. keyContent
  // 2\. displayedNum
  // 3\. previousKeyType
  // 4\. action
} 
```

Enter fullscreen mode Exit fullscreen mode

### 为操作键制作结果字符串

这是我们为操作键编写的代码。

```
if (
  action === 'add' ||
  action === 'subtract' ||
  action === 'multiply' ||
  action === 'divide'
) {
  const firstValue = calculator.dataset.firstValue
  const operator = calculator.dataset.operator
  const secondValue = displayedNum

  if (
    firstValue &&
    operator &&
    previousKeyType !== 'operator' &&
    previousKeyType !== 'calculate'
  ) {
    const calcValue = calculate(firstValue, operator, secondValue)
    display.textContent = calcValue
    calculator.dataset.firstValue = calcValue
  } else {
    calculator.dataset.firstValue = displayedNum
  }

  key.classList.add('is-depressed')
  calculator.dataset.previousKeyType = 'operator'
  calculator.dataset.operator = action
} 
```

Enter fullscreen mode Exit fullscreen mode

你现在知道该怎么做了；我们想把所有改变`display.textContent`的东西都移到`createResultString`里。这里是需要移动的:

```
const createResultString = () => {
  // ...
  if (
    action === 'add' ||
    action === 'subtract' ||
    action === 'multiply' ||
    action === 'divide'
  ) {
    const firstValue = calculator.dataset.firstValue
    const operator = calculator.dataset.operator
    const secondValue = displayedNum

    if (
      firstValue &&
      operator &&
      previousKeyType !== 'operator' &&
      previousKeyType !== 'calculate'
    ) {
      return calculate(firstValue, operator, secondValue)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

记住，`createResultString`需要返回要在计算器上显示的值。如果`if`条件不匹配，我们仍然希望返回显示的数字。

```
const createResultString = () => {
  // ...
  if (
    action === 'add' ||
    action === 'subtract' ||
    action === 'multiply' ||
    action === 'divide'
  ) {
    const firstValue = calculator.dataset.firstValue
    const operator = calculator.dataset.operator
    const secondValue = displayedNum

    if (
      firstValue &&
      operator &&
      previousKeyType !== 'operator' &&
      previousKeyType !== 'calculate'
    ) {
      return calculate(firstValue, operator, secondValue)
    } else {
      return displayedNum
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以将`if/else`语句重构为一个三元运算符:

```
const createResultString = () => {
  // ...
  if (
    action === 'add' ||
    action === 'subtract' ||
    action === 'multiply' ||
    action === 'divide'
  ) {
    const firstValue = calculator.dataset.firstValue
    const operator = calculator.dataset.operator
    const secondValue = displayedNum

    return firstValue &&
      operator &&
      previousKeyType !== 'operator' &&
      previousKeyType !== 'calculate'
      ? calculate(firstValue, operator, secondValue)
      : displayedNum
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你仔细观察，你会发现没有必要存储一个`secondValue`变量。我们可以在`calculate`函数中直接使用`displayedNum`。

```
const createResultString = () => {
  // ...
  if (
    action === 'add' ||
    action === 'subtract' ||
    action === 'multiply' ||
    action === 'divide'
  ) {
    const firstValue = calculator.dataset.firstValue
    const operator = calculator.dataset.operator

    return firstValue &&
      operator &&
      previousKeyType !== 'operator' &&
      previousKeyType !== 'calculate'
      ? calculate(firstValue, operator, displayedNum)
      : displayedNum
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，记下所需的变量和属性。这一次我们需要`calculator.dataset.firstValue`和`calculator.dataset.operator`。

```
const createResultString = () => {
  // Variables & properties required are:
  // 1\. keyContent
  // 2\. displayedNum
  // 3\. previousKeyType
  // 4\. action
  // 5\. calculator.dataset.firstValue
  // 6\. calculator.dataset.operator
} 
```

Enter fullscreen mode Exit fullscreen mode

### 为清除键制作结果字符串

我们编写了以下代码来处理`clear`键。

```
if (action === 'clear') {
  if (key.textContent === 'AC') {
    calculator.dataset.firstValue = ''
    calculator.dataset.modValue = ''
    calculator.dataset.operator = ''
    calculator.dataset.previousKeyType = ''
  } else {
    key.textContent = 'AC'
  }

  display.textContent = 0
  calculator.dataset.previousKeyType = 'clear'
} 
```

Enter fullscreen mode Exit fullscreen mode

如上，想把所有改变`display.textContent`的东西都移到`createResultString`里。

```
const createResultString = () => {
  // ...
  if (action === 'clear') return 0
} 
```

Enter fullscreen mode Exit fullscreen mode

### 为等号键制作结果字符串

下面是我们为等号键编写的代码:

```
if (action === 'calculate') {
  let firstValue = calculator.dataset.firstValue
  const operator = calculator.dataset.operator
  let secondValue = displayedNum

  if (firstValue) {
    if (previousKeyType === 'calculate') {
      firstValue = displayedNum
      secondValue = calculator.dataset.modValue
    }

    display.textContent = calculate(firstValue, operator, secondValue)
  }

  calculator.dataset.modValue = secondValue
  calculator.dataset.previousKeyType = 'calculate'
} 
```

Enter fullscreen mode Exit fullscreen mode

如上所述，我们希望将所有将`display.textContent`更改为`createResultString`的内容复制过来。下面是需要复制的:

```
if (action === 'calculate') {
  let firstValue = calculator.dataset.firstValue
  const operator = calculator.dataset.operator
  let secondValue = displayedNum

  if (firstValue) {
    if (previousKeyType === 'calculate') {
      firstValue = displayedNum
      secondValue = calculator.dataset.modValue
    }
    display.textContent = calculate(firstValue, operator, secondValue)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当将代码复制到`createResultString`中时，确保为每个可能的场景返回值:

```
const createResultString = () => {
  // ...

  if (action === 'calculate') {
    let firstValue = calculator.dataset.firstValue
    const operator = calculator.dataset.operator
    let secondValue = displayedNum

    if (firstValue) {
      if (previousKeyType === 'calculate') {
        firstValue = displayedNum
        secondValue = calculator.dataset.modValue
      }
      return calculate(firstValue, operator, secondValue)
    } else {
      return displayedNum
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们希望减少重新分配。我们可以通过三元运算符将正确的值传递给`calculate`来实现。

```
const createResultString = () => {
  // ...

  if (action === 'calculate') {
    const firstValue = calculator.dataset.firstValue
    const operator = calculator.dataset.operator
    const modValue = calculator.dataset.modValue

    if (firstValue) {
      return previousKeyType === 'calculate'
        ? calculate(displayedNum, operator, modValue)
        : calculate(firstValue, operator, displayedNum)
    } else {
      return displayedNum
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您觉得合适，可以用另一个三元运算符进一步简化上面的代码:

```
const createResultString = () => {
  // ...

  if (action === 'calculate') {
    const firstValue = calculator.dataset.firstValue
    const operator = calculator.dataset.operator
    const modValue = calculator.dataset.modValue

    return firstValue
      ? previousKeyType === 'calculate'
        ? calculate(displayedNum, operator, modValue)
        : calculate(firstValue, operator, displayedNum)
      : displayedNum
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们要再次注意所需的属性和变量:

```
const createResultString = () => {
  // Variables & properties required are:
  // 1\. keyContent
  // 2\. displayedNum
  // 3\. previousKeyType
  // 4\. action
  // 5\. calculator.dataset.firstValue
  // 6\. calculator.dataset.operator
  // 7\. calculator.dataset.modValue
} 
```

Enter fullscreen mode Exit fullscreen mode

### 传入必要的变量

我们在`createResultString`中需要七个属性/变量:

1.  `keyContent`
2.  `displayedNum`
3.  `previousKeyType`
4.  `action`
5.  `firstValue`
6.  `modValue`
7.  `operator`

我们可以从`key`那里得到`keyContent`和`action`。我们还可以从`calculator.dataset`中得到`firstValue`、`modValue`、`operator`和`previousKeyType`。

这意味着`createResultString`函数需要三个变量——`key`、`displayedNum`和`calculator.dataset`。因为`calculator.dataset`代表计算器的状态，所以让我们用一个叫做`state`的变量来代替。

```
const createResultString = (key, displayedNum, state) => {
  const keyContent = key.textContent
  const action = key.dataset.action
  const firstValue = state.firstValue
  const modValue = state.modValue
  const operator = state.operator
  const previousKeyType = state.previousKeyType
  // ... Refactor as necessary
}

// Using createResultString
keys.addEventListener('click', e => {
  if (e.target.matches('button')) return
  const displayedNum = display.textContent
  const resultString = createResultString(e.target, displayedNum, calculator.dataset)

  // ...
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你愿意的话，可以随意地析构变量:

```
const createResultString = (key, displayedNum, state) => {
  const keyContent = key.textContent
  const { action } = key.dataset
  const {
    firstValue,
    modValue,
    operator,
    previousKeyType
  } = state

  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

### if 语句内的一致性

在`createResultString`中，我们使用以下条件来测试被点击的键的类型:

```
// If key is number
if (!action) { /* ... */ }

// If key is decimal
if (action === 'decimal') { /* ... */ }

// If key is operator
if (
  action === 'add' ||
  action === 'subtract' ||
  action === 'multiply' ||
  action === 'divide'
) { /* ... */}

// If key is clear
if (action === 'clear') { /* ... */ }

// If key is calculate
if (action === 'calculate') { /* ... */ } 
```

Enter fullscreen mode Exit fullscreen mode

它们不一致，所以很难读懂。如果可能的话，我们想让它们保持一致，这样我们就可以这样写:

```
if (keyType === 'number') { /* ... */ }
if (keyType === 'decimal') { /* ... */ }
if (keyType === 'operator') { /* ... */}
if (keyType === 'clear') { /* ... */ }
if (keyType === 'calculate') { /* ... */ } 
```

Enter fullscreen mode Exit fullscreen mode

为此，我们可以创建一个名为`getKeyType`的函数。这个函数应该返回被点击的键的类型。

```
const getKeyType = (key) => {
  const { action } = key.dataset
  if (!action) return 'number'
  if (
    action === 'add' ||
    action === 'subtract' ||
    action === 'multiply' ||
    action === 'divide'
  ) return 'operator'
  // For everything else, return the action
  return action
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是该函数的使用方法:

```
const createResultString = (key, displayedNum, state) => {
  const keyType = getKeyType(key)

  if (keyType === 'number') { /* ... */ }
  if (keyType === 'decimal') { /* ... */ }
  if (keyType === 'operator') { /* ... */}
  if (keyType === 'clear') { /* ... */ }
  if (keyType === 'calculate') { /* ... */ }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们完成了`createResultString`。接下来说`updateCalculatorState`。

## 制作更新计算器状态

`updateCalculatorState`是一个改变计算器视觉外观和自定义属性的功能。

和`createResultString`一样，我们需要检查被点击的键的类型。在这里，我们可以重用`getKeyType`。

```
const updateCalculatorState = (key) => {
  const keyType = getKeyType(key)

  if (keyType === 'number') { /* ... */ }
  if (keyType === 'decimal') { /* ... */ }
  if (keyType === 'operator') { /* ... */}
  if (keyType === 'clear') { /* ... */ }
  if (keyType === 'calculate') { /* ... */ }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你看看剩下的代码，你可能会注意到我们为每种类型的键改变了`data-previous-key-type`。下面是代码的样子:

```
const updateCalculatorState = (key, calculator) => {
  const keyType = getKeyType(key)

  if (!action) {
    // ...
    calculator.dataset.previousKeyType = 'number'
  }

  if (action === 'decimal') {
    // ...
    calculator.dataset.previousKeyType = 'decimal'
  }

  if (
    action === 'add' ||
    action === 'subtract' ||
    action === 'multiply' ||
    action === 'divide'
  ) {
    // ...
    calculator.dataset.previousKeyType = 'operator'
  }

  if (action === 'clear') {
    // ...
    calculator.dataset.previousKeyType = 'clear'
  }

  if (action === 'calculate') {
    calculator.dataset.previousKeyType = 'calculate'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是多余的，因为我们已经知道了带有`getKeyType`的键类型。我们可以将上面的重构为:

```
const updateCalculatorState = (key, calculator) => {
  const keyType = getKeyType(key)
  calculator.dataset.previousKeyType = keyType

  if (keyType === 'number') { /* ... */ }
  if (keyType === 'decimal') { /* ... */ }
  if (keyType === 'operator') { /* ... */}
  if (keyType === 'clear') { /* ... */ }
  if (keyType === 'calculate') { /* ... */ }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 制作`updateCalculatorState`为操作键

视觉上，我们需要确保所有的键都释放它们的按下状态。在这里，我们可以复制并粘贴之前的代码:

```
const updateCalculatorState = (key, calculator) => {
  const keyType = getKeyType(key)
  calculator.dataset.previousKeyType = keyType

  Array.from(key.parentNode.children).forEach(k => k.classList.remove('is-depressed'))
} 
```

Enter fullscreen mode Exit fullscreen mode

在将与`display.textContent`相关的部分移到`createResultString`之后，下面是我们为操作键编写的内容。

```
if (keyType === 'operator') {
  if (firstValue &&
      operator &&
      previousKeyType !== 'operator' &&
      previousKeyType !== 'calculate'
  ) {
    calculator.dataset.firstValue = calculatedValue
  } else {
    calculator.dataset.firstValue = displayedNum
  }

  key.classList.add('is-depressed')
  calculator.dataset.operator = key.dataset.action
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能会注意到，我们可以用一个三元运算符来缩短代码:

```
if (keyType === 'operator') {
  key.classList.add('is-depressed')
  calculator.dataset.operator = key.dataset.action
  calculator.dataset.firstValue = firstValue &&
    operator &&
    previousKeyType !== 'operator' &&
    previousKeyType !== 'calculate'
    ? calculatedValue
    : displayedNum
} 
```

Enter fullscreen mode Exit fullscreen mode

像以前一样，记下您需要的变量和属性。这里，我们需要`calculatedValue`和`displayedNum`。

```
const updateCalculatorState = (key, calculator) => {
  // Variables and properties needed
  // 1\. key
  // 2\. calculator
  // 3\. calculatedValue
  // 4\. displayedNum
} 
```

Enter fullscreen mode Exit fullscreen mode

### 制作`updateCalculatorState`为清除键

下面是清除键的剩余代码:

```
if (action === 'clear') {
  if (key.textContent === 'AC') {
    calculator.dataset.firstValue = ''
    calculator.dataset.modValue = ''
    calculator.dataset.operator = ''
    calculator.dataset.previousKeyType = ''
  } else {
    key.textContent = 'AC'
  }
}

if (action !== 'clear') {
  const clearButton = calculator.querySelector('[data-action=clear]')
  clearButton.textContent = 'CE'
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们没有什么可以重构的；请随意将所有内容复制/粘贴到`updateCalculatorState`中。

### 制作`updateCalculatorState`为等号键

下面是我们为等号键编写的代码:

```
if (action === 'calculate') {
  let firstValue = calculator.dataset.firstValue
  const operator = calculator.dataset.operator
  let secondValue = displayedNum

  if (firstValue) {
    if (previousKeyType === 'calculate') {
      firstValue = displayedNum
      secondValue = calculator.dataset.modValue
    }

    display.textContent = calculate(firstValue, operator, secondValue)
  }

  calculator.dataset.modValue = secondValue
  calculator.dataset.previousKeyType = 'calculate'
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们去掉所有与`display.textContent`有关的东西，这就是我们剩下的东西。

```
if (action === 'calculate') {
  let secondValue = displayedNum

  if (firstValue) {
    if (previousKeyType === 'calculate') {
      secondValue = calculator.dataset.modValue
    }
  }

  calculator.dataset.modValue = secondValue
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将它重构如下:

```
if (keyType === 'calculate') {
  calculator.dataset.modValue = firstValue && previousKeyType === 'calculate'
    ? modValue
    : displayedNum
} 
```

Enter fullscreen mode Exit fullscreen mode

像往常一样，记下所使用的属性和变量:

```
const updateCalculatorState = (key, calculator) => {
  // Variables and properties needed
  // 1\. key
  // 2\. calculator
  // 3\. calculatedValue
  // 4\. displayedNum
  // 5\. modValue
} 
```

Enter fullscreen mode Exit fullscreen mode

### 传入必要的变量

我们知道`updateCalculatorState`需要五个变量/属性:

1.  `key`
2.  `calculator`
3.  `calculatedValue`
4.  `displayedNum`
5.  `modValue`

由于可以从`calculator.dataset`中检索到`modValue`，我们只需要传入四个值:

```
const updateCalculatorState = (key, calculator, calculatedValue, displayedNum) => {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

```
keys.addEventListener('click', e => {
  if (e.target.matches('button')) return

  const key = e.target
  const displayedNum = display.textContent
  const resultString = createResultString(key, displayedNum, calculator.dataset)

  display.textContent = resultString

  // Pass in necessary values
  updateCalculatorState(key, calculator, resultString, displayedNum)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 再次重构 updateCalculatorState

我们在`updateCalculatorState`中改变了三种值:

1.  `calculator.dataset`
2.  按下/压下操作符的类别
3.  `AC` vs `CE`文本

如果你想让它更简洁，你可以把(2)和(3)拆分成另一个函数——`updateVisualState`。下面是`updateVisualState`可能的样子:

```
const updateVisualState = (key, calculator) => {
  const keyType = getKeyType(key)
  Array.from(key.parentNode.children).forEach(k => k.classList.remove('is-depressed'))

  if (keyType === 'operator') key.classList.add('is-depressed')

  if (keyType === 'clear' && key.textContent !== 'AC') {
    key.textContent = 'AC'
  }

  if (keyType !== 'clear') {
    const clearButton = calculator.querySelector('[data-action=clear]')
    clearButton.textContent = 'CE'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

重构后，代码变得更加清晰。如果你研究一下事件监听器，你就会知道每个函数是做什么的。

下面是事件监听器最后的样子:

```
keys.addEventListener('click', e => {
  if (e.target.matches('button')) return
  const key = e.target
  const displayedNum = display.textContent

  // Pure functions
  const resultString = createResultString(key, displayedNum, calculator.dataset)

  // Update states
  display.textContent = resultString
  updateCalculatorState(key, calculator, resultString, displayedNum)
  updateVisualState(key, calculator)
}) 
```

Enter fullscreen mode Exit fullscreen mode

至于你的家庭作业，自己完成重构练习，看看你是否能在不看课程的情况下完成它。

我希望你喜欢这篇文章。如果你有，你会想看看[学习 JavaScript](https://learnjavascript.today)——一门帮助你一劳永逸地学习 JavaScript 的课程。
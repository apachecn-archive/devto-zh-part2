# 如何构建计算器—第 1 部分

> 原文：<https://dev.to/zellwk/how-to-build-a-calculatorpart-1-22io>

这是关于制作计算器的三节课的开始。在这三课结束时，您应该会得到一个功能与 iPhone 计算器完全一样的计算器(没有`+/-`和百分比功能)。

以下是您将获得的内容:

[![GIF of a calculator you'll build](../Images/0b42034a275434bd3e3d64ca174d5aa0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XhQBEzcN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.com/images/2018/calculator-1/calculator.gif) 

<figure>

<figcaption>GIF 一个计算器你就造</figcaption>

</figure>

## 先决条件

在你尝试完成本课之前，请确保你已经掌握了 JavaScript。至少，你需要知道这些事情:

1.  [If/else 语句](https://dev.to/blog/js-if-else)
2.  [用于循环](https://dev.to/blog/js-for-loops)
3.  [JavaScript 函数](https://dev.to/blog/js-functions)
4.  [箭头功能](https://dev.to/blog/es6/#arrow-functions)
5.  `&&`和`||`运算符
6.  如何用`textContent`属性改变文本
7.  如何使用事件委托模式添加事件侦听器

注意:这篇文章是来自[学习 JavaScript](https://learnjavascript.today) 的一个示例课程——一个帮助你一劳永逸地学习 JavaScript 的课程。如果你喜欢这一课，就来看看吧:)

## 开始之前

我建议你在学习本课之前，先试着自己制作一个计算器。这是一个很好的练习，因为你将训练自己像一个开发者一样思考。

当你尝试了一个小时后，再回到这一课(不管你是成功还是失败；当你尝试的时候，你会思考，这将有助于你更快地吸取教训。

有了这些，让我们从理解计算器是如何工作的开始。

## 构建计算器

首先，我们想制造计算器。

这个计算器由两部分组成。显示器和按键。

[![Squares that label the calculator's display and keys](../Images/7639c53dfa5f582de6227af05bcdcc5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k76cFMtL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.com/images/2018/calculator-1/display-and-keys.png) 

<figure>

<figcaption>计算器有一个显示屏和几个按键</figcaption>

</figure>

```
<div class="calculator">
  <div class="calculator__display">0</div>
  <div class="calculator__keys"> ... </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用 CSS Grid 来制作键，因为它们是以类似网格的格式排列的。这已经在 starter 文件中为您完成了。你可以在这支笔的[处找到启动文件。](https://codepen.io/zellwk/pen/pLgmGL) 

```
.calculator__keys {
  display: grid;
  /* other necessary CSS */
} 
```

Enter fullscreen mode Exit fullscreen mode

为了帮助我们识别操作符、十进制、清除和等号键，我们将提供一个数据操作属性来描述它们的作用。

```
<div class="calculator__keys">
  <button class="key--operator" data-action="add">+</button>
  <button class="key--operator" data-action="subtract">-</button>
  <button class="key--operator" data-action="multiply">&times;</button>
  <button class="key--operator" data-action="divide">÷</button>
  <button>7</button>
  <button>8</button>
  <button>9</button>
  <button>4</button>
  <button>5</button>
  <button>6</button>
  <button>1</button>
  <button>2</button>
  <button>3</button>
  <button>0</button>
  <button data-action="decimal">.</button>
  <button data-action="clear">AC</button>
  <button class="key--equal" data-action="calculate">=</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 听按键声

当一个人拿到计算器时，可能会发生五种情况:

1.  他们按了一个数字键(0-9)
2.  他们按了一个操作键(+、-、×、)
3.  他们按了十进制键
4.  他们按下了等号键
5.  他们按下了清除键

构建这个计算器的第一步是能够(1)监听所有按键，以及(2)确定所按按键的类型。在这种情况下，我们可以使用事件委托模式来监听，因为键都是`.calculator__keys`的子元素。

```
const calculator = document.querySelector('.calculator')
const keys = calculator.querySelector('.calculator__keys')

keys.addEventListener('click', e => {
  if (e.target.matches('button')) {
    // Do something
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以使用`data-action`属性来确定被点击的键的类型。

```
const key = e.target
const action = key.dataset.action 
```

Enter fullscreen mode Exit fullscreen mode

如果该键没有`data-action`属性，则它必须是数字键。

```
if (!action) {
  console.log('number key!')
} 
```

Enter fullscreen mode Exit fullscreen mode

如果这个键有一个`data-action`，或者是`add`、`subtract`、`multiply`或者是`divide`，我们就知道这个键是一个操作符。

```
if (
  action === 'add' ||
  action === 'subtract' ||
  action === 'multiply' ||
  action === 'divide'
) {
  console.log('operator key!')
} 
```

Enter fullscreen mode Exit fullscreen mode

如果键的`data-action`是`decimal`，我们知道用户点击了十进制键。按照同样的思路，如果键的`data-action`是`clear`，我们知道用户点击了 clear(显示 AC 的那个)键；如果键的`data-action`是`calculate`，我们知道用户点击了等号键。

```
if (action === 'decimal') {
  console.log('decimal key!')
}

if (action === 'clear') {
  console.log('clear key!')
}

if (action === 'calculate') {
  console.log('equal key!')
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，您应该从每个计算器键得到一个`console.log`响应。

[![We're now able to detect different types of keys](../Images/7c37a18d6521914c6454312a402bf3e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hrzFmjFn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.com/images/2018/calculator-1/click-key.gif) 

<figure>

<figcaption>我们现在能够检测不同类型的按键</figcaption>

</figure>

## 建设幸福之路

当用户拿起计算器时，他们可以选择以下五种按键中的任何一种:

1.  数字键(0-9)
2.  一种操作键(+、-、×、)
3.  十进制键
4.  等号键
5.  透明钥匙

一次考虑五种类型的按键可能会让人不知所措，所以让我们一步一步来，考虑一个正常人拿起计算器时会做什么。这条“正常人会做的事”被称为幸福之路。

让我们称我们的正常人为玛丽。

当玛丽拿起计算器时，她可能会敲击数字键。

## 当用户点击数字键时

此时，如果计算器显示 0(默认数字)，目标数字应代替零。

[![Calculator replaces 0 with 9](../Images/3113cf3e0f2a33d0a73a4601c8b2a6db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_f5vviz9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.com/images/2018/calculator-1/num-zero.gif) 

<figure>

<figcaption>计算器用 9</figcaption>

</figure>

代替 0

如果计算器显示一个非零数字，目标数字应附加到显示的数字上。

[![Calculator appends 5 to 9](../Images/c86888f614af3898328f362afa027095.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dr4iqiJ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.com/images/2018/calculator-1/num-non-zero.gif) 

<figure>

<figcaption>计算器追加 5 到 9</figcaption>

</figure>

在这里，我们需要知道两件事:

1.  被单击的键的编号
2.  当前显示的数字

我们可以分别通过被点击键的`textContent`属性和`.calculator__display`得到这两个值。

```
const display = document.querySelector('.calculator__display')

keys.addEventListener('click', e => {
  if (e.target.matches('button')) {
    const key = e.target
    const action = key.dataset.action
    const keyContent = key.textContent
    const displayedNum = display.textContent
    // ...
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

**如果计算器显示 0，我们想用点击的键替换计算器的显示。**我们可以通过替换显示的 textContent 属性来实现。

```
if (!action) {
  if (displayedNum === '0') {
    display.textContent = keyContent
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**如果计算器显示一个非零数字，我们希望将点击的键附加到显示的数字上。为了追加一个数字，我们连接了一个字符串。** 

```
if (!action) {
  if (displayedNum === '0') {
    display.textContent = keyContent
  } else {
    display.textContent = displayedNum + keyContent
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，Mary 可以单击这些键中的任意一个:

1.  十进制键
2.  操作员键

假设玛丽按了十进制键。

## 当用户点击十进制键时

当玛丽按下十进制键时，显示屏上会出现一个十进制数。如果 Mary 在敲击十进制键后敲击任何数字，该数字也应该附加在显示器上。

[![Calculator adds a decimal, followed by a number, to the display](../Images/28a18aa277fbaa3162d938ac29c9dcdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G4Dmekkh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.com/images/2018/calculator-1/decimal-non-zero.gif) 

<figure>

<figcaption>计算器添加一个小数，后跟一个数字，到显示器</figcaption>

</figure>

为了创造这种效果，我们可以将`.`连接到显示的数字上。

```
if (action === 'decimal') {
  display.textContent = displayedNum + '.'
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们假设 Mary 通过按操作键继续她的计算。

## 当用户点击操作键时

如果 Mary 点击了一个操作键，则该操作键应该被突出显示，这样 Mary 就知道该操作键处于活动状态。

[![Operator keys should be depressed when they're clicked on](../Images/7ae0c595f10a63564a61e97e3e7f8c4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e-BfsEQW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.com/images/2018/calculator-1/operator.gif) 

<figure>

<figcaption>点击</figcaption>

</figure>

时要按下操作键

为此，我们可以将`is-depressed`类添加到操作键中。

```
if (
  action === 'add' ||
  action === 'subtract' ||
  action === 'multiply' ||
  action === 'divide'
) {
  key.classList.add('is-depressed')
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦玛丽按下一个操作键，她就会按下另一个数字键。

## 当用户在操作键之后点击数字键时

当 Mary 再次点击数字键时，先前的显示将被新的数字所取代。操作键也应该释放它的按下状态。

[![Display replaced by a new number](../Images/ba358199baa7e2c9602d8d0baa9de148.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sP_TPQim--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.com/images/2018/calculator-1/num-after-operator.gif) 

<figure>

<figcaption>显示被新的数字</figcaption>

</figure>

取代

为了释放按下的状态，我们通过一个`forEach`循环:
从所有键中移除`is-depressed`类

```
keys.addEventListener('click', e => {
  if (e.target.matches('button')) {
    const key = e.target
    // ...

    // Remove .is-depressed class from all keys
    Array.from(key.parentNode.children)
      .forEach(k => k.classList.remove('is-depressed'))
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们希望将显示更新为单击的键。在我们这样做之前，我们需要一种方法来判断前一个键是否是操作键。

一种方法是通过自定义属性。我们把这个自定义属性叫做`data-previous-key-type`。

```
const calculator = document.querySelector('.calculator')
// ...

keys.addEventListener('click', e => {
  if (e.target.matches('button')) {
    // ...

    if (
      action === 'add' ||
      action === 'subtract' ||
      action === 'multiply' ||
      action === 'divide'
    ) {
      key.classList.add('is-depressed')
      // Add custom attribute
      calculator.dataset.previousKeyType = 'operator'
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果`previousKeyType`是一个操作符，我们想用点击的数字替换显示的数字。

```
const previousKeyType = calculator.dataset.previousKeyType

if (!action) {
  if (displayedNum === '0' || previousKeyType === 'operator') {
    display.textContent = keyContent
  } else {
    display.textContent = displayedNum + keyContent
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，假设 Mary 决定按等号键来完成她的计算。

## 当用户点击等号键时

当 Mary 按下等号键时，计算器应该会计算出一个取决于三个值的结果:

1.  输入计算器的第一个数字
2.  *操作员*
3.  输入计算器的第二个数字

计算后，结果应取代显示的值。

[![Calculates the correct value](../Images/b1dbda162e52de4c6ed22415119f177e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FqSXVwrB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.com/images/2018/calculator-1/equal.gif) 

<figure>

<figcaption>计算正确值</figcaption>

</figure>

至此，我们只知道了*秒的数字*——当前显示的数字。

```
if (action === 'calculate') {
  const secondValue = displayedNum
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

为了得到第一个数字，我们需要在清除之前存储计算器的显示值。保存第一个数字的一种方法是在单击 operator 按钮时将它添加到一个自定义属性中。

为了得到*操作符*，我们也可以使用相同的技术。

```
if (
  action === 'add' ||
  action === 'subtract' ||
  action === 'multiply' ||
  action === 'divide'
) {
  // ...
  calculator.dataset.firstValue = displayedNum
  calculator.dataset.operator = action
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了需要的三个值，我们就可以进行计算了。最终，我们希望代码看起来像这样:

```
if (action === 'calculate') {
  const firstValue = calculator.dataset.firstValue
  const operator = calculator.dataset.operator
  const secondValue = displayedNum

  display.textContent = calculate(firstValue, operator, secondValue)
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们需要创建一个`calculate`函数。它应该接受三个参数——第一个数字、操作符和第二个数字。

```
const calculate = (n1, operator, n2) => {
  // Perform calculation and return calculated value
} 
```

Enter fullscreen mode Exit fullscreen mode

如果运算符是`add`，我们要把值加在一起；如果运算符是`subtract`，我们要减去这些值，以此类推。

```
const calculate = (n1, operator, n2) => {
  let result = ''

  if (operator === 'add') {
    result = n1 + n2
  } else if (operator === 'subtract') {
    result = n1 - n2
  } else if (operator === 'multiply') {
    result = n1 * n2
  } else if (operator === 'divide') {
    result = n1 / n2
  }

  return result
} 
```

Enter fullscreen mode Exit fullscreen mode

记住`firstValue`和`secondValue`在这一点上是字符串。如果你把字符串加在一起，你会把它们连接起来(`1 + 1 = 11`)。

所以，在计算结果之前，我们想把字符串转换成数字。我们可以通过两个函数来实现这一点——T0 和 T1。

*   `parseInt`将一个字符串转换成一个**整数**。
*   `parseFloat`将一个字符串转换成一个**浮点**(这意味着一个带小数位的数字)。

对于计算器，我们需要一个浮点数。

```
const calculate = (n1, operator, n2) => {
  let result = ''

  if (operator === 'add') {
    result = parseFloat(n1) + parseFloat(n2)
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

## 包装完毕

就是这样；我们完成了幸福之路的构建！😄

但是我们还没有完成计算器的制作。这是因为用户在现实中倾向于偏离快乐的道路。

因此，当你使用任何应用程序时，你要确保你能应付可能发生的常见边缘情况。您将在下一课中学习如何做到这一点。

我希望你喜欢这篇文章。如果你有，你可能想看看[学习 JavaScript](https://learnjavascript.today)——一门帮助你一劳永逸地学习 JavaScript 的课程。

注:本文原载于[我的博客](https://zellwk.com/images/2018/calculator-1/calculator.gif)。如果你想要源代码，到那边去！:)
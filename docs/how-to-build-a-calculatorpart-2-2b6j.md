# 如何构建计算器—第 2 部分

> 原文：<https://dev.to/zellwk/how-to-build-a-calculatorpart-2-2b6j>

这是关于制作计算器的三节课中的第二节。在这三课结束时，您应该会得到一个功能与 iPhone 计算器完全一样的计算器(没有`+/-`和百分比功能)。

注意:请确保在开始这篇文章之前完成第一部分[。](https://zellwk.com/blog/calculator-part-1)

在本课中，您将学习针对边缘情况进行编码，以使您的计算器能够适应奇怪的输入模式。

要做到这一点，你必须想象一个捣乱者试图通过按错键来破坏你的计算器。我们把这个捣蛋鬼叫做蒂姆吧。

蒂姆可以按任意顺序敲击这些按键:

1.  数字键(0-9)
2.  一种操作键(+、-、×、)
3.  十进制键
4.  等号键
5.  透明钥匙

## 如果蒂姆按了十进制键会发生什么

如果 Tim 在显示器已经显示小数点的时候按了一个十进制键，那么什么都不会发生。

[![Nothing happens when a user hits the decimal key when the display already shows a decimal point](img/4000caec3b86c9c4f3d4adf2322d789a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5VH_sLV5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/decimal-multiple.gif) 

<figure>

<figcaption>当显示器已经显示小数点</figcaption>

</figure>

时，当用户点击十进制键时没有任何反应

[![Nothing should happen even if the previous key isn't the decimal key](img/1ad128231e8bc1f0b40c96c1e01d0fc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a__k9tE---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/decimal-multiple-2.gif) 

<figure>

<figcaption>即使上一个键不是十进制键</figcaption>

</figure>

，也不会发生任何事情

这里，我们可以用`includes`方法检查显示的数字是否包含一个`.`。

`includes`检查给定匹配的字符串。如果找到一个字符串，则返回`true`；如果不是，它返回`false`。注意:`includes`区分大小写

```
// Example of how includes work.
const string = 'The hamburgers taste pretty good!'
const hasExclaimation = string.includes('!')

console.log(hasExclaimation) // true 
```

Enter fullscreen mode Exit fullscreen mode

```
// Do nothing if string has a dot
if (!displayedNum.includes('.')) {
  display.textContent = displayedNum + '.'
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，如果 Tim 在按下操作键后按下十进制键，显示屏将显示`0.`。

[![Display should show '0.' if a user hits a decimal key after an operator key](img/09597c6e8f31c7cb2829f03f9ab5c1ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vHK69CJP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/decimal-after-operator.gif) 

<figure>

<figcaption>显示屏应显示“0”如果用户在操作键</figcaption>

</figure>

后点击十进制键

这里我们需要知道前一个键是否是运算符。我们可以通过检查在上一课中设置的自定义属性`data-previous-key-type`来判断。

`data-previous-key-type`还没有完成。为了正确识别`previousKeyType`是否是一个操作符，我们需要为每个被点击的键更新`previousKeyType`。

```
if (!action) {
  // ...
  calculator.dataset.previousKey = 'number'
}

if (action === 'decimal') {
  // ...
  calculator.dataset.previousKey = 'decimal'
}

if (action === 'clear') {
  // ...
  calculator.dataset.previousKeyType = 'clear'
}

if (action === 'calculate') {
 // ...
  calculator.dataset.previousKeyType = 'calculate'
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了正确的`previousKeyType`，我们就可以用它来检查前一个键是否是一个操作符。

```
if (action === 'decimal') {
  if (!displayedNum.includes('.')) {
    display.textContent = displayedNum + '.'
  } else if (previousKeyType === 'operator') {
    display.textContent = '0.'
  }

  calculator.dataset.previousKeyType = 'decimal'
} 
```

Enter fullscreen mode Exit fullscreen mode

## 如果 Tim 按了操作键会发生什么

首先，如果 Tim 先按下操作键，操作键应该会亮起。(我们已经讨论了这种边缘情况，但是如何讨论呢？看看你能不能认出我们做了什么)。

[![Operator key should light up if it's the first key.](img/31abc0e37d53c9ca0413a0167b605286.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DAvfvCM6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/operator-first.gif) 

<figure>

<figcaption>如果是第一个键，操作键应该亮。</figcaption>

</figure>

第二，如果 Tim 多次按下同一个操作键，应该不会发生任何事情。(我们已经讨论过这种边缘情况)。

注意:如果你想提供更好的 UX，你可以通过一些 CSS 改变来显示操作符被一次又一次的点击。我们没有在这里做，因为在我修复之前，我把所有的 gif 都录下来了。

[![Operator key remains depressed if clicked on multiple times](img/df4502a1abad18b99bead376cf1cefdd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8_H5rVWk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/operator-multiple.gif) 

<figure>

<figcaption>多次点击</figcaption>

</figure>

操作键仍保持按下状态

第三，如果 Tim 在按下第一个操作键之后又按下了另一个操作键，则应该释放第一个操作键；应该按下第二个操作键。(我们也涵盖了这种边缘情况；但是怎么做呢？).

[![The new operator key should be depressed](img/cd5d302e4e88b4d5a4b621c7dd0bc601.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--myS9sBsD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/operator-switch.gif) 

<figure>

<figcaption>新操作员键应被按下</figcaption>

</figure>

第四，如果 Tim 按顺序点击一个数字、一个操作符、一个数字和另一个操作符，则显示应该更新为计算值。

[![Clicking on the operator when numbers are stored in the calculator results in a calculation](img/ee7795a07bd4758dd74e61ae5925659a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cg5MD5LD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/operator-calc.gif) 

<figure>

<figcaption>当数字被存储在计算器中时点击运算符导致一次计算</figcaption>

</figure>

这意味着当`firstValue`、`operator`和`secondValue`存在时，我们需要使用`calculate`函数。

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

  // Note: It's sufficient to check for firstValue and operator because secondValue always exists
  if (firstValue && operator) {
    display.textContent = calculate(firstValue, operator, secondValue)
  }

  key.classList.add('is-depressed')
  calculator.dataset.previousKeyType = 'operator'
  calculator.dataset.firstValue = displayedNum
  calculator.dataset.operator = action
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然我们可以在第二次单击操作键时计算一个值，但我们也在这一点上引入了一个错误——在操作键上的额外单击会在不应该的时候计算一个值。

[![Bug: subsequent clicks on the operator performs a calculation when it shouldn't](img/149e0babb861cc7dfd3bf54aef0caa88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1DwNU5u3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/bug-operator-immed-calc.gif) 

<figure>

<figcaption>Bug:后续点击操作符时执行了不该执行的计算</figcaption>

</figure>

为了防止计算器在后续点击操作键时执行计算，我们需要检查`previousKeyType`是否是一个操作符；如果是，我们不进行计算。

```
if (
  firstValue &&
  operator &&
  previousKeyType !== 'operator'
) {
  display.textContent = calculate(firstValue, operator, secondValue)
} 
```

Enter fullscreen mode Exit fullscreen mode

第五，在操作员键计算一个数字后，如果蒂姆命中一个数字，接着是另一个操作员，操作员应该继续计算，就像这样:`8 - 1 = 7`、`7 - 2 = 5`、`5 - 3 = 2`。

[![Calculator should be able to continue calculation when a user clicks on numbers, followed by operators, followed by numbers, followed by operators, and so on.](img/2c2b99c24e0164a7b1a7045154826902.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mUKDL3ej--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/operator-consec-calc.gif) 

<figure>

<figcaption>计算器应该能够在用户点击数字、运算符、数字、运算符等时继续计算。</figcaption>

</figure>

现在，我们的计算器不能进行连续计算。第二个计算值是错误的。这里是我们有的:`99 - 1 = 98`，`98 - 1 = 0`。

[![Calculated values are wrong. Second calculated value should be 97 instead of 0](img/a36d02e26faca4e7a5f73c7b9912140a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8DUoSB9r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/operator-consec-calc-bug.gif) 

<figure>

<figcaption>计算值有误。第二个计算值应该是 97 而不是 0</figcaption>

T7】</figure>

第二个值计算错误，因为我们将错误的值输入到`calculate`函数中。让我们通过几张图片来理解我们的代码做了什么。

### 了解我们的计算功能

首先，假设用户点击了一个数字，99。此时，计算器中尚未注册任何内容。

[![When a user hits numbers, the calculator doesn't register `firstValue` or `operator`](img/175b0e2360a217c1a2aeaf684319ebcc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PaxFUzOk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/op-consec-calc-1.png) 

<figure>

<figcaption>当用户点击数字时，计算器不会记录`firstValue`或`operator`</figcaption>

</figure>

第二，假设用户点击了减法运算符。在他们点击减法运算符后，我们将`firstValue`设置为 99。我们还设置了`operator`来减去。

点击 操作员按钮后，设置[![`firstValue` and `operator` are set after the operator button is clicked](img/d53fdbcc5b3cf8d23164441e42eb6ffc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NaR56BTT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/op-consec-calc-2.png)

<figure>

<figcaption>`firstValue``operator`</figcaption>

</figure>

第三，假设用户点击了第二个值；这次是 1。此时，显示的数字更新为 1，但我们的`firstValue`、`operator`和`secondValue`保持不变。

[![Display updates to 1, but `firstValue` and `operator` remains at `99` and `subtract`](img/adcf19459a5870ef391c279d41106e16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o5aV6GQX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/op-consec-calc-3.png) 

<figure>

<figcaption>显示更新为 1，但`firstValue`和`operator`仍保持在`99`和`subtract`</figcaption>

</figure>

第四，用户再次点击减法。就在他们点击减法之后，在我们计算结果之前，我们将`secondValue`设置为显示的数字。

[![We set `secondValue` to 1](img/d7aba582948c8d88a25f17b01eb61410.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hK7Kxh02--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/op-consec-calc-4.png) 

<figure>

<figcaption>我们将`secondValue`设为 1</figcaption>

</figure>

第五，我们用`firstValue` 99、`operator`减法和`secondValue` 1 来执行计算。结果是 98。

计算出结果后，我们将显示结果。然后，我们将`operator`设置为减法，将`firstValue`设置为之前显示的数字。

[![After calculation, firstValue is set to `displayedNum`](img/1ff514c90b4992f149c09fb9674ed454.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hC0vEcfj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/op-consec-calc-5.png) 

<figure>

<figcaption>计算后，firstValue 设置为`displayedNum`</figcaption>

</figure>

嗯，这是非常错误的！如果我们想继续计算，我们需要用计算出的值更新`firstValue`。

[![updates calculated value as `firstValue`](img/432e3e9be1e864135e456ffcb534c39c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vyeKtmGO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/op-consec-calc-6.png) 

<figure>

<figcaption>将计算值更新为`firstValue`</figcaption>

</figure>

```
const firstValue = calculator.dataset.firstValue
const operator = calculator.dataset.operator
const secondValue = displayedNum

if (
  firstValue &&
  operator &&
  previousKeyType !== 'operator'
) {
  const calcValue = calculate(firstValue, operator, secondValue)
  display.textContent = calcValue

  // Update calculated value as firstValue
  calculator.dataset.firstValue = calcValue
} else {
  // If there are no calculations, set displayedNum as the firstValue
  calculator.dataset.firstValue = displayedNum
}

key.classList.add('is-depressed')
calculator.dataset.previousKeyType = 'operator'
calculator.dataset.operator = action 
```

Enter fullscreen mode Exit fullscreen mode

通过此修复，由操作键完成的连续计算现在应该是正确的。

[![Consecutive calculations done with the operator key is now correct](img/531f84b7126e6b5a54bbc2737838a125.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QG8z8w8P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/op-consec-calc-fixed.gif) 

<figure>

<figcaption>用操作键完成的连续计算现在是正确的</figcaption>

</figure>

## 如果 Tim 按了等号键会怎么样？

首先，如果 Tim 在按任何操作键之前先按等号键，则不会发生任何事情，

[![Calculator should show zero if equal key is hit first](img/42e0ac49d3a0cf83cb55cde6ee7e12a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D6XhaAUK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/equal-first.gif) 

<figure>

<figcaption>如果先按下等号键</figcaption>

</figure>

，计算器应显示零

[![When no calculation is required, display remains the same](img/0f1824a7daf3618b96377ee00205360d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eu6OyZ5a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/equal-after-num.gif) 

<figure>

<figcaption>当不需要计算时，显示保持不变</figcaption>

</figure>

我们知道，如果`firstValue`没有设置为数字，操作键还没有被点击。我们可以利用这一知识来防止平等计算。

```
if (action === 'calculate') {
  const firstValue = calculator.dataset.firstValue
  const operator = calculator.dataset.operator
  const secondValue = displayedNum

  if (firstValue) {
    display.textContent = calculate(firstValue, operator, secondValue)
  }

  calculator.dataset.previousKeyType = 'calculate'
} 
```

Enter fullscreen mode Exit fullscreen mode

第二，如果 Tim 命中一个数字，后面跟一个运算符，后面跟一个等号，那么计算器应该这样计算结果:

1.  `2 + =` — > `2 + 2 = 4`
2.  `2 - =` — > `2 - 2 = 0`
3.  `2 × =` — > `2 × 2 = 4`
4.  `2 ÷ =` — > `2 ÷ 2 = 1`

[![The calculator should treat first and second values as the same numbers if it's missing a value](img/00b44470fa41e3284e89f8ae6106a5dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hLgtF5D3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/num-op-equal.gif) 

<figure>

<figcaption>如果缺少一个值</figcaption>

</figure>

，计算器会将第一个和第二个值视为相同的数字

我们已经考虑到了这个奇怪的输入。你能理解为什么吗？:)

第三，如果 Tim 在一次计算完成后按了等号键，则应该再次执行另一次计算。计算应该是这样的:

1.  蒂姆以 5 比 1 击出关键一击
2.  蒂姆打了个平手。计算值为`5 - 1 = 4`
3.  蒂姆打了个平手。计算值为`4 - 1 = 3`
4.  蒂姆打了个平手。计算值为`3 - 1 = 2`
5.  蒂姆打了个平手。计算值为`2 - 1 = 1`
6.  蒂姆打了个平手。计算值为`1 - 1 = 0`

[![When a user hits the equal key multiple times, the calculator should continue to calculate](img/2cc95c30be205028b10eeca2e8b955b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N1pmjGca--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/eq-consec-click-fixed.gif) 

<figure>

<figcaption>当用户多次点击等号键时，计算器应继续计算</figcaption>

</figure>

不幸的是，我们的计算器把计算搞乱了。下面是我们的计算器显示的内容:

1.  蒂姆以 5 比 1 击出关键一击
2.  蒂姆打了个平手。计算值为`4`
3.  蒂姆打了个平手。计算值为`1`

[![Equal key consecutive calculation gives a wrong result](img/49efaa55a02cabbddb3f32392804d63d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DM_EHmo_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/eq-consec-calc-wrong.gif) 

<figure>

<figcaption>等键连续计算给出错误结果</figcaption>

</figure>

### 修正计算

首先，假设我们的用户点击了 5 次。此时，计算器中尚未注册任何内容。

[![When a user clicked on the first number the calculator doesn't register `firstValue` or `operator`](img/3cf368bbaf93173170f597d4e7e75d8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oEVsVHZN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/eq-consec-calc-1.png) 

<figure>

<figcaption>当用户点击第一个数字时，计算器不会记录`firstValue`或`operator`</figcaption>

</figure>

第二，假设用户点击了减法运算符。在他们点击减法运算符后，我们将`firstValue`设置为 5。我们还设置了`operator`来减去。

点击 操作员按钮后，设置[![`firstValue` and `operator` are set after the operator button is clicked](img/a3c651e0bbf9511d7e796abff5909136.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IT3lLzIL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/eq-consec-calc-2.png)

<figure>

<figcaption>`firstValue``operator`</figcaption>

</figure>

第三，用户点击第二个值。假设是 1。此时，显示的数字更新为 1，但我们的`firstValue`、`operator`和`secondValue`保持不变。

[![Display updates to 1, but `firstValue` and `operator` remains at `5` and `subtract`](img/a13f01a5c37a5cb16b5d333ab45e32de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1RzC5RUE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/eq-consec-calc-3.png) 

<figure>

<figcaption>显示更新为 1，但`firstValue`和`operator`仍保持在`5`和`subtract`</figcaption>

</figure>

第四，用户点击等号键。就在他们点击等于之后，但是在计算之前，我们将`secondValue`设置为`displayedNum`

[![`displayedNum` is set as `secondValue`](img/2c3f35f7f924d875e8b4d7861d000669.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4zo_z3tX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/eq-consec-calc-4.png) 

<figure>

<figcaption>我们把`secondValue`设定为`displayedNum`</figcaption>

</figure>

第五，计算器计算出`5 - 1`的结果，给出`4`。结果会更新到显示屏上。`firstValue`和`operator`会被结转到下一次计算，因为我们没有更新它们。

[![`firstValue` and `operator` are used for the next operation](img/c59f2b5d45a91c4b9f77315191c77793.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XoMseZ_o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/eq-consec-calc-5.png) 

<figure>

<figcaption>`firstValue`和`operator`用于下一次操作</figcaption>

</figure>

第六，当用户再次命中等号时，我们在计算前将`secondValue`设置为`displayedNum`。

[![Once again, displayed num is set as the `secondValue` before the calculation](img/d0c1bc555bff562e571c5448041a449a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZBZsuGno--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/eq-consec-calc-6.png) 

<figure>

<figcaption>再次显示，小水被设置为`secondValue`之前的</figcaption>

</figure>

你可以看出这里出了什么问题。

代替`secondValue`，我们希望将`firstValue`设置为显示的数字。

```
if (action === 'calculate') {
  let firstValue = calculator.dataset.firstValue
  const operator = calculator.dataset.operator
  const secondValue = displayedNum

  if (firstValue) {
    if (previousKeyType === 'calculate') {
      firstValue = displayedNum
    }

    display.textContent = calculate(firstValue, operator, secondValue)
  }

  calculator.dataset.previousKeyType = 'calculate'
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还想将之前的`secondValue`带入新的计算中。为了让`secondValue`持续到下一次计算，我们需要将它存储在另一个定制属性中。让我们称这个自定义属性为`modValue`(代表修改值)。

```
if (action === 'calculate') {
  let firstValue = calculator.dataset.firstValue
  const operator = calculator.dataset.operator
  const secondValue = displayedNum

  if (firstValue) {
    if (previousKeyType === 'calculate') {
      firstValue = displayedNum
    }

    display.textContent = calculate(firstValue, operator, secondValue)
  }

  // Set modValue attribute
  calculator.dataset.modValue = secondValue
  calculator.dataset.previousKeyType = 'calculate'
} 
```

Enter fullscreen mode Exit fullscreen mode

如果`previousKeyType`是`calculate`，我们知道我们可以用`calculator.dataset.modValue`作为`secondValue`。一旦我们知道了这一点，我们就可以进行计算。

```
if (firstValue) {
  if (previousKeyType === 'calculate') {
    firstValue = displayedNum
    secondValue = calculator.dataset.modValue
  }

  display.textContent = calculate(firstValue, operator, secondValue)
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，当连续单击等号键时，我们就有了正确的计算结果。

[![Consecutive calculations made by the equal key is now fixed](img/2cc95c30be205028b10eeca2e8b955b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N1pmjGca--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/eq-consec-click-fixed.gif) 

<figure>

<figcaption>由等号键做出的连续计算现在被固定</figcaption>

</figure>

### 回到等号键

第四，如果 Tim 在 calculator 键后打了一个十进制键或数字键，则显示器应分别替换为`0.`或新的数字。

在这里，我们不仅要检查`previousKeyType`是否是`operator`，还要检查它是否是`calculate`。

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

第五，如果 Tim 在等号键后按了一个操作键，计算器将不会计算。

[![Operator keys should not perform calculations if they're clicked after the equal key](img/b5e9a2193d3fb58ae5faf1812b2e62c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H1mAoJEk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/op-after-eq.gif) 

<figure>

<figcaption>操作键在等号键</figcaption>

</figure>

后点击，则不会进行运算

为此，我们在使用操作键执行计算之前检查`previousKeyType`是否为`calculate`。

```
if (
  action === 'add' ||
  action === 'subtract' ||
  action === 'multiply' ||
  action === 'divide'
) {
  // ...

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

  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 如果 Tim 按了清除键会怎么样？

清除键有两种用途:

1.  全部清除(用`AC`表示)清除所有内容，并将计算器重置为初始状态。
2.  清除条目(用`CE`表示)清除当前条目。它将以前的数字保存在内存中。

当计算器处于默认状态时，应显示`AC`。

[![AC should be shown in the initial state](img/e4f7b63156c5731fcf8ddfbeb2ee43fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qhqnzEfl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/calculator-2/default.png) 

<figure>

<figcaption>AC 应显示初始状态</figcaption>

</figure>

第一，如果 Tim 打了一个键(除了 clear 以外的任何键)，`AC`要改成`CE`。

[![AC changes to CE when a key (except clear) gets hit](img/7a77261fa324185d75c0049bc6d9ee6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UtnPP5nP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/decimal-first.gif) 

<figure>

<figcaption>当一个键(清除键除外)被击中</figcaption>

</figure>

时，AC 变为 CE

我们通过检查`data-action`是否为`clear`来实现这一点。如果它不是`clear`，我们寻找清除按钮并改变它的`textContent`。

```
if (action !== 'clear') {
  const clearButton = calculator.querySelector('[data-action=clear]')
  clearButton.textContent = 'CE'
} 
```

Enter fullscreen mode Exit fullscreen mode

第二，如果 Tim 点击`CE`，显示屏应该显示 0。同时，`CE`应该恢复到`AC`，这样蒂姆就可以将计算器复位到初始状态。**

[![If CE is clicked, AC should show](img/6137c8fa787ef30e7617a3dd0f36ea15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_91sXM3Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/calculator-2/clear.gif) 

<figure>

<figcaption>如果点击 CE，AC 应该显示</figcaption>

</figure>

```
if (action === 'clear') {
  display.textContent = 0
  key.textContent = 'AC'
  calculator.dataset.previousKeyType = 'clear'
} 
```

Enter fullscreen mode Exit fullscreen mode

第三，如果 Tim 点击`AC`，将计算器重置为初始状态。

要将计算器重置为初始状态，我们需要清除我们设置的所有自定义属性。

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

## 包装完毕

就是这样！制造一个计算器是困难的，如果你不能制造一个没有错误的计算器，不要责备你自己。

对于家庭作业，在一张纸上写下上面提到的所有边缘案例，然后从头开始重新构建计算器。看看你能否把计算器装起来。慢慢来，一个一个地清除你的错误，你最终会让你的计算器工作起来。

我希望你喜欢这篇文章。如果你有，你会想看看[学习 JavaScript](https://learnjavascript.today)——一门帮助你一劳永逸地学习 JavaScript 的课程。

在下一课中，您将学习用最佳实践重构计算器。
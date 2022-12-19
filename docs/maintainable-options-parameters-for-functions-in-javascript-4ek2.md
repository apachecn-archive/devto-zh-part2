# JavaScript 中函数的可维护选项参数

> 原文：<https://dev.to/davejs/maintainable-options-parameters-for-functions-in-javascript-4ek2>

我的朋友兼同事 Sam Jesso 告诉我，他讨厌使用标志来修改函数的行为。有道理。功能应该遵循[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)并且只做一件事。它使测试和维护代码变得更容易，因为它保持了函数的简单和简洁。然而，深入几乎任何代码库都会发现，我们经常会出现例外并使用标志。

这些例外是否合理不是我想讨论的。因为每个人的代码都是不同的，所以不可能想出一套规则或指导方针来解释什么时候一个异常是有意义的。但是，如果您已经决定要将标志传递到函数中，有一个简单的技巧可以让您的函数的接口对开发人员更加友好。

我们可以将标志(或选项)组合成一个单独的`options`对象:
，而不是将它们视为独立的参数

```
// Before
function func(inputA, inputB, flagA, flagB, flagC) { /* ... */ }
func('Some input.', 'Some more input.', true, false, true);

// After
function func(inputA, inputB, { flagA, flagB, flagC } = {}) { /* ... */ }
func('Some input.', 'Some more input.', { flagA: true, flagC: true }); 
```

Enter fullscreen mode Exit fullscreen mode

与使用单独的参数相比，将选项分组到单个对象中有几个优点。为了更好地理解这些优势，让我们看一个不太抽象的例子...

* * *

## 用格式化时间举例

这里有一个简单的函数从一个`Date`对象:
中获取格式化的时间字符串

```
function formatTime(dateTime) {
  const hours   = leftPad(dateTime.getHours(), 2);
  const minutes = leftPad(dateTime.getMinutes(), 2);
  const seconds = leftPad(dateTime.getSeconds(), 2);

  return `${hours}:${minutes}:${seconds}`;
}

formatTime(new Date());  // 01:23:45 
```

Enter fullscreen mode Exit fullscreen mode

*补充说明:是的，我写了自己版本的`leftPad`，因为我不会为一篇博客文章引入简单的依赖关系。([另外，如果你听到关于 leftPad 的讨论时没有退缩，请花点时间读读这篇文章。](https://www.theregister.co.uk/2016/03/23/npm_left_pad_chaos/) )*

```
function leftPad(number, numberOfChars) {
  let paddedNumber = `${number}`;
  numberOfChars -= paddedNumber.length;
  while (numberOfChars--) paddedNumber = `0${paddedNumber}`;
  return paddedNumber;
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，我知道这很容易出错，但它对我们这里的实现是有效的。

无论如何，回到例子。

### 新要求

我们有一个格式化时间的函数，它做得很好。但是现在我们想让选项在 12 小时制和 24 小时制之间切换。我们还想在某些情况下排除秒。

没问题，我们可以给函数添加一些额外的参数:

```
function formatTime(dateTime, is12Hours, showSeconds = true) {
  const hours   = leftPad(is12Hours ? dateTime.getHours() % 12 : dateTime.getHours(), 2);
  const minutes = leftPad(dateTime.getMinutes(), 2);
  const seconds = showSeconds ? `:${leftPad(dateTime.getSeconds(), 2)}` : '';

  return `${hours}:${minutes}${seconds}`;
}

formatTime(new Date(), true, false);  // 01:23 
```

Enter fullscreen mode Exit fullscreen mode

这种方法有几个问题:

*   **参数必须以特定的顺序传递。**如果我们想要隐藏秒，我们仍然必须在为`showSeconds`指定一个值之前为`is12Hours`传递一个值。
*   **参数未命名。**如果在远离定义的地方调用函数，可能不清楚参数的含义。我们必须查看函数定义，找出各种`true` / `false`值的作用。

这些问题使得函数界面很难阅读和理解，它们放大了人为错误的可能性，特别是当函数有许多选项时，因为很容易意外地跳过参数或混淆它们的顺序。

### 用选项对象重构

解决这些问题的一个简单方法是重构函数，将一个对象用于标志/选项:

```
function formatTime(dateTime, { is12Hours, showSeconds = true } = {}) {
  const hours   = leftPad(is12Hours ? dateTime.getHours() % 12 : dateTime.getHours(), 2);
  const minutes = leftPad(dateTime.getMinutes(), 2);
  const seconds = showSeconds ? `:${leftPad(dateTime.getSeconds(), 2)}` : '';

  return `${leftPad(hours)}:${leftPad(minutes, 2)}${seconds}`;
}

const time = formatTime(new Date(), { 
  is12Hours: true, 
  showSeconds: false 
});   // 01:23 
```

Enter fullscreen mode Exit fullscreen mode

这种方法通过以下方式解决了将标志作为单独参数传递所存在的问题:

*   向接口公开标志名。
*   迫使开发人员正确标记这些标志。
*   使得标志的排序变得无关紧要。
*   当我们需要默认行为时，允许排除标志。

除了使函数更可读之外，我们还使其可维护，因为现在可以更容易地向我们的`formatTime`函数添加许多标志，而不用添加越来越多的无名布尔，使函数调用不可读。我们可以为`showMinutes`、`showMilliseconds`添加标志，甚至可以指定一个自定义分隔符来替换默认的冒号。无论我们添加什么标志或选项，函数都将保持相对的可读性。

* * *

## 还有一件事...

尽管我们已经使函数的接口易于使用和添加，但这并不意味着这些参数的所有功能都应该聚合到一个函数中。使用您的最佳判断，决定何时将功能委托给助手功能。
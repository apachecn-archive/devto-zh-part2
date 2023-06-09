# 不要使用 Number.toFixed 进行舍入

> 原文：<https://dev.to/tomoyukikashiro/do-not-use-numbertofixed-for-rounding-2k41>

这篇文章最初发表于[不要使用 Number.toFixed 进行舍入](https://blog.tomoyukikashiro.me/post/do-not-use-number-tofixed-for-rounding)。

有时我会看到开发人员用[号、原型、固定的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toFixed)来代替`rounding`的用例。但它在某些方面是不正确的。我试图解释为什么它是不正确的。😥

## 返回字符串而不是数字/浮点数

`toFixed`的结果是`String`，所以如果你计算的话会出现意外的结果。

```
// What you expect// 1.1 + 1 = 2.1
// Result1.12.toFixed(1) + 1// '1.1' + 1 = '1.11' 
```

## 不准确的情况

您可以将数字选项传递给`toFixed([digits])`,它表示…

> 数字
> 
> 可选。小数点后出现的位数；这可以是 0 和 20 之间的值，包括 0 和 20，并且实现可以可选地支持更大范围的值。如果省略该参数，它将被视为 0。

例如，你运行`1.1N.toFixd(1)`，返回值将是`1.N`。好的，让我们看看现实世界中发生了什么。

```
1.150.toFixed(1) // -> '1.1' expected '1.2' because of rounding
1.15.toFixed(1) // -> '1.1' expected '1.2' because of rounding
1.151.toFixed(1) // -> '1.2' 
```

对数字旁边应该大于 0 的数字进行舍入。💩
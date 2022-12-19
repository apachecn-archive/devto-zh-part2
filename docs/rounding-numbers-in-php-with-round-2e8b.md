# PHP Round Number 用 Round()对 PHP 中的数字进行舍入

> 原文：<https://dev.to/morinoko/rounding-numbers-in-php-with-round-2e8b>

我不知道你怎么想，但是对我来说，这些从我的[上一篇文章](https://dev.to/morinoko/for-loops-in-php-converting-oven-temperatures-414c)转换的温度有点难读。转换 400 华氏度显示为 204 摄氏度，转换 200 摄氏度显示为 392 华氏度。这些数字真的不容易在烤箱上设定，乍看之下大脑有点难以处理。让我们通过使用 PHP 的`round()`函数四舍五入到最近的五度来解决这个问题！

## 四舍五入到最近的 5 度

PHP 的`round()`函数接受一个十进制数(也称为浮点数)并向上或向下舍入。在其最简单和默认的形式中，它会将小数四舍五入到最接近的整数。例如，`round(3.457)`会给出结果`3`。

如果你想保留一些小数点，你可以添加一个精度参数。缺省值是 0，所以如果没有精度值，`round()`将简单地四舍五入到最接近的整数，如上所述。如果你想把前面的例子四舍五入到小数点后两位，你可以写`round(3.457, 2)`，结果是`3.46`。

所以基本用法是这样的:

```
round(<number you want to round>, <precision/number of decimal points to round to>) 
```

Enter fullscreen mode Exit fullscreen mode

还有第三个参数可以使用，称为“mode ”,它将让您控制舍入的工作方式。例如，您希望舍入到零还是远离零，还是下一个偶数或奇数？在 PHP 手册中了解更多信息。

## 四舍五入到最近的 5 度

然而，当谈到烤箱温度时，我们不只是想舍入到最接近的整数，我们想舍入到最接近的 5 度，因为这些更可能是你可以设置烤箱的实际温度，并且更容易读取。但是，如果我们只是做一些类似于`round(204)`的事情，结果将会是`204`。我们怎样才能把它四舍五入到最接近的 5 度呢？

诀窍是:首先，把你要舍入的数除以 5。那就绕过去。最后，再乘以 5。这将得到四舍五入到最接近的 5 的倍数的原始数字。

```
$celsius = round(204/5) * 5;
print $celsius;

// This will print "205" 
```

Enter fullscreen mode Exit fullscreen mode

如果你想要的是 10 的倍数，那就把你的数字除以 10，四舍五入，然后再乘以 10。

```
$celsius = round(204/10) * 10;
print $celsius;

// This will print "200" 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看我上一篇文章中的温度转换列表。

### 将摄氏温度转换为华氏温度并舍入结果

[https://repl.it/@morinoko_/RoundingOvenTemperaturesCtoF?lite=true](https://repl.it/@morinoko_/RoundingOvenTemperaturesCtoF?lite=true)

### 将华氏温度转换为摄氏温度并舍入结果

[https://repl.it/@morinoko_/RoundingOvenTemperaturesFtoC?lite=true](https://repl.it/@morinoko_/RoundingOvenTemperaturesFtoC?lite=true)

我们走吧！你可以输入烤箱的温度:)
# JavaScript 数学函数和运算符

> 原文：<https://dev.to/codetheweb/javascript-maths-functions-and-operators-1bek>

[![JavaScript Maths functions and operators](../Images/6e3165f8ec587f6e0861abf67f590bd0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BNX0wE2H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/javascript-math/cover.jpg)

在这篇文章中学习如何使用加法、减法、乘法、除法、乘方、舍入、取模等等...

## 入门！

欢迎来到另一篇关于编写 Web 代码的帖子！首先，我想鼓励你跟随这篇文章。这将有助于你更好地学习，也有助于你记住你做了什么。让我们首先创建一个新的 HTML 文件，其中包含一个`<script>`标签:

```
<!DOCTYPE html>
<html>
    <head>
        If statements are awesome!
    </head>
    <body>
        <h1>JavaScript :)</h1>
        <script>
            // Our script will go here!
        </script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，在你的网络浏览器中打开它，你就可以开始了！(不要忘记在每次更改时保存并重新加载页面)

## 数字的类型

JavaScript 中的数字主要有两种类型:*浮点数*和*整数*。整数是没有小数的整数。这里有几个例子:

*   `3`
*   `0`
*   `156`

浮点数是包含小数的数字。需要注意的是*浮点数可以是整数*。等等什么？我记得你说过整数是整数？嗯，像`5.0`这样的东西仍然被认为是浮点数，因为它有一个小数，而*可能是*也可能是`5.1`。

*   `2.225345`
*   `0.0`
*   `42.0`

在大多数情况下，您不必担心这些不同的类型，因为 JavaScript 会自动为您转换它们(😍).然而，在一些编程语言中，你将不得不自己去做。在 JavaScript 中也有一些情况，你*将*必须处理浮点数和整数之间的转换。

## 基本运算符

让我们从头开始——从基本操作开始！

### 加法

JavaScript 中的加法非常简单——你所需要做的就是在两个数字之间加上一个加号，就像在现实生活中一样。试试吧！将以下内容添加到您的脚本中，保存，然后重新加载页面:

```
alert(1 + 2);
// Equal to 3
alert(2.5 + 3.5);
// Equal to 6
alert(-2 + -3);
// Equal to -5 
```

Enter fullscreen mode Exit fullscreen mode

您还可以在一个表达式中添加浮点数和整数:

```
alert(7 + 1.25);
// Equal to 8.25 
```

Enter fullscreen mode Exit fullscreen mode

显然，返回的数字将是一个浮点数。继续前进。

### 减法

减法也像在现实生活中一样有效。简单吧。这里有一些例子——如果你愿意，你可以试一试

```
alert(5 - 3);
// Equal to 2
alert(33 - 42);
// Equal to -9
alert(-3.3 - 1.1);
// Equal to -4.4 
```

Enter fullscreen mode Exit fullscreen mode

### 乘法

乘法和你在纸上做的略有不同。通常，我们会在键盘上使用一个十字符号——字母`x`。但是，我们不能用那个！在编程中，我们试图给每个字符只有一个含义。既然`x`已经是一个字母了，我们就要用别的了。在 JavaScript 中，我们使用星号(`*`)符号。下面是一些 JavaScript 中乘法的例子:

```
alert(1 * 3);
// Equal to 3
alert(9 * 8);
// Equal to 72
alert(-2.23 * 7.92);
// Equal to -17.6616
alert(-4 * -3);
// Equal to 12 
```

Enter fullscreen mode Exit fullscreen mode

### 分部

除法的工作方式也与纸上不同。虽然有一个代表除法(÷)的 [Unicode 符号](https://codetheweb.blog/newsletter)，但它不是你可以在键盘上轻松输入的东西，也不是那么常用。相反，我们使用斜线(`/`)符号来表示“除以”。下面是一些例子:

```
alert(1 / 2);
// Equal to 0.5
alert(20 / -4);
// Equal to -5
alert(0 / 5);
// Equal to 0
alert(64 / 0);
// Equal to Infinity 
```

Enter fullscreen mode Exit fullscreen mode

我只想强调列表中的最后一项:

```
alert(64 / 0);
// Equal to Infinity 
```

Enter fullscreen mode Exit fullscreen mode

在数学中，任何东西除以 0 都等于无穷大([解释此处](https://math.stackexchange.com/questions/1399099/why-is-any-number-divided-by-0-is-infinite/1399100))。但是，在 JavaScript 中它不能等于"`infinity`"——否则 JavaScript 会认为它是一个变量！所以，我们需要利用它来使它成为`Infinity`。这是 JavaScript 中的一个*特殊值*(**不是**一个变量)。它实际上没有很多用例，但它是上述语句的结果。

> 有趣的事实:JavaScript 中的[`Infinity - Infinity`*不等于`0`*！](https://stackoverflow.com/questions/27074158/infinity-infinity-nan)

### 表格

模是你以前可能没听说过的东西，不像上面的四则运算。简单来说，模就是两个数相除时的*余数*。这是通过在两个数字之间加一个`%`符号来实现的。例如:

```
alert(24 % 5);
// Equal to 4 
```

Enter fullscreen mode Exit fullscreen mode

让我们进一步分析一下。我们有数字`24`和`5`，由模数(`%`)符号分隔。这意味着，为了在头脑中计算答案，我们首先需要将`24`除以`5`(分成五组)。在这里，我们可以组成四组，每组五个。然而，我们仍然有多余的`4`作为余数。所以，答案是`4`。这里有一些其他的例子——如果你还是不明白，试着做上面的过程

```
alert(10 % 4);
// Equal to 2
alert(30 % 3);
// Equal to 0, 3 goes into 30 ten times without a remainder
alert(6 % 5);
// Equal to 1 
```

Enter fullscreen mode Exit fullscreen mode

## `Math`功能

除了上面的操作符，我们还可以使用一些函数来执行稍微高级一些的任务。这些函数一般遵循`Math.whateverTheFunctionIs()`的形式。这是因为`Math`是一个[对象](https://codetheweb.blog/newsletter)，包含许多不同的数学相关函数。我将在稍后的[文章](https://codetheweb.blog/newsletter)中谈论更多关于对象的内容，但是你现在真的不必担心它是如何工作的。只要使用我放在这里的语法就可以了。

### 的力量

我们使用`Math.pow(number,power)`函数来完成这项工作。例如，假设我们想对数字`5` :
求平方

```
alert(Math.pow(5,2));
// Equal to 25 
```

Enter fullscreen mode Exit fullscreen mode

等等什么？`alert`函数中的函数*？没错。这是因为`Math.pow`是 [*返回*某物](https://codetheweb.blog/2018/04/05/javascript-functions/#functions-with-an-output-functions-that-return-something)的函数。你可以把它想象成一个变量。因此，`x`不等于`25`，而是`Math.pow(5,2)`等于`25`。*

你也可以去更高的权力，如果你想(双关语)🙏**叹息** ):

```
alert(Math.pow(3,3)); // 3 to the power of 3
// Equal to 27
alert(Math.pow(2,10)); // 2 to the power of 10
// Equal to 1024 
```

Enter fullscreen mode Exit fullscreen mode

### 平方根&立方根

您可以使用`Math.sqrt()`和`Math.cbrt()`函数在 JavaScript 中计算立方根的平方。这里有一些你可以尝试的例子:

```
alert(Math.sqrt(25));
// Equal to 5
alert(Math.cbrt(8));
// Equal to 2 
```

Enter fullscreen mode Exit fullscreen mode

### 四舍五入

我们可以使用`Math.round()`函数将一个数字四舍五入为整数。下面是一些例子:

```
alert(Math.round(35.82562));
// Equal to 36
alert(Math.round(35.22353));
// Equal to 35
alert(Math.round(4));
// Equal to 4 (already rounded)
alert(Math.round(6.5));
// Equal to 7 (.5 rounds up) 
```

Enter fullscreen mode Exit fullscreen mode

如果数字的小数部分大于或等于`0.5`，它会将*向上舍入为*。否则将向下舍入。

### 特指向上或向下舍入

但是如果我们想明确地向上或向下取整呢？举个例子，如果之前我们想把`35.82562` *向下*四舍五入呢？这就是*地板*和*天花板*派上用场的地方。`Math.floor()`将数字*向下*舍入，而`Math.ceil()`将数字*向上*舍入。注意`Math.ceil(6)`甚至`Math.ceil(6.0)`都不会四舍五入到`7`。以下是一些例子:

```
alert(Math.floor(35.82562));
// Equal to 35
alert(Math.ceil(35.82562));
// Equal to 36
alert(Math.ceil(4.0));
// Equal to 4
alert(Math.ceil(4.01));
// Equal to 5
alert(Math.floor(0.99999));
// Equal to 0 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

今天到此为止！这只是涵盖了一些基本的数学运算，但还有更多。[点击此处](https://www.w3schools.com/js/js_math.asp)查看`Math`对象中所有函数的列表(以`Math.`开头的函数)。除了函数之外，`Math`对象还有一些静态值，比如`Math.PI`——它们也列在那个页面上。

希望你从这篇文章中学到了很多！如果你有，我会很高兴你在社交媒体上分享它。

还有，写这些文章花了我很多时间。到目前为止，我在这篇文章上花了 1 小时 45 分钟，有些文章我花了 3 个多小时！在我写这篇文章的时候，我目前在这里没有任何广告，所以我能得到所有这些内容支持的唯一方法是通过捐赠。

如果你想给我一点感谢，让我一整天都感觉很棒，如果你能给我买杯☕咖啡，我会非常感激。只有 4 美元，而且差别很大。事实上，目前我只差 49 美分来弥补我的网络编码成本，如果你能帮助我达到这个里程碑，那将会非常酷。好了，够了😉

如果你需要任何关于本文主题的帮助，或者想给出反馈(我喜欢一些非常棒的反馈)，请在下面的[评论中提出，或者通过现在可能在你屏幕右下角的酷酷的实时聊天工具。](https://codetheweb.blog/2018/03/18/javascript-math/#disqus_thread)

此外，如果您希望每周一次在您的收件箱中收到来自 Code web 和互联网的最新 Web 开发文章，请在下面输入您的电子邮件地址！你可以随时退订。

### [点击这里报名:D](https://codetheweb.blog/newsletter)

今天到此为止！下一次，我将在 JavaScript 中写关于 [for 循环和 while 循环的内容——它们真的很酷，因为你可以告诉浏览器一遍又一遍地重复你的代码(即使每次都有不同的值)！再见🚀](https://codetheweb.blog/2018/03/25/for-and-while-loops-javascript/)

* * *

> ### 想要更多这样的帖子？[注册我的简讯](https://codetheweb.blog/newsletter/)。
> 
> 我一直在努力学习这些教程，但是到目前为止还没有赚到任何钱，所以如果你注册的话，我会非常感激；)

这篇文章最初发表在 [Code The Web](https://codetheweb.blog) 上，这是一个有趣且易于理解的博客，帮助你学习 Web 开发🚀
学:[HTML](https://codetheweb.blog/learn/html/)|[CSS](https://codetheweb.blog/learn/css/)|[JavaScript](https://codetheweb.blog/learn/javascript/)
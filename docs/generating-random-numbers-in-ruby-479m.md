# 在 Ruby 中生成随机数

> 原文：<https://dev.to/appsignal/generating-random-numbers-in-ruby-479m>

随机数有多种用途，如游戏、加密和建筑模拟。从技术上讲，计算机不能纯粹通过计算生成随机数。在任何确定性设备上根本不可能产生真正的随机数。你所能期望的最好结果是伪随机数，一串看起来像是随机产生的数字。

在本文中，我们将看看在 Ruby 中生成随机数的各种方法。

## 用核#rand 生成随机数

首先，让我们用 [`rand`](http://ruby-doc.org/core-2.5.1/Kernel.html#method-i-rand) 方法生成随机数。当不带参数调用该方法时，它返回一个大于或等于 0.0 且小于 1.0 的浮点数。

```
rand()
> 0.7308136972953823 
```

Enter fullscreen mode Exit fullscreen mode

要获得一个整数，需要将一个整数传递给函数。该函数将返回一个大于或等于 0 且小于传递给该函数的整数的随机整数值。每次运行下面的代码，你都会得到一个介于 0 和 7 之间的数字。

```
rand(8)
> 5 
```

Enter fullscreen mode Exit fullscreen mode

对于特定范围内的随机数，将[范围](https://ruby-doc.org/core-2.2.0/Range.html)传递给`rand`。

以下使用包含*和*的范围来生成从下限(`1`)到上限(`10`)的随机数。

```
rand(1..10)
> 6 
```

Enter fullscreen mode Exit fullscreen mode

下一个例子使用一个*非包含*范围来生成从下限到(但不包括)上限的随机数。

```
rand(1...10)
> 9 
```

Enter fullscreen mode Exit fullscreen mode

该范围也可以在浮点值之间。

```
rand(1.5..3.0)
> 1.7494305393711571 
```

Enter fullscreen mode Exit fullscreen mode

您也可以通过`rand`使用负范围限制。

```
rand(-5..-1)
> -5 
```

Enter fullscreen mode Exit fullscreen mode

传入单个负数可能会产生令人惊讶的结果，如下所示。

```
rand(-100)
> 94

rand(-0.5)
> 0.7692627344737486 
```

Enter fullscreen mode Exit fullscreen mode

这是因为对于传递给`rand`的参数`n`，`rand`返回从 0 到(但不包括)`n.to_i.abs`的随机数。对于上面的例子，`(-100).to_i.abs`是`100`，而`(-0.5).to_i.abs`是`0`，因此产生随机数。

调用`rand(0)`类似于调用`rand()`。你会得到 0.0 到 1.0 之间的随机数(不含)。

## 用核#srand 生成可再现序列

在进入下一个生成随机数的方法之前，我们先来看看`srand`函数。

[`Kernel#srand`](http://ruby-doc.org/core-2.5.1/Kernel.html#method-i-srand) 为`Kernel#rand`设置种子。我们可以用它在程序的不同运行之间生成可重复的随机数序列。

为了理解这意味着什么，我们首先需要理解随机数是如何产生的。

## 从种子生成“随机”数

如前所述，计算机不会纯粹通过计算生成真正的随机数。他们所做的是生成一系列看似随机的数字。为了做到这一点，计算机从一个种子号开始，通过某种算法运行，然后抛出一个看似随机的输出。

种子号由计算机使用不同元素的组合来生成，例如时间戳、程序的进程 ID 等。因为这些元素对于生成随机数的每个请求都不同，所以种子号总是不同的，这将产生不同的数字序列，从而产生随机数输出。如果你用相同的种子运行算法，那么你每次都会得到相同的数字序列。这是`Kernel#srand`允许我们做的。

`srand`通常用于测试。它可以方便地测试你的应用程序中处理随机性的代码，这些代码的值是随机的，但仍然有足够的可预测性来测试。它还可以帮助隔离或复制错误。

下面我们使用`srand`来设置种子，然后首先调用`rand`来产生几个单独的随机数，然后产生几个随机数序列。

```
srand(777)

rand()
> 0.152663734901322

rand()
> 0.3023566097075212

10.times.map { rand(10) }
> [7, 1, 7, 4, 7, 9, 8, 7, 2, 0]

10.times.map { rand(10) }
> [1, 2, 4, 5, 7, 1, 7, 2, 2, 7] 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用相同的种子再次运行`srand`,并进行我们之前进行的相同调用，您将看到我们得到相同的随机数。

```
srand(777)

rand()
> 0.152663734901322

rand()
> 0.3023566097075212

10.times.map { rand(10) }
> [7, 1, 7, 4, 7, 9, 8, 7, 2, 0]

10.times.map { rand(10) }
> [1, 2, 4, 5, 7, 1, 7, 2, 2, 7] 
```

Enter fullscreen mode Exit fullscreen mode

## 用随机数生成随机数

你也可以用 [Random](http://ruby-doc.org/core-2.1.3/Random.html) 类生成随机数。

类方法`rand`提供了`Kernel#rand`的基本功能以及更好的浮点值处理。

```
Random.rand(1...10)
> 5 
```

Enter fullscreen mode Exit fullscreen mode

与`Kernel#rand`不同，如果`Random.rand`被赋予一个否定或`0`参数，它会引发一个[参数错误](http://ruby-doc.org/core-2.1.3/ArgumentError.html)。

## 基于正态分布生成随机数

在现实世界中，许多事情往往遵循一个[正态分布](https://en.wikipedia.org/wiki/Normal_distribution)。如果你有一系列的值，你很难得到所有值的平均分布。大多数情况下，大多数数据倾向于落在较小的范围内，较小的百分比落在较大的范围内。我们以一个成年男性的身高为例。记录的最短高度是 54.6 厘米(21.5 英寸)，最高高度是 267 厘米(8 英尺 9 英寸)。如果您想要生成数据来模拟人群中男性的身高，您可能不想使用带有这些限制的`rand`。你不会希望得到一个 8 英尺 9 英寸高的男人的概率和得到一个 6 英尺高的男人的概率相同，因为后者更常见。

遵循正态分布的其他例子有:

*   测量误差
*   血压
*   考试成绩
*   成年男性/女性的体重

要为这样的用例生成更好的随机数，您可以使用 [`rubystats`](https://rubygems.org/gems/rubystats) gem。

```
$ gem install rubystats 
```

Enter fullscreen mode Exit fullscreen mode

```
require 'rubystats'

adult_male_height = Rubystats::NormalDistribution.new(178, 10)
sample = 50.times.map { adult_male_height.rng.round(1) }

> [183.2, 169.5, 189.7, 171.9, 176.0, 179.3, 189.3, 175.3, 188.3, 190.0, 185.5, 182.8, 187.2, 191.6, 185.4, 178.4, 187.1, 183.3, 189.6, 179.7, 172.7, 174.4, 153.8, 197.4, 176.0, 174.6, 181.1, 182.0, 204.7, 185.2, 175.9, 167.7, 160.6, 170.5, 169.3, 160.6, 165.6, 166.4, 182.6, 179.7, 183.1, 171.9, 185.4, 175.4, 179.7, 176.9, 160.6, 173.8, 181.9, 190.2] 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们将男性的平均身高(178 厘米)和 10 厘米的标准偏差传递给`NormalDistribution.new`，然后生成 50 个符合该正态分布的值。如果你对数学感兴趣，这篇文章可能会让你感兴趣。

## 随机综述

我们的讨论到此结束。我们用`rand`、`srand`、`Random`和`Rubystats`讲述了在 Ruby 中创建“随机”数字的几种不同方法。我们还简要地谈到了“随机”数是如何产生的，并研究了确定性设备不能产生真正的随机数的原因。我们希望您对此感兴趣。如果您对我们所涵盖的内容有任何意见或问题，请联系我们 [@AppSignal](https://twitter.com/appsignal) 。您也可以向我们发送您希望涵盖的主题的请求。

本文由客座作者[乔伊斯·埃切萨](http://echessa.com)撰写。Joyce 是一名全栈开发人员，热爱文字和文学。
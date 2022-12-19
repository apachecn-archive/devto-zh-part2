# 梯形法则积分的函数方法

> 原文：<https://dev.to/juanroalvarado/functional-approach-to-trapezoidal-rule-integration-m68>

## 什么是数值方法:

无论我们把计算机描述得多么强大，它们都是有限的。它们的局限性阻止我们直接评估数学方程，这就是数值方法发挥作用的地方。我们将数值方法定义为数学模型，它使用计算机固有的解决问题的方法来逼近更复杂方程的解。

## 利用梯形法则进行积分:

维基百科将积分描述为“以一种可以描述位移、面积、体积和其他通过组合无穷小的数据而产生的概念的方式将数字分配给函数。”
[![Integral](../Images/7a1b4bc9ed6cbc7e12fe6446867db60d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TO4y5QBJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikimedia.org/api/rest_v1/media/math/render/svg/ac02adeed584466d53dee65f3228ad66939eb58b) 
它们是微积分中引入的基本概念之一，在整个工程和科学世界中普遍使用。

梯形法则是一种通过函数创建梯形分区来逼近定积分的方法。取分区的累积面积，得到近似的积分。

[![Trapezoidal rule](../Images/725a6474c070a9eafeba52d90bb5fc79.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q0XQHw5M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/daZ4D.png)

## 函数逼近数值方法:

为了计算下面的方法，我们将使用函数式编程语言 Haskell。
你可以在这里找到下面代码[的源代码。](https://github.com/Juanroalvarado/Integratehaskell)

首先，我们将定义要积分的函数。

```
func x = sin (x) 
```

在我们的例子中，我们将从 0 到π对 sin 函数进行积分。

现在我们开始定义我们的方法。

由于 Haskell 是一种惰性语言，我们必须首先定义函数 integrate 的参数。

```
integrate :: Float -> Float -> Float -> Float -> Float -> Float 
```

该函数将采用:

1.  初始值
2.  所需的迭代或分区的数量
3.  下限
4.  下限加增量
5.  上限

由于 Haskell 是一种函数式语言，我们将递归计算每个梯形分区。

我们必须首先添加一个方法，让函数知道何时计算我们的最终值，这是通过将完整的总和乘以一个增量来实现的。停止我们的递归。

```
integrate x n lim0 newlim0 lim1 
    | newlim0 >= lim1 = (x * delta)
    where delta = ((lim1-lim0)/n) 
```

我们检查“newlim0 ”,它将等于初始限制加上增量，当我们到达上限时停止递归。

```
integrate x n lim0 newlim0 lim1 
    | newlim0 >= lim1 = (x * delta)
    |  x == 0 = integrate (x +  (func lim0 + func lim1)/2) n lim0 (newlim0+delta) lim1
    where delta = ((lim1-lim0)/n) 
```

我们接着做第一道算术题，如果我们看这个公式，我们将计算梯形的面积。当我们的初始值为 0 时添加它。递归调用没有新值和新限制的 integrate。

```
integrate x n lim0 newlim0 lim1 
    | newlim0 >= lim1 = (x * delta)
    |  x == 0 = integrate (x +  (func lim0 + func lim1)/2) n lim0 (newlim0+delta) lim1
    | otherwise = integrate (x + func newlim0) n lim0 (newlim0+delta) lim1
    where delta = ((lim1-lim0)/n) 
```

最后，在任何其他情况下，我们将通过将评估的函数与我们的 newlim0 相加来计算总和。

我们完整的压缩代码可以在我的 GitHub 上找到。
[https://github.com/Juanroalvarado/Integratehaskell](https://github.com/Juanroalvarado/Integratehaskell)

### 我们在哪里使用积分？

[![Integral visual](../Images/78ba2ed9c4c70837f96a5c49262eac35.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8qagCxau--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/9/9f/Integral_example.svg)

积分随处可见！特别是处理物理的时候！

例如，假设我们镇上有一个水箱。我们想知道它是否能为这个城镇提供足够一周的水。

计算油箱面积的积分就能得到容积。现在我们能够知道它能容纳和提供多少水。

## 结论

通常我们无法计算一个精确函数的积分。或者精确地计算一个方法，使用数值方法来逼近一个值将是得到那个值的更有效和更快速的方法。

数值方法的函数式编程方法提供了一种直观的方法来计算复杂的方程，而没有传统编程方法的麻烦。

#### 参考文献:

[https://en.wikipedia.org/wiki/Trapezoidal_rule](https://en.wikipedia.org/wiki/Trapezoidal_rule)
T3】http://learnyouahaskell.com/introductionT5[https://en.wikipedia.org/wiki/Numerical_method](https://en.wikipedia.org/wiki/Numerical_method)
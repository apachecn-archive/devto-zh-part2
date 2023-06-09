# 计算流式数据的移动平均值

> 原文：<https://dev.to/nestedsoftware/calculating-a-moving-average-on-streaming-data-5a7k>

最近，我需要计算一些关于输入数据流的统计数据(平均值和标准偏差)。我做了一些研究，这篇文章就是结果。我要把它分成几部分。第一部分是关于如何逐步计算平均值。第二部分将讲述如何用标准差做同样的事情。第三部分将是关于[指数移动平均线](https://dev.to/nestedsoftware/exponential-moving-average-on-streaming-data-4hhl)，也被称为低通滤波器。

> 人们通常所说的“平均值”在统计学上更专业地称为“算术平均值”。对于本文，术语“平均”和“均值”可以互换。

计算我们在学校学到的一组数据的平均值的通常方法是将所有值相加(总计)，然后除以值的数量(计数):

[![mean = total/count](img/4574f27233692c5ef5cf339fca567fdc.png "mean = total / count")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ChKRp_Ij--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m6gaye6s0vuyd1b78g8c.png)

下面是描述我刚才写的内容的数学符号:

[![arithmetic mean formula](img/6a9e9702b8638cb8fb25ae6816ae8192.png "arithmetic mean formula")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2qmJ3aEy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o4j4e8jumxflb0jx8uwo.png)

下面是一个简单的 javascript 函数，它使用这种简单的方法来获得平均值:

```
const simpleMean = values => {
    validate(values)

    const sum = values.reduce((a,b)=>a+b, 0)
    const mean = sum/values.length
    return mean
}

const validate = values =>  {
    if (!values || values.length == 0) {
        throw new Error('Mean is undefined')
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这种逻辑很好，但在实践中确实有一些限制:

*   我们累积了一个潜在的大和，当使用[浮点](https://en.wikipedia.org/wiki/Floating-point_arithmetic)类型时，这会导致精度和溢出问题。
*   在进行计算之前，我们需要有所有可用的数据。

这两个问题都可以通过增量方法来解决，在这种方法中，我们对出现的每个新值调整平均值。我将首先展示如何用一些数学推导出这个公式，然后展示一个 JavaScript 实现。

> 数学中有两个符号经常用来表示[的意思是](https://en.wikipedia.org/wiki/Mean)。 **`σ`** ，小写希腊字母 sigma，指*人口意为*。这是整个人口的平均值，所有可能的值。某次考试所有成绩的平均值就是一个例子。
> 
> **`x̄`** ，读作 x-bar，指*样意为*。这是总体样本值的平均值。你可以在全国范围内随机抽取一些人来计算人口的平均身高，但是测量整个国家每个人的身高是不切实际的。当然，当使用样本时，最好尽可能接近样本所代表的总体的平均值。
> 
> 我决定在本文中使用 sample 符号来表示我们计算的平均值可以基于一个样本。

好，让我们从之前看到的平均值公式开始:

[![arithmetic mean formula](img/6a9e9702b8638cb8fb25ae6816ae8192.png "arithmetic mean formula")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2qmJ3aEy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o4j4e8jumxflb0jx8uwo.png)

让我们拆分总和，这样我们首先将前 n-1 个值相加，然后我们将最后一个值 x <sub>n</sub> 相加。

[![arithmetic mean split](img/6930a6b1bb139200323c4b7e6a12bfdb.png "arithmetic mean split")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O0WyDquH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yclplm85jp0x9z4m9wnh.png)

我们知道平均值=总数/计数:

[![average of first n-1 values](img/56f0e58605658ae1533db2b73ff30356.png "average of first n-1 values")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--75GYK9tL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s3wonz2r1rg3k6e79cq9.png)

让我们稍微重新排列一下:

[![sum to n-1 = mean of n-1 values * n-1](img/593dc78185c7bf8e590045a8c6d05987.png "sum to n-1 = mean of n-1 values * n-1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yh_7MaYZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fj6tmxj7n3r1golo7nky.png)

以下是将上述替换应用于前 n-1 个值的结果:

[![result of substitution](img/f8d6826e059e9f99d17410300fd6592e.png "result of substitution")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W3X5VhL2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e9kkjin6zcsedxfvhy6e.png)

让我们扩展一下:

[![expansion](img/3efdaeb90c32167c48695943b68c8fba.png "expansion")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YQRhQujz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vniaouvrqc3jp9br8guy.png)

稍微重新排列一下，我们得到:

[![rearrange expression](img/461b1b3bce84bf21b1a3e1b9e36dd2a6.png "rearrange expression")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sj9cbC3a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yirl7hs5nibjrmg18hxh.png)

我们可以取消第一部分中的`n`，以获得最终结果:

[![cancel out n's](img/f31697c76a308a985346a80299a3af29.png "cancel out n's")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TBSUP3lH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yhd6bgnjexpbg51gx5l3.png)

这一切到底意味着什么？我们现在有一个递归关系，它定义了第 n 个值的平均值，如下所示:对前 n-1 个值的平均值加上一个微分。每次我们增加一个新值，我们所要做的就是计算这个差值，并把它加到之前的平均值上。这现在变成了新的平均值。

下面是这个想法的一个简单实现:

```
class MovingAverageCalculator {
    constructor() {
        this.count = 0
        this._mean = 0
    }

    update(newValue) {
        this.count++

        const differential = (newValue - this._mean) / this.count

        const newMean = this._mean + differential

        this._mean = newMean
    }

    get mean() {
        this.validate()
        return this._mean
    }

    validate() {
        if (this.count == 0) {
            throw new Error('Mean is undefined')
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，每当我们用一个新值调用`update`时，我们增加计数并计算我们的微分。`newMean`是前一个平均值加到这个差值上。这就是我们下次给`update`打电话时要用到的平均值。

下面是两种方法的简单对比:

```
console.log('simple mean = ' + simpleMean([1,2,3]))

const calc = new MovingAverageCalculator()
calc.update(1)
calc.update(2)
calc.update(3)
console.log('moving average mean = ' + calc.mean) 
```

Enter fullscreen mode Exit fullscreen mode

结果在意料之中:

```
C:\dev\>node RunningMean.js
simple mean = 2
moving average mean = 2 
```

Enter fullscreen mode Exit fullscreen mode

当然，还有许多其他类型的[移动平均线](https://en.wikipedia.org/wiki/Moving_average)是可能的，但是如果你只是想要一个累积移动平均线，这个逻辑工作得很好:它很简单，你可以将它应用到一个流数据集，并且它回避了天真的方法可能发生的精度和溢出问题。

> 在结束之前，我想用我们最后的结果再推导一个恒等式。我们现在不需要它，但是我们将在下一篇文章中使用它。
> 
> 我们将从均值的递推关系开始:
> 
> [![recurrence relation for mean](img/f31697c76a308a985346a80299a3af29.png "recurrence relation for mean")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TBSUP3lH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yhd6bgnjexpbg51gx5l3.png)
> 
> 让我们从两边减去右边的第一项，得到我们的微分值:
> 
> [![subtract](img/42d4d00d3b16bd3f2384b2083d92e847.png "subtract")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vT9KXsGo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8u4lfssnd1uuj7a85axp.png)
> 
> 现在让我们乘以 n:
> 
> [![multiply by n](img/6d3790a29333eb7279dfae82db7d7585.png "multiply by n")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f3CICilB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ogy6x57jlycck2ardfmy.png)
> 
> 让我们把两边都乘以-1:
> 
> [![multiply by -1](img/705ee9fedb3cbdf9d56f497ae33dffc1.png "multiply by -1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3uIBnOVm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qts7u9mqpw2fkt1kdk68.png)
> 
> 最后，让我们通过两边乘以-1:
> 
> [![multiply -1 through](img/02387566a603476e52fb9c1e17ee2338.png "multiply -1 through")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eQqHMXlK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8dbnrfwyumt3xbph6o31.png)
> 
> 我们现在只保留这个恒等式，但它在第 2 部分会很有用，在第 2 部分中，我们将推导出增量计算方差和标准差的公式。

相关:

*   [计算流数据的标准偏差](https://dev.to/nestedsoftware/calculating-standard-deviation-on-streaming-data-253l)
*   [流数据的指数移动平均](https://dev.to/nestedsoftware/exponential-moving-average-on-streaming-data-4hhl)
*   [标准差的几何形状](https://dev.to/nestedsoftware/the-geometry-of-standard-deviation--3m3o)
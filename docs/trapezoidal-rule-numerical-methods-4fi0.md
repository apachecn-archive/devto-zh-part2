# 梯形法则:数值方法

> 原文：<https://dev.to/jbagur/trapezoidal-rule-numerical-methods-4fi0>

# 简介:

数值方法是一种工具，它使用代数和算术技术，以近似的方式求解复杂的方程组。有些方程很难用解析方法求解，因此可以用算术运算来重新表述这些方程，从而得到它们的近似解。数值方法有很多应用，我们可以求解积分，导数，近似值。在积分中，有几种算法，为了给出这些算法的近似解，在这种情况下我们将在 scala 中实现梯形法则。在数学上，梯形法则是一种数值积分方法，即近似计算定积分值的方法。

# 梯形法则:

在数学上，梯形法则是一种数值积分方法，即近似计算定积分值的方法。
[![i2](img/4256bc3d28d3e0d1ea207d995c5222ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kP3tYBfT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/dzbxcbntl/image/upload/v1526755300/Selection_004.png) 
该规则基于通过点(a，f (a))和(b，f (b))的线性函数的积分值来近似 f (x)的积分值。它的积分等于线性函数图形下梯形的面积。接下来:
[![i3](img/4688dceaf97ff0f09a1d889eddc7eba1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MKmLum13--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/dzbxcbntl/image/upload/v1526755300/Selection_005.png)

错误的是:
[![i4](img/55f7fa158303a5f0b30665831c8c62de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NCS-5YbV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/dzbxcbntl/image/upload/v1526755300/Selection_006.png)

Xi 是介于 a 和 b 之间的一个数

[![i1](img/9ec582747ee37754330e6d4d7f9f7ee5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_ynRHX8v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/d/dd/Trapezoidal_rule_illustration.png)

# 实现

梯形规则的实现是在 Scala 中完成的，你可以在这里找到完整的实现。

### 功能进行整合

首先，我们创建要积分的函数，在这种情况下，我们将使用函数 sin:

```
 //Fuction SIN
  def function(x:Double) = Math.sin(x) 
```

Enter fullscreen mode Exit fullscreen mode

### 梯形法则实现

三参数函数:初始边值、最终边值和区间长度:

```
 //Trapezoid Method
  def IntegralT(a:Double, b:Double, n:Int)={ 
```

Enter fullscreen mode Exit fullscreen mode

计算条带数，n =(最终边界值-最终边界值)/间隔长度。这将用于计算每次迭代的近似值:

```
 var interval = ((b-a)/n) 
```

Enter fullscreen mode Exit fullscreen mode

设定&辛:

```
 var xi = a
    var xin = b 
```

Enter fullscreen mode Exit fullscreen mode

计算 x0 = y x1 = b 的牛顿插值公式，设置和的初始值:

```
 var left = (interval)/(2.0)*(function(xi)+function(xin))
    var sum = 0.0 
```

Enter fullscreen mode Exit fullscreen mode

在循环中执行以下操作
sum equals: sum + f(xi)
重新计算 xi、Xi+区间

```
 for( i <- 2 to n){
      sum += function(xi)
      xi = xi + interval
      } 
```

Enter fullscreen mode Exit fullscreen mode

最后我们返回条纹数的总和，牛顿插值公式和求和:

```
 left + interval * sum 
```

Enter fullscreen mode Exit fullscreen mode

为了执行，我们只调用函数，使用已建立的参数，并打印结果:

```
 def main(args: Array[String]): Unit = {
  println("Trapezoidal integration") 
  //set integrate limits, and n of iterations
  var integral = IntegralT(0,math.Pi,20)
  println("Result: "+integral)
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们用不同的 n 执行程序:10，20，100 来证明，你用的梯形越多，积分就越精确

```
Result: 1.8864429855731812
Result: 1.971313304401783
Result: 1.9988487057878095 
```

Enter fullscreen mode Exit fullscreen mode

完整代码:

```
package Main

object Main {
  def main(args: Array[String]): Unit = {
  println("Trapezoidal integration") 
  //set integrate limits, and n of iterations
  var integral = IntegralT(0,math.Pi,20)
  println("Result: "+integral)
  }
  //Fuction SIN
  def function(x:Double) = Math.sin(x)

  //Trapezoid Method
  def IntegralT(a:Double, b:Double, n:Int)={
    var interval = ((b-a)/n)
    var xi = a
    var xin = b
    var left = (interval)/(2.0)*(function(xi)+function(xin))
    var sum = 0.0
    for( i <- 2 to n){
      sum += function(xi)
      xi = xi + interval
      }
    left + interval * sum
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

# 申请

积分在现实生活中有很多应用，从测量容器的体积，到速度的不同应用。积分的另一个用途是用于经济研究。

## 经济过剩

[![i5](img/3e78402cb1d74823ff53f4f7f0e94dc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9UIcauJV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/d/d7/Economic-surpluses.svg) 
消费者剩余是我们从某种商品或服务中获得的总效用与其市场价格之间的差额。

消费者的剩余来自收益递减规律。这意味着第一个收购的单位价值很高，但随着我们收购更多的单位，我们的价值就会下降。然而，我们为任何单位支付的价格总是一样的:市场价格。这样，我们在获得的第一个单位中享有正盈余，直到我们到达盈余为零的最后一个单位。

用图形术语来说，消费者剩余是指市场需求曲线以下和价格线以上的区域。需求曲线衡量消费者愿意为每单位消费支付的金额。然后，需求曲线下的总面积反映了商品或服务消费的总效用。如果从这个面积中减去每个单位支付的价格，就获得了消费者的剩余。

从消费者剩余到供给曲线的另一部分被称为生产者剩余。

### 消费者剩余

它代表愿意支付高于均衡价格的消费者的总收益，缩写为 EC。
[![i6](img/b95e1ba3f370b280956b8c32eb22971c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RLXAMIbx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/dzbxcbntl/image/upload/v1526762212/Screenshot_from_2018-05-19_14-35-05.png) 
有关该应用程序的更多信息，请点击下面的[链接](https://en.wikipedia.org/wiki/Economic_surplus)。

# 参考书目:

[https://www.intmath.com/integration/5-trapezoidal-rule.php](https://www.intmath.com/integration/5-trapezoidal-rule.php)
[http://www.mathwords.com/t/trapezoid_rule.htm](http://www.mathwords.com/t/trapezoid_rule.htm)
[https://en.wikipedia.org/wiki/Trapezoidal_rule](https://en.wikipedia.org/wiki/Trapezoidal_rule)
[http://math . slu . edu/~ may/excelcalcul/sec-7-8-businessapplicationintegral . html](http://math.slu.edu/%7Emay/ExcelCalculus/sec-7-8-BusinessApplicationsIntegral.html)
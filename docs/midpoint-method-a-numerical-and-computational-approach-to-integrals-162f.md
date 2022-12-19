# 中点法:积分的数值和计算方法

> 原文：<https://dev.to/enriquezs94/midpoint-method-a-numerical-and-computational-approach-to-integrals-162f>

## 简介:

数值方法是用来解决某些数学问题的重要工具。这些问题往往非常繁重，更适合计算机的处理能力。积分是许多可以用数值方法解决的数学问题之一。数值求解积分有许多算法，中点法就是其中之一。这种方法是在一条曲线下画几个矩形，然后将它们的面积相加，得出曲线下面积的估计值。数值方法的目的是使用近似法找到问题的解决方案。

## 中点法:

我选择了中点法进行数值积分，并在 F#中实现了它，如果你有兴趣尝试我的代码，你可以在这里找到它。给定一个函数，中点法将创建 N 个矩形来近似函数曲线下的面积。更多的矩形意味着更精确的近似。

在这个例子中，我使用函数 sin(x)。
 `let f x : float =
sin x` 
中点公式需要一个起点和一个终点。给定这些值和矩形的数量，我们可以计算 x 的变化。
 `let h = (b - a) / (float N)` 
一旦我们计算出 x 的变化，我们就进入循环，计算每个矩形的面积，一旦我们将它们加在一起，我们就获得曲线下的面积。
 `for i = 0 to N-1 do
let x = (a+h/2.0)
let funcEval = f(x + float i * h)
midpoint <- midpoint + funcEval * h` 
在这种情况下，我们计算 sin(x)从 0 到 pi (3.14159)的积分，并将其分成 100 个矩形，该积分的结果是 2.000082，考虑到实际结果是 2，这是一个很好的近似值。

## 应用:

数值积分在现实世界中有许多应用，其中一个应用是在汽车工业中实施安全措施。当汽车停止时，如果汽车突然刹车，随着时间的推移加速曲线可能是陡峭的，或者如果汽车以较慢的速度减速，曲线可能是平缓的。不管减速需要多长时间，曲线下的面积总是相同的，因为两种情况下的速度相同。可以使用数值积分来近似所述曲线下的面积，并找出在一段时间内经历了多少减速。以较低的速度刹车可能需要更长的时间，但乘客不会遇到危险的高峰，而突然停车更快，但风险更高。如果没有计算机，这样做将会更加困难，而且容易出错。使用数值方法来解决这个问题可以在短时间内产生非常精确的近似值。这可以帮助汽车制造商进行更好更快的测试，从而产生新的和改进的安全措施，挽救生命。

## 参考书目:

[https://www . encyclopedia . com/computing/dictionary-thesaurus-pictures-and-press-releases/numerical-methods](https://www.encyclopedia.com/computing/dictionaries-thesauruses-pictures-and-press-releases/numerical-methods)

[https://www . Whitman . edu/mathematics/calculus _ online/section 09.08 . html](https://www.whitman.edu/mathematics/calculus_online/section09.08.html)
https://www . int math . com/applications-integration/hic-head-injury-criterion . PHP
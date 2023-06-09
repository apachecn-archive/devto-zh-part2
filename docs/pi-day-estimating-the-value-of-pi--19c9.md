# 圆周率日:估算圆周率的值

> 原文：<https://dev.to/lefebvre/pi-day-estimating-the-value-of-pi--19c9>

今天是圆周率日(3 月 14 日)，你可能想知道的一个问题是“圆周率是如何计算的？”

测量圆可以给出一个粗略的估计，但是无穷级数可能是一个更好更常用的方法。正如在 MathCareers.org.uk 计算圆周率时所描述的，尼拉坎塔系列工作得相当好。它的公式如下所示:

尼拉坎塔系列:π= 3+4/(2×3×4)—4/(4×5×6)+4/(6×7×8)—4/(8×9×10)+…

你应该能从中找出规律。将它转换成 Xojo 代码是一个很小的飞跃。下面是使用尼拉坎塔级数计算圆周率的代码:

```
Dim even As Boolean = True
Dim calc As Double
Dim calcStart As Int64

Dim iterations As Integer
While iterations < 1000
  iterations = iterations + 1
  calcStart = calcStart + 2
  calc = 4.0 / (calcStart * (calcStart + 1) * (calcStart + 2))

  If even Then
    Pi = Pi + calc
  Else
    Pi = Pi - calc
  End If

  even = Not even
Wend 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个可以在 Xojo 中运行的完整项目: [CalcPi 项目](http://cdn.xojo.com/BlogExamples/CalcPi.xojo_binary_project)

上面的代码几乎立刻就正确地计算出了圆周率的前 9 位数字。不幸的是，使用大多数编程语言中使用的标准双精度浮点数据类型来计算更多位数的圆周率并不实际。
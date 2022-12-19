# 圆周率日:用圆估计圆周率

> 原文：<https://dev.to/lefebvre/pi-day-estimate-pi-with-a-circle-3a5o>

离圆周率日(3 月 14 日)只有一周了。圆周率是一个有趣的数字，这里有一个有趣的事情可以尝试:用线段画一个圆。其工作方式是，你首先只用三条线段画一个圆，这实际上是画一个三角形。四条线段画一个正方形。五段开始呈现“圆形”形状。随着分段数量的增加(达到约 150)，形状变得越来越圆。

这里我的意思是:
[![Pi Animation](../Images/23f1526afa16b0faa387655bb1367036.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UbhqJvLX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.xojo.com/wp-content/uploads/2015/03/circle.gift1466486449161ampwidth480ampheight360)

为了制作一个这样的应用程序，你需要知道一点点三角学，这样你就可以使用正弦和余弦。下面的图表可能有助于更好地解释这个问题:

[![Pi calc](../Images/7aab19f59b39d9f044525f0d6357828c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xvW0BCVS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2015/03/2015-03-06_17-26-38-thumb-400x309-593.pngt1466486449161ampwidth400ampheight309)

本质上，代码计算从开始位置到结束位置的直线。通过使用余弦得到图上的 X 值来计算终点位置。正弦用于获取图表上的 Y 值。

代码通过线段长度循环通过圆周。这是进行计算和绘图的相关 Xojo 代码:

```
// Rough value for PI (π)
Const kPi As Double = 3.141592654

// The cicumference of a circle is 2πr
Dim circumference As Double = 2 * kPi

// Divide the circumference into small line segments based on the number
// of steps.
// The resolution of the circle improves as lineLength gets small.
Dim lineLength As Double
lineLength = circumference / mLines

// Use available space in the Canvas to draw the circle
Dim radius As Integer
radius = Min(g.Height / 2, g.Width / 2)

Dim xOffset As Integer = radius // x center of circle
Dim yOffset As Integer = radius // y center of circle

// Initial color and pensize
g.ForeColor = &c0000ff
g.PenWidth = 1

// Step through the circle by lineLength, drawing
// a line at each radian.
Dim x As Double
Dim y As Double
Dim lastX As Double = Cos(0) * radius // Previous point to draw from
Dim lastY As Double = Sin(0) * radius // Previous point to draw from

For radian As Double = 0 To circumference Step lineLength
  // Cosine gives you the x position of the point on the circle
  // starting from the center x position.
  x = Cos(radian) * radius

  // Sine gives you the y position of the point on the circle
  // starting from the center y position.
  y = Sin(radian) * radius

  g.DrawLine(xOffset + lastX, yOffset + lastY, xOffset + x, yOffset + y)

  LastX = x
  LastY = y
Next

// Draw line back to start of circle so that circle is always completed
// without gaps.
g.DrawLine(xOffset + LastX, yOffset + LastY, xOffset + Cos(0) * radius, yOffset + Sin(0) * radius) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想自己尝试一下，完整的项目是免费的 Xojo 下载中包含的示例的一部分:Examples/Misc/PiDay
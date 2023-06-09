# 以编程方式实现自动布局

> 原文：<https://dev.to/abelagoi/programmatically-approach-auto-layout--3998>

布局是事物的各个部分被安排或布置的方式。在 swift 中设计用户界面时，我们必须考虑元素是如何排列的，以及在不同的屏幕尺寸上它将如何重新排列。为了对用户界面进行布局，使其能够适应 swift 中基于不同屏幕尺寸的变化，我们必须使用 autolayout。

> 自动布局简单的意思是我们描述界面应该在的位置，然后系统会自动为我们定位界面。
> 自动布局是一个基于约束的描述系统，由此两个视图之间的关系可以表示为一个线性方程

```
A1 = (M * A2) + C
where the A1 and A2 refer to views' attribute, 
M refer to multiplier and 
C refer to as a constant 
```

约束允许我们在单个视图的属性之间或者与另一个视图的属性之间建立关系。

### 什么是属性？

属性是视图对齐矩形上的一个位置。在上面的等式中，A1 将是视图 1 上的属性，A2 将是视图 2 上的属性。一个视图属性用于建立与另一个视图属性的关系。第一组属性对应于视图对齐矩形的边缘，它们是`top`、`bottom`、`right`和`left`属性。

[![](img/f34a31eb2cce0b383ed09920083fe281.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MYw_BEBF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AxgIcNDwXpd9oKXbPtRNiXg.png)

第二组属性是前导、尾随、左和右属性。

[![](img/51dd026218bc18e901d941325f8ce02a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_c_BSzxE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AE3v9wIwNoj1X_wmCoryumA.png)

下一组属性是高度和宽度，高度和宽度属性允许我们创建与我们的视图或另一个视图的高度和宽度的关系。

[![](img/218e252fa46a8db78d15dd14a548cace.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yKYlz7pc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A1NsvVc2DGtHDnryiGQYR9w.png)

下一组是 CenterX 和 CenterY 属性，用于对齐视图的水平轴和垂直轴。

[![](img/9957361b0f50566a6129df30f7af0dbb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n9sotuH6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AavlfslqOMkNH_PrpO6COpQ.png)

我们还有基线属性，第一个基线和最后一个基线。基线就像文本所在的假想线，如果有多条基线，第一条是第一条基线，最后一条是最后一条基线。

每个视图都包含边距，允许您修改视图边缘和添加的任何子视图之间的间距，控制这种关系的属性主要是上边距、右边距、下边距和左边距。除了边距属性，我们还有前导边距、尾随边距、带边距的 centerX、带边距的 centerY。我们也没有一个属性允许我们不在其他视图上使用约束。这些属性是我们在视图之间建立关系时可以使用的选项。

### 关系类型

因为我们已经知道我们必须使用属性来创建两个视图之间的关系，下一个决定是这些视图之间应该存在的关系类型。视图之间可能存在三种关系，但我们一次只能使用一种:等于、小于或等于以及大于或等于。

等于意味着 viewA 的特定属性应该等于 viewB 的特定属性。

小于或等于意味着视图 a 的特定属性应该只小于或等于视图 b 的特定属性。

大于或等于意味着 viewA 的特定属性应该只大于或等于 viewB 的特定属性。

### 乘数

等式中表示为 M 的乘数用于乘以(调整)某个浮点值的关系。我们可以说 viewA.height 应该是 viewB.height 的*2(两倍)

### 常数

该常数用于使一个视图偏离另一个视图，它主要用于在两个视图之间创建空间。

### 在代码中添加自动布局

当谈到以编程方式创建约束时，我们有三种选择，您可以使用 NSLayoutConstraint 类、布局锚，或者您可以使用可视化格式语言。我们将使用 NSLayoutConstraint 类和布局锚点。
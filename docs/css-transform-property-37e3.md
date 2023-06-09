# CSS 转换属性

> 原文：<https://dev.to/neshaz/css-transform-property-37e3>

没有[过渡](https://kolosek.com/css-transition/)，transform property 只能翻转它所应用的对象。所以，在最真实的情况下(真实生活？)，这两个选项一起应用。

## 只是变换

该财产拥有的权力是:

*   **旋转(角度)**，
*   **歪斜(角度)**，
*   **平移(x，y)** ，
*   **scale(x，y)** ，
*   **矩阵(n，n，n，n，n，n)** 。

除了矩阵之外，所有的值都很容易解释。

`rotate`和`skew`选项使用角度值(值可以是负值，您可以键入 360°以上，如 1080 度，旋转三整圈)。

`translate`选项使用`px`或`%`。

`scale`选项仅使用数字，它们根据给定的宽度和高度参数定义对象将增加/减少多少。

所有这些属性都接受矩阵(可能还没有自我解释),具有特殊选项，X 和 Y 被添加到语法中，在这种情况下，只需要一个参数，并且属性在`rotate`和`skew`情况下相对于 X 或 Y 轴(例如 translateX()、scaleY()...).

`matrix`选项无所不能(您是否期望更少？).确切的语法是`matrix(scaleX(),skewY(),skewX(),scaleY(),translateX(),translateY())`。尽管里面没有旋转选项，但我看到它也可以旋转。我不能多说这个(因为这是数学...某物...功能...东西)。查看[这篇文章](https://www.quackit.com/css/functions/css_matrix_function.cfm)了解更多。

也可以通过在一个转换下定义它们来组合多个转换。

```
 div {
     transform: rotate(90deg) scale(2) translateY(-50%) translateX(50%);
 } 
```

这个属性最近最流行的作用是旋转导航，社交和其他信息，大多在左侧，并使它们垂直。下面的两个例子都使用了`transform: rotate(-90deg)`属性。

[![Screen-Shot-on-Mar-1st-at-08_39-AM](img/e72fb20fdc93ac077b55e8f083168352.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ffLWJv12--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/03/Screen-Shot-on-Mar-1st-at-08_39-AM.png)

[![Screen-Shot-on-Mar-1st-at-08_53-AM](img/d1795e75968863d271a6d384d417c236.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oAKu8P3F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/03/Screen-Shot-on-Mar-1st-at-08_53-AM.png)

## 3D 变换

2D 属性值`rotate`、`translate`、`scale`和`matrix`有其平行的 3D 值，并添加了 z 轴。额外的 3D 属性是`perspective`，它定义了一个 3D 转换元素的透视图。`Perspective`使用一个值来定义元素离视图的距离。

*   **rotate3d(x，y，z，角度)**，
*   **translate3d(x，y，z)** ，
*   **scale3d(x，y，z)** ，
*   **matrix3d(n，n，n，n，n，n，n，n，n，n，n，n，n，n，n，n，n，n)** ，
*   **视角(n)** 。

我相信，在我们理解了 2D 期权之后，所有这些都是不言自明的。

## 过渡+变换

[`transform`属性](https://kolosek.com/css-transition/)用于定义一个元素的两种状态之间的转换。有了`transition`，他们组成了强有力的一对，就像开头提到的。

```
 .box {
     ...
     background-color: #0000FF;
     -webkit-transition:background-color 2s, -webkit-transform 2s;
     transition:background-color 2s, transform 2s;
}
.box:hover {
     background-color: #FFCCCC;
     -webkit-transform: rotate(180deg);
     transform: rotate(180deg);
} 
```

这种组合是两个属性最常见的用法，`transform`和`transition`。重要的是要记住**在第一个状态中用 transform 作为值定义转换，在第二个状态中定义 transform。**

## 最终字

摇摇你的直元素！

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/css-transform/)上。
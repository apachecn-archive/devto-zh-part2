# CSS 项目符号样式[解释]

> 原文：<https://dev.to/neshaz/css-bullet-style-explained-3lm5>

你喜欢列很多很多的清单吗？我也不是。但是，这里我们不是在谈论家务或购物清单。我说的是制作现代网站和应用时不可避免的有序和无序列表。

如前所述，在 HTML 和 CSS 中创建列表时，有两种类型:**有序和无序**。在**的情况下，有序列表编号出现**。另一方面，**在无序列表的情况下，不存在编号**，这就是为什么这种类型的列表的常见名称是**项目符号列表**。

使用列表时，正确设置文本的格式[很重要。](https://kolosek.com/css-line-height/)

本文的重点是无序列表，以及项目符号的类型和属性。

无序列表使用了`<ul></ul>`标签。让我们看一个简单的例子。

HTML

```
<ul>
  <li>First item</li>
  <li>Second item</li>
  <li>Third item</li>
<ul> 
```

## 类型属性

**类型**属性可用于确定列表中将使用哪种类型的项目符号。该属性可以由三个不同的值组成:

*   **圈**，
*   **圆盘**，
*   **方**。

可以在`<ul></ul>`或者`<li></li>`元素上设置项目符号类型，也就是说可以在**总列表**上设置样式，也可以在**单独设置每个列表项的样式**。

制作列表时，知道使用哪种字体大小是一件好事。

## 项目符号样式

要更改项目符号样式，可以使用两个属性。**列表样式类型**属性或者**列表样式**。不同之处在于，通过使用第二个选项，您可以同时更改多个属性。但我稍后会讲到。

CSS

```
.ul {
  list-style-type: square;
} 
```

这里我们看到列表样式设置为方形。

## 使用自定义图像

借助 CSS 的强大功能，可以将一个**自定义图像用作一个项目符号**。通常，它是一个简单的小图标。为此，我们使用了**列表样式图像**。

CSS

```
ul {
  list-style-image: url(‘images/sample-bullet.png’);
} 
```

## 项目符号位置

您还可以使用 **list-style-position** 属性来设置项目符号的位置。它可以有两个值:**外部和内部。**

CSS

```
ul {
  list-style-position: outside;
} 
```

CSS

```
ul {
  list-style-position: inside;
} 
```

[![list_position_outside](img/7e10ca7e25f53b8c748f715a95c51cb1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hizBzFCD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/02/list_position_outside.png)

[![list_position_inside](img/4a8de55faac2480de76ffa6c88b6878c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IUZbl48c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/02/list_position_inside.png)

第一幅图像显示了设置为外**的值，第二幅图像显示了设置为**内**的值。
当设置值为 outside 时，表示项目符号将在列表项**的**之外。并且，当将值设置为 inside 时，项目符号将位于列表项**内的**。**

不要忘记给列表一些样式。

如果这种样式还不够，你可以[改变项目符号的颜色](https://www.w3.org/Style/Examples/007/color-bullets.en.html)。

## 结合属性

正如我之前提到的，您可以通过设置**列表样式的**来组合多个属性。

CSS

```
ul {
  list-style: square inside url("element.gif");
} 
```

用这个简写，我们同时设置**列表样式类型、列表样式位置和列表样式图像**。

## 总结

开发网站和应用程序时经常使用列表，所以你应该知道如何设计它们的样式。我希望这个简短的解释能对你的项目有所帮助。

本文原载于 [Kolosek 博客](https://kolosek.com/css-bullet-style/)。
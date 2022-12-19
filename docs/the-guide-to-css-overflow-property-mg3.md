# CSS 溢出属性指南

> 原文：<https://dev.to/neshaz/the-guide-to-css-overflow-property-mg3>

**CSS 溢出**属性指定在内容**太大**而无法放入容器盒的情况下该做什么。它指定了是否应该出现一个**滚动条**，或者一个内容是否被**剪切**。

*overflow 属性是 overflow-x 和 overflow-y 的简写，overflow-x 属性指定处理水平方向的溢出，overflow-y 指定处理垂直方向的溢出。*

在了解这一点时，多了解一点关于[定位](https://kolosek.com/css-position-relative-vs-position-absolute/)的知识是很有用的。

## 值

溢出属性可以有不同的值:

*   **可见** -内容可以在盒子外面呈现，
*   **隐藏** -内容被剪切，不显示滚动条，
*   **滚动** -内容被剪切，必要的滚动条被显示，
*   **auto** -浏览器决定如何处理内容，这可能因浏览器而异，但一般来说，滚动条会根据需要出现。

溢出属性仅适用于**块、内嵌块和表格元素**。

## 语法

以下语法用于定义溢出属性:

```
div {
  overflow: hidden;
} 
```

## 例子

现在，让我们用一些例子来支持这个理论。当处理文本时，它应该有一个合适的[格式](https://kolosek.com/css-relative-font-size/)。

HTML

```
<div>
  <p>Lorem ipsum dolor amet next level banh mi actually etsy craft beer. Portland meh palo santo pitchfork wayfarers raclette kinfolk try-hard YOLO. Lo-fi cred pork belly, cloud bread artisan heirloom raw denim kombucha. Godard etsy ugh, letterpress roof party fingerstache succulents edison bulb. Iceland disrupt palo santo fixie hella taiyaki celiac green juice.</p>
</div> 
```

CSS

```
div {
  height: 200px;
  width: 200px;
  border: solid thin blue;
  background-color: #fafafa;
  overflow: visible;
} 
```

[![overflow_visible](../Images/dee0bda98d88f0c29d2bc20acb85099e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rNv3v5sN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/03/overflow_visible.png)

在上面的例子中，我已经将**溢出设置为可见的**，内容**溢出**到包含框的外面。

HTML

```
<div>
  <p>Lorem ipsum dolor amet next level banh mi actually etsy craft beer. Portland meh palo santo pitchfork wayfarers raclette kinfolk try-hard YOLO. Lo-fi cred pork belly, cloud bread artisan heirloom raw denim kombucha. Godard etsy ugh, letterpress roof party fingerstache succulents edison bulb. Iceland disrupt palo santo fixie hella taiyaki celiac green juice.</p>
</div> 
```

CSS

```
div {
  height: 200px;
  width: 200px;
  border: solid thin blue;
  background-color: #fafafa;
  overflow: hidden;
} 
```

[![overflow_hidden](../Images/17afa52d9b4ea4666679671417190401.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1aHZhZaP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/03/overflow_hidden.png)

在第二个例子中，我将**溢出属性设置为隐藏的**。内容是**剪辑过的**和**没有滚动条**可见。

HTML

```
<div>
  <p>Lorem ipsum dolor amet next level banh mi actually etsy craft beer. Portland meh palo santo pitchfork wayfarers raclette kinfolk try-hard YOLO. Lo-fi cred pork belly, cloud bread artisan heirloom raw denim kombucha. Godard etsy ugh, letterpress roof party fingerstache succulents edison bulb. Iceland disrupt palo santo fixie hella taiyaki celiac green juice.</p>
</div> 
```

CSS

```
div {
  height: 200px;
  width: 200px;
  border: solid thin blue;
  background-color: #fafafa;
  overflow: scroll;
} 
```

[![overflow_scroll-](../Images/35e354894199fdff58f9935a46c2705a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T5Y5jv_7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/03/overflow_scroll-.png)

在第三个例子中，我设置了**溢出属性来滚动**。在这种情况下，内容**溢出**到容器外，**滚动条出现**。

HTML

```
<div>
  <p>Lorem ipsum dolor amet next level banh mi actually etsy craft beer. Portland meh palo santo pitchfork wayfarers raclette kinfolk try-hard YOLO. Lo-fi cred pork belly, cloud bread artisan heirloom raw denim kombucha. Godard etsy ugh, letterpress roof party fingerstache succulents edison bulb. Iceland disrupt palo santo fixie hella taiyaki celiac green juice.</p>
</div> 
```

CSS

```
div {
  height: 200px;
  width: 200px;
  border: solid thin blue;
  background-color: #fafafa;
  overflow-y: scroll;
  overflow-x: hidden;
} 
```

[![overflow_y](../Images/5cc8a21222d8c60950070c0e14f423f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RsfuLakt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/03/overflow_y.png)

最后，您可以看到 **overflow-y** 属性的用法。你只看到**垂直滚动条可见**，而**水平滚动条被隐藏**。

## 总结

在这篇简短的教程中，我解释了溢出属性。希望，当你开始下一个项目时，它会有所帮助。

感谢您的阅读！

本文原载于 [Kolosek 博客](https://kolosek.com/css-overflow/?utm_source=dvt)。
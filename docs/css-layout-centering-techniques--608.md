# CSS 布局居中技术

> 原文：<https://dev.to/alexantoniades/css-layout-centering-techniques--608>

用 CSS 使元素居中有时会很棘手。您可以使用很多技术，但是您应该使用什么技术取决于元素和内容。尝试将元素居中时，您可能会问自己一些问题:

*   它是内联元素还是块元素？
*   只是一行文字还是多行？
*   它有固定的宽度和高度还是大小未知？

当你想将一个元素居中时，这些可能是你最常问自己的问题。

我要把技术分为水平，垂直和两者。让我们来看看代码。

## **水平居中**

**文本对齐**

这是最简单的水平居中技术，可以用于文本和 inline-*元素。

```
p { text-align: center; }
```

如果要将内联元素居中，应该将其应用于其父元素，而不是直接应用于该元素:

```
.parent { text-align: center; }
```

```
<div class="parent">
 <strong>I'm Centered</strong>
</div>
```

In case we have an inline-block element we could make it full width and apply `text-align: center;` on it directly instead of it's parent.

```
strong {
 display: inline-block; 
 width: 100%;
 text-align: center;
}
```

[Pen](https://codepen.io/AlanFall/pen/aqOGXw/)
**Margin Auto** This is the most common technique for centering horizontally block elements, it's widely used by many frameworks like [Bootstrap](https://getbootstrap.com/). You just set the left and right`margin` to auto, make sure you have also set a width:

```
.block-box { 
 width: 600px;
 margin: auto;
}

```

[笔](https://codepen.io/AlanFall/pen/zRGaoP/)

**绝对位置**

水平和垂直居中的常用技术是使用绝对定位元素作为相对父元素的子元素。

我们所做的基本上是将我们的子元素向左移动 50%,我们使用负 50% `translateX`将它移回一半大小，以使它居中。

```
.parent { position: relative; }
.centered-element {
 position: absolute;
 left: 50%;
 transform: translateX(-50%);
}
```

```
<div class="parent">
 <div class="centered-element">I'm Centered!</div>
</div>

```

这也是将未知宽度(和高度)的元素居中的好方法。

另一种方法是——如果你有已知宽度的元素——使用负的`margin-left`代替`translateX`:

```
.centered-element { 
 position: absolute;
 width: 600px;
 left: 50%;
 margin-left: -300px; // Shift it back by half of it's size.
}
```

[笔](https://codepen.io/AlanFall/pen/VQLdOo/)

**伸缩**

如果您不关心对旧浏览器的支持，水平(和垂直)居中一个元素或一组元素的好方法是使用 flex。

```
.parent { 
 display: flex; 
 justify-content: center;
 height: 100vh;
}
```

***注意:**确保没有将`flex-direction`属性设置为列，否则会产生相反的效果(垂直居中)。*

[笔](https://codepen.io/AlanFall/pen/GQJBpZ/)

## 垂直居中

**绝对位置**

同样，我们可以使用与之前相同的绝对定位技术，但不是将左侧位置设置为 50%，而是将顶部位置设置为 50%，并使用负 50% `translateY`:

```
.parent { position: relative; }
.centered-element {
 position: absolute;
 top: 50%;
 transform: translateY(-50%);
}
```

[笔](https://codepen.io/AlanFall/pen/VQLBjY/)

**表格单元格**

垂直居中的一个好方法是使用 vertical-align: middle。当然这很棘手，因为您必须将它应用于设置为 `display: table`的父元素和设置为`display: table-cell`的子元素。

```
.parent { 
 display: table;
 height: 100vh; 
}

.centered-element {
 display: table-cell;
 vertical-align: middle;
}
```

***注意:**确保父元素有高度，否则不起作用。*

[笔](https://codepen.io/AlanFall/pen/PQqBbW/)

**行高**

如果你想将单个单词而不是整段或整行文本居中，这种方法是可以的。如果一个元素中有一个单词，你可以通过在该元素上设置一个固定的高度，并设置一个等于该元素高度的 `line-height`,使其垂直居中:

```
.box { 
 width: 600px;
 height: 600px;
 line-height: 600px;
}
```

```
<div class="box">
 <strong>I'm Centered</strong>
</div>
```

[笔](https://codepen.io/AlanFall/pen/zRGLPQ/)

**幽灵元素**

在这种技术中，我们有两个内联块元素:一个容器，其“ghost”伪元素设置为全高，一个子元素设置为 `vertical-align: middle;`,这样子元素与 ghost 元素对齐——后者也有`vertical-align: middle`

```
.container { font-size: 0; }
.container::before {
  content: '';
  display: inline-block;
  height: 100%;
  vertical-align: middle;
}
.container strong {
 display: inline-block;
 vertical-align: middle;
 font-size: 1rem;
}
```

***注意:**ghost 元素通常在左侧创建一个空白区域，所以要确保在父容器上添加一个负边距(`margin-right: -0.25em`)或一个`font-size: 0`，在子容器上添加一个`font-size: 1rem`。*

[笔](https://codepen.io/AlanFall/pen/xYGJWK/)

**伸缩**

我们已经看到了如何与 flex 水平对齐。我们可以通过使用`align-items`属性对垂直居中做同样的事情。

```
.parent { 
 display: flex; 
 align-items: center;
 height: 100vh;
}
```

***注意:**还是那句话，确保你没有把`flex-direction`设置成`row`，否则会适得其反。(水平居中)。*

[笔](https://codepen.io/AlanFall/pen/YeXjeW/)

## 水平和垂直居中

我们可以将之前使用的所有技术结合起来，在两个轴上居中。

在我们使用的所有技术中，绝对定位和弯曲技术将为我们提供水平和垂直居中。

**绝对位置**

我们将元素定位在顶部和左侧的 50%，并通过使用 50%的负平移在两个位置上向后移动:

```
.centered-element {
 position: absolute;
 top: 50%;
 left: 50%;
 transform: translate(-50%, -50%);
}
```

[笔](https://codepen.io/AlanFall/pen/qxdyLb/)

**伸缩**

同样，使用相同的伸缩技术，我们将 `align-items`和`justify-content`设置为中心。

```
.parent { 
 display: flex; 
 align-items: center;
 justify-content: center;
 height: 100vh;
}
```

[笔](https://codepen.io/AlanFall/pen/MQKxam/)

有什么要补充的吗？请随意在评论区留言。

*感谢 [PVGR](http://www.pvgr.eu/) 提供的有用信息和更正。*
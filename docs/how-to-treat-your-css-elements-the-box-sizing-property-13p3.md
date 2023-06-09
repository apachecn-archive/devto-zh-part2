# 如何对待你的 CSS 元素:盒子大小属性

> 原文：<https://dev.to/neshaz/how-to-treat-your-css-elements-the-box-sizing-property-13p3>

在上一篇讨论 CSS 主题的文章中，我谈到了 **[盒子模型](http://https://kolosek.com/css-box-model-for-beginners/)** 。与盒子模型相关的一个重要属性是**盒子大小属性**。

box-sizing 属性定义元素的高度和宽度是如何计算的，以及它是否应该包括边框、填充和边距。

## 值

框大小属性有两个可能的值:

*   **内容框** -默认值。
*   **边框**。

让我们看看在这两种情况下宽度是如何计算的。

提醒一下，箱式模型看起来像这样:

[![box_7-1](img/c36a952d5ea0bd4a0bf4483613f58ef7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZLnIOynI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/box_7-1.png)

## 内容框

**box-sizing 属性的默认值是 content-box** 。我将解释在这种情况下宽度是如何计算的。

即使我将元素的宽度设置为 200px，实际宽度也会有很大不同。公式是:

*宽度=左边距+左边界+左填充+内容+右填充+右边界+右边距*

因此，在前面的例子中，宽度是:

*宽度= 20px+3px+10px+200px+10px+3px+20px*

这次计算的结果是 266 像素。这大概不是你期待的结果，我说的对吗？这就是为什么你需要非常小心的原因。

现在，你可能想知道是否有比每次都使用计算器更简单的方法来处理宽度？有！通过使用边框。

## 边框-框

就拿前面的例子来说吧。我们指定元素的宽度为 200px，每边的填充为 10px。如果你使用内容盒，那么它会变成 220 像素。

但是，当使用 border-box 时，宽度将是 200 像素，这意味着内容的宽度将是 180 像素，填充是 20 像素。内容的大小会缩小，以容纳添加的填充。

## 举例

让我们来看一个这两种情况的可视化示例，以便更好地解释这些属性。

HTML

```
<div class=”content-box”>Content-box</div>
<div class=”border-box”>Border-box</div> 
```

CSS

```
div {
  height: 200px;
  width: 200px;
  background-color: hotpink;
  color: #fff;
  padding: 10px;
  border: solid 3px black;
  margin: 20px;
}

.content-box {
  box-sizing: content-box
}

.border-box {
  box-sizing: border-box;
} 
```

[![content](img/a96c055e2ab6543933f767e5e0174f25.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UT7y2CRL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/content.png)

如你所见，在第一个例子中，**内容框**的宽度总计为 **266px** ，而**边框**的宽度总计为 **200px** 。

## 结论

盒子大小属性和盒子模型之间有一个重要的联系，而且是非常重要的。理解框大小属性的内容框和边框框值是很有价值的。虽然 content-box 是默认值，但是使用 border-box 属性可以更容易地避免经常令人困惑的计算。

编码快乐！:)

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/css-box-sizing/?utm_source=dvt)上。

@Mattia Astorino 文章来了，如约而至！
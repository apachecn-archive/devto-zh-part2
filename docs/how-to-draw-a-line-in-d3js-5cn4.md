# 如何在 D3.js 中画线

> 原文：<https://dev.to/webdva/how-to-draw-a-line-in-d3js-5cn4>

本教程是为了帮助那些不熟悉 D3.js 图表库，想知道如何使用它画线的人。

# 设置

内联 Javascript 和 CSS 将在单个。html 文件。和 D3.js 将从内容传递网络获取。所以。您将使用的 html 文件将具有这样的结构:

```
<script src="https://d3js.org/d3.v5.js"></script>

<script>
</script> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，使用的是版本 5(而不是版本 4)。

# `svg`元素配置

在 Javascript 部分中，使用`d3.select`函数获取对 SVG 元素的引用。它的参数是一个包含元素的`id`属性的字符串。

```
const svg = d3.select('#svg1'); 
```

Enter fullscreen mode Exit fullscreen mode

由`d3.select('#svg1')`返回的值具有以链接方式附加的函数，以进一步修改所选的 SVG 元素。许多 D3.js 的操作都使用这种链附加方式，这也是使用它的关键。

例如，似乎可以用`attr`和`style`函数在 Javascript 中修改 SVG 元素的 HTML 属性和 CSS 属性。

下面定义了一个 400 像素宽和高的元素，背景颜色为黑色。

```
const svg = d3.select('#svg1')
    .attr('width', 400)
    .attr('height', 400)
    .style('background-color', 'black'); 
```

Enter fullscreen mode Exit fullscreen mode

# 定义一条线

现在我们将向 SVG 元素添加一行。我们将使用我们定义的`svg`变量的`append`函数。

## 线条颜色

我们想创建一个浅绿色的线。我们使用`style`函数定义一种颜色来修改一个`stroke`属性，这里是线条的颜色。

```
svg.append('line')
    .style('stroke', 'lightgreen'); 
```

Enter fullscreen mode Exit fullscreen mode

## 线条宽度

我们还想用`stroke-width`属性定义线条的宽度。一条 10 像素宽的线定义如下。

```
svg.append('line')
    .style('stroke', 'lightgreen')
    .style('stroke-width', 10); 
```

Enter fullscreen mode Exit fullscreen mode

## 线位置

对于简单的二维线，必须定义线的端点才能显示。这是通过`x1`、`y1`、`x2`和`y2`属性实现的。`x1`和`y1`对应第一端点，而`x2`和`y2`对应第二端点。

D3.js 好像用的是原点在左上角的坐标系。

```
svg.append('line')
    .style('stroke', 'lightgreen')
    .style('stroke-width', 10)
    .attr('x1', 0)
    .attr('y1', 0)
    .attr('x2', 200)
    .attr('y2', 200); 
```

Enter fullscreen mode Exit fullscreen mode

# 结果

这就是我们想要的产品系列。

[![example](../Images/26f059b3971a022fae6445089d362fe4.png "The result")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_klaRXLn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gaap4qve9ycmoldlf7uc.PNG)

# 完整源代码

为了您的方便，完整的源代码:

```
<script src="https://d3js.org/d3.v5.js"></script>

<script>
    let svg = d3.select('#svg1')
        .attr('width', 400)
        .attr('height', 400)
        .style('background-color', 'black');

    svg.append('line')
        .style('stroke', 'lightgreen')
        .style('stroke-width', 10)
        .attr('x1', 0)
        .attr('y1', 0)
        .attr('x2', 200)
        .attr('y2', 200);        
</script> 
```

Enter fullscreen mode Exit fullscreen mode
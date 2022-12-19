# 在 SVG 中创建线性渐变

> 原文：<https://dev.to/neradev/creating-linear-gradients-in-svg-359h>

我最近不得不弄清楚，如何在不使用 PNG 文件或其他任何东西的情况下在 SVG 中创建线性渐变。因为这个方法很简单也很有效，所以我想和你分享一个很简单的例子。

让我们考虑这个简单的 SVG 结构:

```

    <g>
        <rect x="10" y="10" width="200" height="100" class="rectangle" />
    </g>
 
```

Enter fullscreen mode Exit fullscreen mode

它只是呈现一个黑色的矩形。我现在想要一个从左到右的线性渐变作为矩形的填充颜色。我们需要在 SVG 元素的`<defs>`标签中定义这一点。

```

    <g>
        <rect x="10" y="10" width="200" height="100" class="rectangle" />
    </g>
    <defs>
        <linearGradient id="myGradient">
            <stop offset="0%" class="start" />
            <stop offset="100%" class="end" />
        </linearGradient>
    </defs>
 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用的`linearGradient`标签的 id 作为 rect 元素的填充引用。`stop`标签用于定义渐变的断点和变色点。你甚至可以不止定义两个。

需要的 CSS 看起来是这样的:

```
.rectangle {
  fill: url(#myGradient);
}

.start {
  stop-color: #a0ced9;
}

.end {
  stop-color: #83a9b2;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们只需要使用`url(#myGradient)`来引用梯度。这直接搜索定义的线性梯度(或不同的梯度)。不同停止元素的颜色需要使用 stop-color 属性来定义。

结果如下:

[https://codepen.io/neatoro/embed/JZGoZN?height=600&default-tab=result&embed-version=2](https://codepen.io/neatoro/embed/JZGoZN?height=600&default-tab=result&embed-version=2)

**Post:** 如果你想创建一个从上到下的线性渐变，那么你需要将线性渐变定义改为`<linearGradient id="myGradient" x2="0%" y2="100%">`。
# 纯 CSS 类似 Salesforce 的 progressbar 控件

> 原文：<https://dev.to/bohdanstupak1/pure-css-salesforce-like-progressbar-control-1jo7>

本文最初发表于 [Codeproject](https://www.codeproject.com/Tips/1194241/Pure-CSS-Salesforce-like-progressbar-Control)
你可以在 [Github](https://github.com/Wkalmar/StageControl) 下载完整的源代码。

## 少用

LESS 是 CSS 预处理器，它允许用一些有用的特性扩展 CSS。你可以在这里了解更多信息。在这个项目中，我使用了更少的变量和函数，正如你在后面看到的，这让我可以用更干净的方式处理颜色。

样式表像通常的 CSS 一样附加到 index.html 文件。

```
<link rel="stylesheet" href="stage-control.min.css"></link> 
```

Enter fullscreen mode Exit fullscreen mode

LESS 文件通过:
编译成样式表

```
$ lessc --clean-css stage-contro.less stage-contro.min.css 
```

Enter fullscreen mode Exit fullscreen mode

## 制作相同宽度的舞台物品

考虑一个容器，其中有随机数量的物品，这些物品应该具有相同的宽度，并占据所有可用的容器空间。

解决方案是:

```
.stage-wrapper {
    display: flex;
} 
.stage-item { 
    flex-basis: 100%; 
} 
```

Enter fullscreen mode Exit fullscreen mode

显示:flex 允许容器改变项目的大小，通常用于动态布局。flex-basis 定义了分配剩余空间之前元素的默认大小。100%使相同宽度的物品适合我们的情况。

## 绘图和箭头

每个物品盒都装饰有一个箭头。
[![arrow](img/ad9dd79b1dae4ba1498fb2d59d93ec6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1BqmnACM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codeproject.com/KB/HTML/1194241/arrow1.png) 
画箭头背后的思想是零宽零高的方框，这个边框本身就作为箭头。箭头的大小由边框的大小决定。例如，如果你想创建一个指向右边的箭头，你可以用一些颜色填充左边的边框，其他的边框保持透明。

```
.arrow-right { 
    width: 0; 
    height: 0; 
    border-top: 60px solid transparent; 
    border-bottom: 60px solid transparent; 
    border-left: 60px solid green; 
} 
```

Enter fullscreen mode Exit fullscreen mode

为了在项目框后接收箭头和两个框之间的一些空白，我们组合了两个箭头:一个较小的箭头使用项目框的颜色，另一个相当大的箭头使用伪元素:before 和:after。

```
&:before,
&:after {
    border: solid transparent;
    content: " ";
    left: 100%;                
    position: absolute;
    top: 50%;
    z-index: 2
}

&:after {
    border-left-color: inherit;
    border-width: 12px;
    margin-top: -12px;
}

&:before {
    border-color: rgba(255, 255, 255, 0);
    border-left-color: #fff;
    border-width: 14px;
    margin-top: -14px;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 悬停物品

考虑到我们的进度条已经通过了一些项目。让我们介绍一下标记这些项目的类。

```
&.stage-active {
    background-color: @active-color;

    &:after {
        border-left-color: @active-color;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们如何显示移动到下一个项目的意图，并因此将这个项目和之前的项目标记为通过？这个想法是用较浅的@active-color 阴影来标记之前或悬停的所有未通过的项目。
[![hover control forward](img/79bc51e0f03e46128becf245d5daa7f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ACAiAcP5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codeproject.com/KB/HTML/1194241/hover1.png) 
在 CSS 中，我们不能只选择之前悬停的所有项目，我们分两步实现我们的目标:

1.  用较浅的@active-color 阴影标记我们的控件包装的所有未通过的子级:

```
.stage-wrapper:hover div:not(.stage-active) {
    background-color: lighten(@active-color, 20%);

    &:after {
        border-left-color: lighten(@active-color, 20%);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意使用较少的功能变亮如何允许我们不硬编码活动颜色的较亮阴影。

1.  用默认颜色标记悬停后的所有项目。

```
&:hover ~ div:not(.stage-active) {
    background-color: @default-color;

    &:after {
        border-left-color: @default-color;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 悬停物品后退

考虑一个类似的逻辑，将已经传递的项指向后面返回。
[![hovering control backward](img/3d32a434eb411e38c2a870bc05e512a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ogaDyGtb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codeproject.com/KB/HTML/1194241/hover2.png) 
为了实现我们的目标，我们只需用强调色标记所有悬停后的项目:

```
&:hover ~ div {
    background-color: lighten(@active-color, 20%);

    &:after {
        border-left-color: lighten(@active-color, 20%);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode
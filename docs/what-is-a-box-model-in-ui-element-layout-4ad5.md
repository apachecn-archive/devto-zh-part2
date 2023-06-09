# 什么是 UI 元素布局中的“盒子模型”？

> 原文：<https://dev.to/mortoray/what-is-a-box-model-in-ui-element-layout-4ad5>

盒子模型描述了如何测量布局的元素。它定义了什么是大小以及它与内容、边距和填充的关系。这可能看起来微不足道，但是这里清晰的定义对于构建 UI 引擎是必不可少的。

> 这篇文章是关于编写 UI 引擎的系列文章的一部分。

## 常见的箱式模型

[![box-model diagram](img/9f4ee9fa7984f58aee49533681c38f2f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AUgX_77y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/03/box-model.png)

一个普通的盒子模型由几个盒子组成，每个盒子里有一个更小的盒子。与 CSS 模型一样，从内到外依次是:

*   内容:这是一个元素的主体，可以是子元素，也可以是直接的内容，比如图像或文本。
*   填充:填充框从内容偏移。通常在该区域内绘制背景。
*   边框:边框可以完全或部分包围填充框。*姑且称这个为“可选”，看我下面的评论。*
*   边距:边距框是用来分隔元素的空间。

这些是关于盒子模型各部分的一般概念。确切的含义与它们如何与其他元素和布局属性交互紧密相关。让我们来看看一些可能性。

> 我会忽略边框。我写的布局引擎没有它，我也看不出它有什么实质性的价值，使用这个引擎的人甚至都没有提到过它。我还没有发现一个 UI 因为拥有它而简化。

## 大小

一个元素的大小是多少？考虑这个 UI 片段:

```
Panel{Color=#008}
    Rectangle{Margin=10 Alignment=TopLeft Padding=5 Width=20 Height=40 Color=#080}
        Ellipse{Color=#800} 
```

Enter fullscreen mode Exit fullscreen mode

矩形覆盖的面积是 20x40，还是`Padding`增加的面积是 30x50 (5 适用于所有尺寸)？从设计的角度来看，使用前一种方法更方便，改变填充不会改变矩形的大小。通过调整子模型的大小，很容易创建后者，即附加模型。

```
Panel{Color=#008}
    Rectangle{Margin=10 Alignment=TopLeft Padding=5 Color=#080}
        Ellipse{Color=#800 Width=20 Height=40} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们改变矩形的填充，它的整体大小将会改变。发生这种情况是因为子级不再扩展以填充大小，而是定义大小，并且父级添加填充大小。盒子模型必然依赖于[自然尺寸和从属尺寸](https://mortoray.com/2018/03/15/ui-layout-natural-versus-dependent-size/)的定义，或者我们可以说它是该定义的一部分。

换句话说，`Width`属性定义了上下文框、填充框或边距框的宽度吗？所有这些在某些情况下都有意义，但是我可以向你保证 UI 系统的用户会对提供所有这些的系统的复杂性感到惊讶。此外，编写 UI 引擎会非常困难——属性不仅仅是别名，而且在框定大小的过程中有特定的规则。

将`Width`定义为填充框似乎是一个合理而自然的选择。

> CSS 因盒子模型碍事或有错误的属性定义而臭名昭著。这很大程度上可能是由于缺少布局，而不一定是盒子模型本身。如果你正在设计一个新的布局引擎，最好把这些麻烦记在心里，并确保你不会重复它们！

## 折叠页边距

在基于流的布局中，比如 HTML，我们可以定义折叠边距。

```
StackPanel
    Text{Margin=10 Value="One"}
    Text{Margin=10 Value="Two"} 
```

Enter fullscreen mode Exit fullscreen mode

[![collapsed margin example](img/2a45125eb4021861c23067e8a52ab734.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wUEdV8CC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/03/collapsed-margin.png)

随着边距的缩小，项目“一”和“二”之间将有 10 个点。由于严格的边距，这两项之间将有 20 个点。

对于流动布局，尤其是文本，折叠边距是有意义的。在这种模式下，它们更多地成为周围元素的最小空间，而不是额外的空间。对于典型的应用程序布局，附加边距更容易实现，并提供更高的一致性。

可以通过其他方式达到压缩边距的目的。在列表中，您可能希望项目之间有相等的间距。我用一个`ItemSpacing`属性实现了这一点。

```
StackPanel{ItemSpacing=10}
    Item
    Item 
```

Enter fullscreen mode Exit fullscreen mode

在这种方法中，`StackPanel`决定项目之间的间距，而不是项目决定。每个项目可能仍然使用一个`Margin`给自己额外的空间。我还为网格定义了一个`CellSpacing`，为列布局定义了一个`ColumnSpacing`。每个布局类型可以有一个`...Spacing`属性。

## 放置

我们需要定义两个点来决定元素的位置:父元素的位置，以及子元素的位置(锚点)。

基本对齐包括一些简单的例子。与左上角对齐的面板被假定为元素的左上角与该角齐平。居中元素的内容中心位于父元素的中心。

这就引出了一个问题，什么的左上角？在这种情况下，我们通常说的是边距框。与左上角对齐的元素的内容按其边距的大小与角分开。有道理。对居中的元素应用边距怎么样？这有点奇怪，但引擎最好有明确的定义。

而显性主播呢？我在[之前已经提到过 CSS](https://mortoray.com/2014/01/31/improving-css-positioning-with-position-anchor/) 了。它们是对布局的有益补充。例如，您可能希望将图像的左上角与父图像的中心对齐。

```
Image {Alignment=Center Anchor=0%,0%} 
```

Enter fullscreen mode Exit fullscreen mode

或者你正在装饰一个底部有标题的面板。

```
Panel
    GoodStuff
    CaptionPanel {Alignment=Bottom Anchor="50%,0%"}
        Text{Value="Something about good stuff"} 
```

Enter fullscreen mode Exit fullscreen mode

不管您提供什么属性，您总是需要根据两个位置来定义位置:一个在父节点中，称为位置，另一个在子节点中，称为锚点。在父容器中，你指的是内容框中的一个点；在子图中，你指的是边距框中的一个点。

> 当我们开始实现时，我将不得不回到位置，特别是锚定。我现在意识到它可能比这里的几个段落更复杂。

## 拳击起来

本文介绍了盒子模型，但没有提供完整的定义。随着我们在布局方面的深入，我们将继续参考盒子模型并定义与内容盒、填充盒和边距盒相关的行为。

当我们谈到渲染和点击测试时，也会提到盒子模型。很多属性涉及位置和大小，通常是不同的盒子。提出一个一致的定义和行为将为引擎用户在设计布局时省去很多麻烦。
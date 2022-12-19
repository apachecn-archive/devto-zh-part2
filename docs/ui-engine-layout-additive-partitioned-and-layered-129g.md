# UI 引擎布局:添加式、分区式和分层式

> 原文：<https://dev.to/mortoray/ui-engine-layout-additive-partitioned-and-layered-129g>

布局决定了子元素在其父元素中的位置和大小。在父元素中放置子元素有三种方法:

*   加法:子元素组合起来定义父元素的大小
*   Partitioned:父级的空间在子级之间划分
*   分层:子对象相互独立，都占据父对象的相同区域

这里我们将介绍每种方法的基础。

> 现在我们正在考虑应用程序布局，它与文档或文本布局有着不同的需求。富文本使用基于流的布局，这种布局适用于文档，但不适用于应用程序结构。这是一种与这里列出的方法截然不同的方法，我们最终会回到这个问题上来。
> 
> 这篇文章是关于编写 UI 引擎的系列文章的一部分。

## 加法-简单

堆栈面板是最常见、最容易理解的附加控制。堆栈面板的大小是其所有子面板的累积大小。

```
StackPanel{Alignment="Bottom"}
    ChildA
    ChildB
    ChildC 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/8d0d95ea26ecfd58de422a1f32ae2f71.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7Ik-szc4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/03/screen-shot-2018-03-22-at-10-58-19.png%3Fw%3D249%26h%3D300)

假设这是一个垂直面板，`StackPanel`的高度是所有三个子的高度之和。随着孩子越来越多，身高也越来越高；当我们移除儿童时，高度会降低。

附加布局在一个或两个维度上创建具有的元素。这些布局允许构建从其子组件获得尺寸的组件。

> 一个`StackPanel`元素的创建是可选的。在我实现的 API 中，`StackPanel`是一个普通的面板，附带一个`StackLayout`。使用带有布局实现的通用面板是一种很好的代码结构。然而，拥有像`StackPanel`这样的高级包装类型对于框架中的编码人员来说是一个友好的选择。代码整洁性和 API 友好性之间有一场战斗。

## 分区-简单

网格是最常见的分区控件。在一种基本形式中，它将可用空间分配给其子节点。这种布局的目标是确保每个孩子都有适当的屏幕区域，并且不会重叠。

```
Grid {RowCount="2" ColumnCount="2"}
    ChildA
    ChildB
    ChildC
    ChildD 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/63ee86a204629b3d85367757c295ac3c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_qdaZ3Ye--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/03/screen-shot-2018-03-22-at-10-59-22.png%3Fw%3D300%26h%3D273)

此示例网格将创建四个大小相等的单元格。这些孩子中的每一个然后可以在分配的区域内执行布局。

分区布局可以考虑孩子的大小，并且不需要平均划分空间。

```
Grid {RowCount="2" Columns="auto,1*,2*"}
    Text {Value="Short"}
    Child11
    Child12

    Text {Value="Longer Text"}
    Child21
    Child22 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/4b2dc47cd0569714a3d431ac8227d7c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wsBkQvoQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/03/screen-shot-2018-03-22-at-11-03-30.png%3Fw%3D300%26h%3D108)

假设行主排序，第一列包含文本`Short`和`Longer Text`。网格将确定这些元素的大小，取两者中较宽的一个作为列宽。显示器中的剩余空间将在另外两列之间划分，第三列的宽度是第二列的两倍。

一个`DockPanel`是另一个熟悉的分区布局。

```
DockPanel
    TitleBar {Dock="Top"}
    ActionBar {Dock="Bottom"}
    PrimeChild 
```

Enter fullscreen mode Exit fullscreen mode

标题栏占用了控件顶部的空间，ActionBar 占用了控件底部的空间。像网格上的`auto`列一样，我们在这里使用子元素的大小来减少可用空间。这种类型的分区是 DockPanel 提供的唯一类型，是一种减法布局:每个连续的子节点占用更多的空间，留给后面的子节点的空间更少。最后一个孩子得到剩余的空间。

## 分层

在分层布局中，子元素被认为是相互独立的。没有孩子会影响其他孩子的布局，也不会增加或减少可用空间。由 UI 编码器来正确定位子元素，包括潜在重叠子元素的 Z 顺序。

```
Panel
    Text {Alignment=TopLeft Value="User Portrait"}
    Icon {Alignment=BottomRight Style=Back}
    Image {Alignment=Center File="portrait.jpg" Width=50%}
    Rectangle {Color=#aaa} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/b28a54cc9ce8ada2201d88e5929f83d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AP5QHygm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/03/screen-shot-2018-03-22-at-11-10-22.png%3Fw%3D300%26h%3D171)

对于子层，您需要定义默认的 Z 顺序。这里我们假设第一个孩子在顶部，最后一个孩子在底部。这种布局将一些控件、标题文本和后退按钮放置在用户肖像的顶部。纯色矩形在它们的后面。

## 相加-复合

网格和停靠布局也可以以附加形式使用。这些实现起来有点困难，但是会产生一些有用的布局结构。

具有可变行数(固定高度或自然高度)的`Grid`可以放在另一个布局中，或者放在父布局中。

```
StackPanel
    Text {Value="Profile"}
    Grid {ColumnCount=2 DefaultRow="auto"}
        Label {Value="First Name"}
        TextEdit

        Label {Value="Last Name"}
        TextEdit

    Button {Action="Submit"} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/fa7e9dcaf5ea94773591fc758485a866.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ROO8NleC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/03/screen-shot-2018-03-22-at-11-14-16.png%3Fw%3D300%26h%3D180)

网格随着孩子的数量而增长。它在“堆栈”面板中被视为普通大小的元素，放置在文本标题和提交按钮之间。

类似地，可以使用 A `DockPanel`来构造自然大小的复合元素。

```
StackPanel
    DockPanel
        Image {Dock="Left" File="tommy.png"}
        UserStatus {Dock="Top"}
        Text {Value="Tommy"} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/1c5859998f1411e345acbf8384d906bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dNwBGg7w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/03/screen-shot-2018-03-22-at-11-21-35.png%3Fw%3D300%26h%3D125)

DockPanel 的三个元素各有一个自然大小，加在一起就创建了一个复合卡片。其中许多可以添加到堆栈面板，或用作屏幕上自然大小的弹出窗口。

> 让一个布局在两种模式下都工作是一个挑战。人们很容易说这两种不同的模式应该是显式的或者有不同的类型名。这可能对用户不友好，因为我们通常可以查看布局代码并理解其含义。同样重要的是要看到有四种模式，而不仅仅是两种，因为每个维度都可以独立地相加或分割。例如，最后两个代码片段中的网格和 dock 有一个相关的宽度，但是增加了高度。

## 只是一个概述

这些不同的布局目标用于构建一个合理的布局引擎。当我们开始为布局定义特定的 API 时，需要理解这两种方法之间的区别。你不希望只从一种布局开始，然后再尝试添加其他形式——除非你对重大的 API 重组感到满意。

> 如果你希望将富文本流作为另一种布局，你将面临一场艰难的战斗。如果我们看 HTML，我们看到它合理地解决了文档布局，但在应用程序布局方面有很大的局限性。即使有了 flex-box 这样的东西，一些简单的应用程序在混合模型方法中仍然很棘手。我确信我现在能够有效地将它们结合起来，但是我必须从头开始构建 API 来支持它。

在后面的文章中，我将更深入地研究每一种特定的布局。了解这些一般概念以及具体布局的细节非常重要。

* * *

> 阅读更多关于[写 UI 引擎](https://mortoray.com/topics/writing-a-ui-engine/)的文章。[在推特上关注我](https://twitter.com/edaqa)或者[脸书](https://www.facebook.com/mortoray/)，为了让我继续写作，考虑[成为赞助人](https://www.patreon.com/mortoray)。
# 你也可以制作动画:SVG 线条动画

> 原文：<https://dev.to/oppnheimer/you-too-can-animate-svg-line-animation-jgm>

动画看起来很令人愉快，并且提高了应用程序的可用性。动画可以提供重要的反馈，让用户更有耐心。动画让与技术的互动变得更有趣，视觉上也更愉悦，然而很多时候，网站是静态的，感觉不像人。这篇文章的重点不是为你的下一个项目添加动画，而是展示这样做的成本有多低。取悦你的用户可能比你想象的更容易！

在本教程中，您将创建一个自定义的 SVG 线条画，并使用 CSS 关键帧动画制作动画。

## 你将需要什么:

本教程使用三个免费的基于 web 的工具，不需要任何 web 开发、动画或图形编辑经验。

> **免责声明:强烈建议使用[谷歌 Chrome](https://www.google.com/chrome/) 、[火狐](https://www.mozilla.org/en-US/firefox/new/)或微软 Edge 跟随本教程。** Vectr 和 CodePen 可能无法在 Safari 或 Internet Explorer 上可靠运行。

### 向量

[Vectr](https://vectr.com/) 是一个在线矢量图形编辑器。它是免费的，并且有用于绘制和导出 SVG 的直观工具。

### SVGOMG

SVGOMG 是另一个免费工具，它获取 SVG 文件并去掉不必要的代码。这减小了文件大小，并消除了可能会分散注意力的代码。

### 码笔

CodePen 是一个在线社区和编辑器，用于编写和展示代码。它非常适合小动画。您可以编写代码并实时查看变化。

## 什么是 SVG？

SVGs 或可缩放矢量图形是超级的。它们是:

*   矢量图像意味着它们可以缩放到任意大小，而无需考虑分辨率
*   受所有现代网络浏览器支持
*   用 XML 定义，XML 是一种可以在 HTML 文件中使用的标记语言。

本质上，SVG 文件使用 xy 坐标或“点”来创建图像。这些点被组合成描述线条或形状的“路径”。

[![incomplete triangle](img/b5f9ed52186a930c6e249e436bd81516.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CdpgHa4Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23h935nisr5falzdtgx7.png)

描述上述三角形的路径可以是这样的:

```
 <path d="M0 0 L100 0 L50 100 L5 10" /> 
```

Enter fullscreen mode Exit fullscreen mode

**不要担心，你永远不需要手写 SVG 路径。**但是，请注意每个`M`和`L`后面的数字对。这些是 x-y 坐标，沿着路径标记三角形的每个点。与 JPEGs 和 png 等光栅图像格式不同，放大后的 SVG 看不到像素。增加图像大小只会增加点之间的距离。无论大小如何，SVG 路径都被平滑地填充。

在本教程中，您将创建一个单一路径的 SVG。然而，SVG 通常包含许多路径来描述更复杂的图像。

## 创建您的 SVG

前往[Vectr.com](https://vectr.com/)，点击“在线使用”按钮启动基于网络的编辑器。不需要注册！它看起来会像这样:

[![blank vectr editor](img/c3ab094558e24929bb05868ebc4c9471.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--exYNxrnY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qw9fb34f8uthrhhtvj7q.png)

在左上角你可以看到“层”标签。如果你是这种类型软件的新手，你可以把层想象成一堆透明的薄片。每个层都可以单独编辑，而不会影响其他层。当您添加参考图像和绘制路径时，您将在此栏中看到一个新图层。

向下移动，你会注意到钢笔工具。钢笔工具通过连接直线上的点来创建路径。这是本教程中使用的工具。其他工具创建形状或文本，也可以导出为 SVG 和动画。

下方是上传图片按钮。

最后，一旦完成，您将使用右侧栏顶部的 export 按钮导出您的 SVG。

### 上传图片

从您的电脑中选择一个参考图像，或在网上找到一个并下载。保持简单。线条清晰或对比度高的图像最好。当用手画图时，表达精细的细节是困难和乏味的。使用上传按钮或拖放。一旦上传，你会在左栏看到一个“图像”层。通过拖动任意角来调整图像大小以适合页面(**提示:按住 shift 键以保持图像的原始纵横比**

[![vectr with image](img/6bc1175e034551a0011c1ccf719ae32a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FkxVRvcx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1c4w8q575u15ga11htg2.png)

### 勾画你的道路

素描。意思是快速而轻松地完成这一步。很容易添加，删除和调整点沿着你的路径。在这个阶段，一条粗略的道路可以让你快速地尝试和做出改变。单击钢笔工具，然后单击图像上的任意位置开始绘制路径。随后的每一次单击都会添加一个新点并延伸您的路径。

[![vectr with started path](img/cdce55b92ae22535b2e9ffb2ef8acb03.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7emwjkbc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/abjstxbp9az12jsip87z.png)

继续沿着参考图像的主线添加点。用单一路径捕捉图像可能需要几次尝试。删除键将删除路径中的最后一点。如果需要重新定位，也可以拖动现有的点。根据图像和您的偏好，您可以选择连接路径的起点和终点，以形成一个闭环。

您可能希望隐藏参考图像，以便更好地了解完成的 SVG 的外观。将鼠标悬停在图像图层上，然后单击眼睛图标来切换该图层的可见性。

[![toggle image visibility gif](img/bb35c18a07612da5f8ab286487fcd450.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U5055MrX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/27h8s3rufde604g3dar2.gif)

### 提炼你的路径

有几种基本方法可以编辑路径以更好地适应图像:

*   重新定位点-拖动任何现有的点
*   添加一个新的点-单击现有路径上的任意位置
*   删除点-单击任何现有的点以选择它，然后按 delete 键

[![edit point demo](img/9ee636f6ac578856d1427fe354576869.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ny_CK5H7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/adl0dcp5m2ax9g8rzxm6.gif)

你可能已经注意到路径上每个点旁边的小圆圈或“角落部件”。这些小部件调整点的曲率。将 widget 拖离它所在的点，以柔化曲线。

[![edit point demo 2](img/565d582e417388ba5d489ddea9c697e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0dywJX0g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wbjgyyqvmnzkcoduxjpd.gif)

双击一个点可以在调整曲线的两种方法之间切换。两个都玩！

[![edit point demo 3](img/65c5fdc5ce5b4b9889e4daabf1eead89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B6jyzsrV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uqmq1nmbypybd35uq074.gif)

#### 几个小技巧

*   不要为小事烦恼——插图就是用更少的东西传达更多的信息。不可能捕捉到每一个细节，所以要选择要表达的元素。尝试移除点以简化路径。
*   你没有得到现实主义的分数-隐藏你的参考图片。尝试比例和曲线的强度，给你的插图风格。

### 导出您的 SVG

一旦对自己的道路满意了，就该出口了！首先，单击路径图层以选择它进行导出。您不希望您的参考图像包含在导出的文件中。您将看到一个灰色矩形，仅围绕着路径。然后单击导出按钮。

[![pre export steps](img/fe7b238d003982f43f6ed34d9f115a82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--92hDZpGG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u1ykbm67yhl4cvewra2c.png)

在导出页面上，选择“选择”并仔细检查您的路径后面是否有参考图像。然后点击下载保存您的 SVG！

[![export page](img/3822326d686f1d90d833f81f0d18275d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vwyrcOsQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dtktsxn96dn4a32x6aa8.png)

## 清洁 Your SVG

SVG 清理器做了一些有用的事情。清理器删除不必要的代码，并公开下一步使用的格式良好的 XML 代码。

导航到 [SVGOMG](https://jakearchibald.github.io/svgomg/) 并上传从 Vectr 下载的 SVG 文件(它可能在您的下载文件夹“untitled.svg”中)

[![blank SVGOMG](img/55412718114cab679877cc18d60700b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AVobTI_2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uxfpzj6qkmx0wado7dzc.png)

上传后，您将在右侧看到一列开关。保留默认设置，点击油漆桶下方的“复制为文本”按钮。您将在下一步中粘贴此文本。

[![SVG copy button](img/4a7d79ac0421708b89d43bf565a4e987.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--At8X_PAj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/771ulvamy5dkny45mj3x.png)

## 禀！

导航到 [CodePen](https://codepen.io/) 并创建一个新的“笔”，一个编辑器，你可以在其中编写一些代码来激活你的 SVG。

[![new code pen](img/43fba0bff6fa32572b16d2bfefbf1363.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0mz6SHMM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hsopk5pbqq2nmchapjpe.gif)

在新的编辑器中，你会注意到顶部有三个文本区域，标题分别是“HTML”、“CSS”和“JS”。超文本标记语言、级联样式表和 JavaScript 是用于创建网页和应用程序的标准语言。HTML 描述了网页的内容和结构，CSS 描述了 HTML 的风格和表现形式。忽略本教程的 JS 部分。

### HTML

将从 [SVGOMG](https://jakearchibald.github.io/svgomg/) 复制的 SVG 代码粘贴到 HTML 区域，这是你将使用 CSS 制作动画的内容。您的 SVG 将出现在下面的白色区域。

[![code pen with HTML](img/31eeba4440f4cdfa0abe175899390270.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6f0gvWfn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f50hcluyzeqcd0uxaf62.png)

**不要编辑这段 HTML 代码，注意它的结构。在顶部和底部，您可以看到``和``。这些标签被称为“开始标签”和“结束标签”。它们表示 HTML 元素的开始和结束，在本例中是一个 SVG。HTML 元素可以嵌套，这意味着它们可以包含其他元素。开始和结束 SVG 标记之间的所有内容都是 SVG 的一部分。你应该认识被附上的`path`标签。路径标签看起来有点不同`<path d="[your path coordinates]" />`。它不包含任何其他 HTML 元素，所以它是“自封闭的”。**

### CSS

现在您可以使用 CSS 来描述`svg`和`path` HTML 元素。CSS 代码遵循以下语法:

```
selector {
  property: value;
} 
```

Enter fullscreen mode Exit fullscreen mode

一个`selector`是作为目标的 HTML 元素。一个`property`是可以被修改的元素的质量，一个`value`指定该质量应该看起来如何或者如何表现。一个 CSS 文档可以有许多选择器，选择器可以有许多属性/值对。要进行演示，请将以下代码粘贴到 CodePen CSS 区域。

```
svg {
  height: 100vh;
  width: 100vw;
  background-color: BurlyWood;
} 
```

Enter fullscreen mode Exit fullscreen mode

CSS 选择器的目标是整个 HTML 元素。SVG 的高度和宽度应该已经改变，以填充代码下方的整个窗口。值`100vh`和`100vw`指定 SVG 的高度和宽度应该分别是视图高度和视图宽度的 100%。如果您调整显示区域或浏览器窗口的大小，SVG 也会随之调整大小。高度`50vh`将指定窗口高度的一半。您的 SVG 也有一个有趣的新背景颜色！你可以从一个[完整的命名 CSS 颜色列表](https://css-tricks.com/snippets/css/named-colors-and-hex-equivalents/)中选择你自己的颜色。

接下来，瞄准`path`元素。在新的一行中，将以下代码粘贴在`svg`选择器的右括号(`}`)之后:

```
path {
  stroke-dasharray: 100;
} 
```

Enter fullscreen mode Exit fullscreen mode

“笔画”是沿着路径的线。到目前为止，路径一直是用实线绘制的，意思是一条连续的线。`stroke-dasharray`属性使一个笔画成为虚线，其值指定每个虚线的长度。

[![path with dash array](img/bda7f83aea04c4ac323505fb39733211.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DQCumhsv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vqmsz2z0rbm1wcex007y.gif)

接下来，定义动画。将以下关键帧定义粘贴到 CSS 区域的新行中。您还看不到 SVG 的任何变化。

```
@keyframes draw {
  from {
    stroke-dashoffset: 0
  }
  to {
    stroke-dashoffset: 200;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`@keyframes`是描述行为的“at-rule”或 CSS 关键字。这个描述了动画中的步骤。接下来的单词“draw”是这个动画的名字，以后用来指代这个动画。你可以给动画起任何你喜欢的名字。`from`和`to`是关键帧选择器，用于指定动画开始和结束时的样子。

该关键帧定义描述了`stroke-dashoffset`属性的渐变。它指定开始和结束值；CSS 关键帧可以填充其余部分。`stroke-dashoffset`属性描述了笔画开始的位置。其值指定距离路径起点的距离。通过设置`stroke-dashoffset`属性的动画，您实际上是在动画过程中沿着路径推动笔划。

[![stroke dashoffset example](img/89219fcc2a87c780fc3d01c80e080a8a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DQxWknHL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/umr7sac43aj1cpcfcbi0.gif)

最后，是时候应用动画了。在你的`path` css 选择器中添加`animation: draw 2s infinite`。animation 属性可以取几个值。这个例子接收`draw`关键帧动画的名称，`2s`以秒为单位的动画持续时间，`infinite`指定动画应该无限重复的关键字。你的完整 CSS 应该是这样的:

```
svg {
  height: 100vh;
  width: 100vw;
  background-color: white;
}

path {
  stroke-dasharray: 100;
  animation: draw 2s infinite;
}

@keyframes draw {
  from {
    stroke-dashoffset: 0
  }
  to {
    stroke-dashoffset: 200;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦您的 SVG 开始运行，尝试使用`stroke-dasharray`和`stroke-dashoffset`值来找到适合您的图像的破折号长度。对于一个平滑的动画，确保你的“到”`stroke-dashoffset`值是`stroke-dasharray`值的倍数(至少两倍)。

本教程中使用的所有 CSS 属性都可以有不同的值。它们的组合可以极大地改变动画的效果。

[![pear examples](img/d4e715f2e385f1798c9c75f535544a32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9nhKc62K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ox0xrxrauhfhxp2ua9yn.gif) 
在 [CodePen](https://codepen.io/OppNHeimer/pen/EeBzMw) 上查看这个例子的代码！

特别是，读入[动画](https://developer.mozilla.org/en-US/docs/Web/CSS/animation)、[笔画-dasharray](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/stroke-dasharray) 和[@关键帧](https://developer.mozilla.org/en-US/docs/Web/CSS/@keyframes)来进一步定制您的动画。还有许多其他的 [CSS 属性可以应用于 SVG](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute)

现在您已经有了构建动画 SVG 线条画的工具。给你的下一个项目增加一点动感！
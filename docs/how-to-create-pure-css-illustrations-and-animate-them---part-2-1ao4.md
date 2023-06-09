# 如何创建纯 CSS 插图并制作动画-第 2 部分

> 原文：<https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-2-1ao4>

这是关于 CSS 插图和动画的三部分系列的第二部分。在这一部分，我们将建立一个 CSS 宝丽来，然后学习如何制作动画。

[第 1 部分:使用 CSS 笑脸](https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-1-1j1k)
**学习基础知识和工作流程提示第 2 部分:使用 CSS 宝丽来**
[介绍 CSS 动画第 3 部分:使用 CSS 灯塔场景的更高级技术](https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-3-3e8a)

这是我们将要建造的:

[![](img/c8f8270ed058f88100301be4f28e8931.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G4B-2rCH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rabeuioxm4nlytgkzrbd.jpg)

如果你想试着自己建造它，在这里停下来。这里没有我们在上一部分没有看到的东西。不要太担心确切的颜色和尺寸，记住通常有多种方法可以达到相同的效果。

好吧，我们开始吧。让我们仔细看看这张图片，并对其进行分解。它由相当简单的形状组成，大部分是圆角矩形和圆形，我们可以很容易地用基本的 CSS 重新创建它们。让我们看看基本的 HTML 树是什么样子:

```
.polaroid
  .button
  .flash
  .polaroid-body
  .blinker
  .zoom
  .stripes 
```

Enter fullscreen mode Exit fullscreen mode

元素充当我们的主容器，并将作为所有其他元素定位的参考。

让我们从为 html 和主体选择器设置一些基本属性以及一些颜色变量开始:

```
$background: #b5e8fb;
$grey: #34495e;
$dark-grey: #212f3d;

html, body { 
  height: 100%; 
  width: 100%; 
  padding:0;
  margin:0;
  overflow: hidden; 
} 
html { 
  box-sizing: border-box; 
} 
*, *:before, *:after { 
  box-sizing: inherit; 
}
body { 
  background: $background; 
} 
```

Enter fullscreen mode Exit fullscreen mode

我们图中的所有元素都需要有一个绝对位置，所有的`:before`和`:after`伪选择器也是如此。

```
* { 
  position: absolute; 
}
*:before, *:after { 
  content: ""; 
  position: absolute; 
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们将主容器在页面上垂直和水平居中，并为其指定宽度和高度。

```
.polaroid { 
  width: 420px;
  height: 280px;
  top: 50%; 
  left: 50%;
  transform: translate(-50%, -50%);
 } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的环境设置好了，我们可以开始制作不同形状的拍立得了。首先，我们需要一个代表相机机身的`.polaroid-body`元素。我们可以使用 SASS 的变暗功能来生成与阴影背景相匹配的阴影。它给人一种比使用纯黑色更柔和的感觉。

```
.polaroid-body {
  width: 100%;
  height: 100%;
  border-radius: 40px;
  background-color: #fff;
  box-shadow: 10px 10px darken($background, 5%);
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/eeb929b3bcf1cc4cad0eb16e604f632a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HDeHSJM9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hpp64bnfy36y8ts2o5ys.jpg)

按钮很简单。我们想把它放在拍立得的主体后面，HTML 的自然流动会为我们处理好这一点。

```
.button { 
  width: 10%; 
  height: 5%; 
  top: -2%; 
  left: 14%; 
  background-color: #cb214a; 
  border-radius: 16px; 
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/61840f07853fce31aad8de66b53b2590.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aJWfRiCW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sdz3unu2namz57ch0w64.jpg)

因为我们需要两个闪光灯选择器，我们可以使用`:after` :

```
 .flash { 
   width: 24%; 
   height: 15%;
   top: -12%; 
   left: 38%; 
   background-color: $grey; 
   border-radius: 8px; 
   &:after { 
     width: 36%; 
     height: 60%; 
     background-color: lighten($grey, 30%); 
     border-radius: 5px; 
     top: 10%; 
     left: 32%; 
   } 
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/5fb3496cdf20f601d3c960738ae4f5b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v0qziR2d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vrid83mw3d3nyl999tb7.jpg)

信号灯也很简单:

```
.blinker {
  width: 15px;
  height: 15px;
  border-radius: 15px;
  background-color: $dark-grey;
  top: 15%;
  left: 15%;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/793b0b6b8b74ebcc0befd1a5997935e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8bdyX6hO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k3tmd16c56gv6apidpot.jpg)

让我们继续缩放:

```
.zoom { 
  width: 50%; 
  height: 75%;
  left: 25%; 
  top: 12.5%;
  background: $dark-grey; 
  border-radius: 50%; 
  border: 5px solid $grey; 
  box-shadow: 10px 10px rgba(0, 0, 0, 0.1); 
  overflow: hidden; 
  &:before { 
    width: 80%; 
    height: 80%; 
    top: 10%; 
    left: 10%; 
    border-radius: 50%; 
    background: #2c3e50; 
    border: 10px solid $grey; 
  } 
  &:after { 
    width: 40%; 
    height: 40%; 
    top: 30%; 
    left: 30%; 
    border-radius: 50%; 
    background: $dark-grey; 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

使用伪选择器和边框的组合，我们用一个 div 重新创建了缩放的不同部分。太棒了。

[![](img/fef1b9b03bb4524212581d0a3d6a9174.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6G3cPXRQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iyixcgmgx886yt671krw.jpg)

现在，我想为我们的缩放添加更多的细节，但是我们已经用完了伪选择器。让我们添加一个 div 到我们的 HTML:

```
.polaroid
  .button
  .flash
  .polaroid-body
  .blinker
  .stripes
  .zoom
    .reflections 
```

Enter fullscreen mode Exit fullscreen mode

```
.reflections { 
  background-color: #fff; 
  opacity: 0.3; 
  width: 100%; 
  height: 100%; 
  left: -35%; 
  top: -35%; 
  z-index: 99; 
  transform: rotate(-45deg); 
  &:after { 
    background-color: #fff; 
    width: 20px; 
    height: 20px; 
    border-radius: 50%; 
    left: 50%; 
    top: 105%; 
    opacity: 0.5; 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

主反射是由旋转 45 度的正方形产生的。我已经给 zoom 元素添加了属性`overflow:hidden`,只显示与它重叠的反射部分。用`:after`伪选择器，我添加了另一个小的光反射。

[![](img/0c138725a1481d6f009fd90f48b886df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--swST9Pnc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c04cwm1g0ou8my6anwr6.jpg)

我们的拍立得快好了。我们需要添加最后一个细节:代表品牌的彩虹条纹。实现这一点有不同的方法。最简单的方法是创建五个 div，并为每个 div 分配不同的背景颜色。我们也可以有伪选择器和边框的组合，就像我们对缩放所做的那样。另一种选择是应用带有色标的渐变。相反，我们将使用盒子阴影属性并链接多个值

```
.stripes {
  height: 7px;
  width: 50%;
  right: 0;
  top: 40%;
  box-shadow: 0px 7px rgb(214, 0, 121), 0px 14px rgb(230, 141, 21),
              0px 21px rgb(254, 200, 3), 0px 28px rgb(221, 229, 107),
          0px 35px rgb(62, 162, 48), 0px 42px rgb(1, 136, 194);
} 
```

Enter fullscreen mode Exit fullscreen mode

这再次表明，通常有多种方法可以达到相同的结果。做这件事没有对错之分，一切都取决于你自己的喜好。我喜欢让我的代码尽可能干燥，所以我通常会选择需要最少代码的方法。

我们完成了我们的宝丽来！
[![](img/924fe2035cd8092d616cd452db9206a5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--9igaPi6s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q4o88aqu0qjt4mm3j55b.jpg)

# CSS 动画

现在是我最喜欢的部分:制作插图动画！

web 开发中有[种不同的方式](https://css-tricks.com/comparison-animation-technologies/)来制作动画，几乎所有的方式都可以用来制作 CSS 图像的动画。但是，本着本系列主题的精神，我们当然会使用 CSS 动画。

#### 动画基础知识

CSS 动画分两步实现。

假设您想要创建一个旋转正方形的动画。

你首先需要一个`@keyframes`规则:

```
@keyframes rotation {
  0% {
    transform: rotate(0deg)
  }
  100% {
    transform: rotate(360deg)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个规则由两个关键帧选择器组成，0%和 100%，我们可以在其中添加 CSS 属性。这些属性将控制我们的正方形在动画中该点的外观。

在上面的例子中，正方形将从 0 度旋转到 360 度。

[![](img/f900bdfc76458417209e11afa494cc2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oRyWYilv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cbgifaba9am6xtwogkmt.gif)

这个规则中只定义了两个关键帧选择器，但是我们可以添加任意多个。假设我们不想让我们的正方形旋转一整圈，而是将
旋转到 180 度，然后再旋转回 0。可以通过一个额外的关键帧选择器来实现:

```
@keyframes rotation {
  0% {
    transform: rotate(0deg)
  }
  50% {
    transform: rotate(180deg)
  }
  100% {
    transform: rotate(0deg)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/30055ed93781d38a4027ed53782fb34f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a5mpxTv0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8o24vho7izki0t0nuye3.gif)

两个关键帧选择器之间的任何点代表从一个选择器到另一个选择器的过渡状态。例如，假设动画是线性的，在 25%时，正方形将旋转 90 度。

第二步是在带有动画属性的选择器中调用这个规则:

```
.square {
  animation-name: rotation;
  animation-duration: 5s;
  animation-timing-function: linear;
  animation-iteration-count: infinite;
} 
```

Enter fullscreen mode Exit fullscreen mode

或者用快捷键:

```
.square {   
  animation: rotation 5s linear infinite; 
} 
```

Enter fullscreen mode Exit fullscreen mode

这些动画子属性允许我们调用我们的`@keyframes`规则并设置参数，如持续时间、缓和、延迟等。其中大多数是可选的，但我们必须指定名称和持续时间。点击了解更多信息。

> 虽然我们不会在本教程中使用它们，但现实生活中的 CSS 动画声明和`@keyframes`规则都需要供应商前缀才能在所有现代浏览器中工作:

```
.square {
  -webkit-animation: rotation 5s linear infinite;
  -moz-animation:    rotation 5s linear infinite; 
  -o-animation:      rotation 5s linear infinite; 
  animation:         rotation 5s linear infinite; 
} 
```

Enter fullscreen mode Exit fullscreen mode

```
-webkit-@keyframes rotation {
  0% {
    transform: rotate(0deg)
  }
  100% {
    transform: rotate(360deg)
  }
}

-moz-@keyframes rotation {
  0% {
    transform: rotate(0deg)
  }
  100% {
    transform: rotate(360deg)
  }
}

-o-@keyframes rotation {
  0% {
    transform: rotate(0deg)
  }
  100% {
    transform: rotate(360deg)
  }
}

@keyframes rotation {
  0% {
    transform: rotate(0deg)
  }
  100% {
    transform: rotate(360deg)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 制作我们的 CSS 宝丽来动画

现在我们已经有了基本的东西，让我们来制作 CSS 宝丽来的动画。我想创建一个动画序列，其中首先按下按钮，然后闪光灯闪烁，然后变焦旋转，最后闪光灯熄灭。然后动画会无限重复。

通常，不建议使用 CSS 关键帧创建序列，因为没有简单的方法来链接多个独立的动画。取而代之的是，你必须把它们的时间安排得恰到好处，这样它们才能在正确的时间被触发。如果动画很复杂，这很快就会变成一种痛苦，但是这个很简单，我认为这是一个很好的练习！让我们开始吧。

首先，我们缺少一个元素:手电筒。赶紧补充一下:

```
.polaroid
  .button
  .flash
  .polaroid-body
  .blinker
  .stripes
  .zoom
    .reflections
.flashlight 
```

Enter fullscreen mode Exit fullscreen mode

```
.flashlight {
  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  background: rgba(white, 0.7);
  transition: all ease 0.2s;
  opacity: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我打破了规则，把手电筒元素放在了主容器的外面。这是因为我想让它占据整个屏幕的宽度和高度。我还设置了它的不透明度为 0，因为我们不想让它现在可见。

第一个动画将是按下按钮。它需要向下移动，然后像这样回到原来的位置:

```
@keyframes button {
  0% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(10px);
  }
  100% {
    transform: translateY(0);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以在`.button`选择器:
中调用这个规则

```
.button {
  //...
  animation: button 5s linear infinite;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/b2e73729325d3539dbdb699764f1f68f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zqat0lYu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bbxn7o8j0xth164u0d7b.gif)

这看起来不太对，动画太慢了。我们已经将动画的持续时间设置为 5s，但这应该是整个序列的长度，包括所有四个动画。我们需要缩短这个的长度。让它在 5%到 15%之间运行。

```
@keyframes button {
  0% {
    transform: translateY(0);
  }
  5% {
    transform: translateY(0);
  }
  10% {
    transform: translateY(10px);
  }
  15% {
    transform: translateY(0);
  }
  100% {
    transform: translateY(0);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以走捷径，这样写:

```
@keyframes button {
  0%, 5%, 15%, 100% {
    transform: translateY(0);
  }
  10% {
    transform: translateY(10px);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果我们仔细观察时间线，我们会发现按钮在 0%到 5%或 15%到 100%之间没有被激活。动画只在 5%到 15%之间运行，在 5s 时间轴中的任何其他点，它都处于原始的、未变形的状态:(`translateY(0)`)。

因此不需要开始和结束关键帧选择器:

```
@keyframes button {
  5%, 15%{
    transform: translateY(0);
  }
  10% {
    transform: translateY(10px);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我在上面说“原始状态”是因为`.button`元素一开始就没有被赋予 transform 属性。例如，如果我们有这个:

```
.button {
  transform: translateY(-20px);
} 
```

Enter fullscreen mode Exit fullscreen mode

那么上面的快捷键就不起作用了，因为从 15%到 100%按钮会转换回那个位置。

[![](img/cac405f20e9d9ad6cc2689f7096af74f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zawau0Es--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r2emqml3mxo9qvjfptn9.gif)

现在让我们继续第二个动画，缩放旋转。让它从 20%开始，在 6%的总持续时间内旋转 20 度，然后再旋转 6%回到初始位置。

```
@keyframes zoom {
  20%, 32%  {
    transform: rotate(0deg);
  }
  26% {
    transform: rotate(20deg);
  }
}

.zoom {
  //...
  animation: zoom 5s linear infinite;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/bc544737c0db5a1af1bf4d43619c6d25.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ivztcan4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h3heiuenysapeguedydv.gif)

酷毙了。现在让我们制作闪光灯的动画。我们将动画背景颜色，使其闪烁三次红色。

```
@keyframes blinker {
  33%, 37%, 39%, 43%, 45%, 50% {
    background-color: $dark-grey;
  }
  34%, 36%, 40%, 42%, 46%, 48% {
    background-color: $red
  }
}

.blinker { 
  //... 
  animation: blinker 5s linear infinite; 
} 
```

Enter fullscreen mode Exit fullscreen mode

它从 33%开始，然后过渡到总持续时间的 1%的红色背景，然后暂停 2%，然后过渡回灰色 1%。这个序列又重复了两次。

在这种情况下，您可以看到链接或循环独立动画的可能性是多么有用。如果能够定义一个闪烁动画，然后设置它重复两次，而不是必须定义这么多关键帧选择器，那就太好了。唉，CSS 没有提供这个选项，所以我们必须借助这种计算来得到我们想要的效果。

[![](img/46e241db7ee74a2b823ded94e8eec29b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KJakgTN6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/khyboiooumnhu5c4ss99.gif)

最后，flash 动画:

```
@keyframes flashlight {
  55%, 65% {
    opacity: 0;
  }
  56% {
    opacity: 1;
  }
}

.flash-light { 
  //... 
  animation: flashlight 5s linear infinite; 
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/037a1453642d764b69f3654eae51cf66.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O-gvC-L4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hxjg6ai41hqwqgpnalf6.gif)

我们的动画可以到此为止了。在 65%和 100%之间什么都不会发生:这将在动画重新开始之前创建一个很好的暂停。

我们可以做的最后一件事是将动画的持续时间存储到一个变量:

```
$duration: 5s;

.button {
  //...
  animation: button $duration linear infinite;
}
.zoom {
  //...
  animation: zoom $duration linear infinite;
}
.blinker { 
  //... 
  animation: blinker $duration linear infinite; 
}
.flash-light { 
  //... 
  animation: flash-light $duration linear infinite; 
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们决定改变动画的速度，这将使它变得非常容易。

这是 CodePen 的最后一个项目。
[https://codepen.io/agathaco/embed/BOpBbR?height=600&default-tab=result&embed-version=2](https://codepen.io/agathaco/embed/BOpBbR?height=600&default-tab=result&embed-version=2)T2】

到现在为止，你应该对如何创建纯 CSS 图像有了很好的理解。让我们最后一次练习所有这些概念。在这个系列的第三部分，也是最后一部分，我们将看看一些新的技术来建立一个动画灯塔场景。

第三部分:更先进的 CSS 灯塔场景技术
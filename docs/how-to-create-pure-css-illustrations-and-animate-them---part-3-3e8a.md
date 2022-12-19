# 如何创建纯 CSS 插图和动画-第 3 部分

> 原文：<https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-3-3e8a>

这是关于 CSS 插图和动画的三部分系列的第三部分。在这最后一部分，我们将建立一个动画灯塔场景。为此，我们将研究一些新技术，如 SASS 循环和 CSS 3D 变换。

[第 1 部分:使用 CSS 笑脸](https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-1-1j1k)
[学习基础知识和工作流程提示第 2 部分:使用 CSS 宝丽来](https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-2-1ao4)
**介绍 CSS 动画第 3 部分:使用 CSS 灯塔场景的更高级技术**

这是我们将要建造的:

[![](../Images/902ce3088a5389f35820f5fcf5c5a3fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FMn19yJt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/05hsnvrtbdnb7f9rte4k.jpg)

因为这个图比前几个图要复杂一点，我们将快速浏览一下我们已经看到的内容。然而，我将介绍一些新的技术，如 CSS 3D 变换，重复梯度和 SASS 循环。

在我们开始之前，让我们观察一下这个图是由哪些组件组成的，以及它们是如何重叠的。我们的场景由两个主要部分组成，背景和灯塔。我们需要在 z 桩的底部设置背景，在顶部设置灯塔。这两个组都包含许多重叠的元素，很容易混淆。让我们利用 z-index 属性来确保每个元素都在堆中的正确位置。

这是我们的基本 HTML/Pug 树的样子。稍后我们会添加一些新的元素。

```
.scene
  .background
    .stars
    .moon
    .mountains
    .sea
      .waves
      .boat
  .lighthouse-group
    .land
    .lighthouse-holder
      .shadow
      .lighthouse
      .top
      .windows
      .door
        .stairs 
```

Enter fullscreen mode Exit fullscreen mode

在 SCSS 中，让我们定义颜色变量和全局属性:

```
$x-dark: #29284c;
$dark: #4c4b82;
$medium: #717ae1;
$light: #b9befa;
$x-light: #d6d9f6;
$aqua: #75e2fa;
$grey: #9e9ebe;
$yellow: #f7f2b4;

html,
body {
  height: 100%;
  width: 100%;
  overflow: hidden;
  padding: 0;
  margin: 0;
}
html {
  box-sizing: border-box;
}
*,
*:before,
*:after {
  box-sizing: inherit;
}
* {
  position: absolute;
}
*:before,
*:after {
  content: "";
  position: absolute;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将首先在背景上工作:

```
.scene {
  width: 100vw;
  height: 100vh;
}
.background {
  background: $x-dark;
  background-image: linear-gradient(
    $x-dark 0%,
    $dark 10%,
    $medium 60%,
    $aqua 90%
  );
  width: 100%;
  height: 100%;
  overflow: hidden;
  z-index: 1;
}
.sea {
  background: $x-dark;
  background-image: linear-gradient(
    to top,
    $x-dark 0%,
    $dark 30%,
    $medium 60%,
    $aqua 90%
  );
  width: 100%;
  height: 170px;
  bottom: 0;
  left: 0;
  z-index: 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

元素将作为我们的主容器，我们希望它和我们的屏幕一样宽一样高。`.background`是我们的第一层，`.sea`放在上面。我们使用`background-image`属性对两个元素应用渐变。同样，在现实生活中，我们将不得不对该属性使用供应商前缀，但是为了简洁起见，让我们省略它们。

[![](../Images/ead8c3e4854a2ea8bb05bf7d3b89ee7d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I1fqzumg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wi1ixt8hxihg0thp21hm.jpg)

#### 使用循环来生成和随机化内容

去星星上。我们想要创建大约 60 颗星，所以我们需要 60 个 HTML 元素。我们还需要为这些`.star`元素中的每一个生成一个唯一的位置。这意味着 60 个不同的 CSS 类，或者 60 次使用`nth-child`选择器。我们真的不想这样做，因为它听起来真的很长和重复。相反，让我们来一个循环。

在帕格和 SCSS 中，就像在 Javascript 中一样，循环是一个强大的工具。除了其他功能之外，它们还允许您轻松、自动地生成内容。它是这样工作的。

首先我们需要用 Pug 创建 60 个`.star`元素。Pug 循环就是简单的 [javascript 循环](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-javascript)，语法略有不同:

```
- for (var x = 0; x < 60; x++)
  .star 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！只有两行代码！

现在我们需要设计这些元素的样式。首先，我们创建一个包含所有`.star`元素的`.stars`容器。然后我们可以为它们定义通用的样式。(我们稍后将创建闪烁动画)。

```
.stars {
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 1;
  .star {
    border-radius: 50%;
    background-color: $light;
    animation: twinkle 5s ease-in-out infinite;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的插图中，我们仍然看不到任何星星，因为我们还没有设定位置和大小。我们需要每个恒星都有不同的属性。就像我们之前说的，我们可以一个接一个地定位这些元素，并手动为每个元素编码不同的值，但是我们已经决定不这么做了。SASS 循环将帮助我们随机生成这些值。下面是一个基本循环的语法:

```
@for $i from 1 through (60) {
 // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

为了给每个元素指定不同的值，我们可以使用第 n 个子选择器:

```
@for $i from 1 through (60) {
  .star:nth-child(#{$i}) {
    // do something
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 CSS 中这将编译成:

```
.star-nth-child(1) {
  // do something
}

.star-nth-child(2) {
  // do something
}

...

.star-nth-child(60) {
  // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

为了随机化这些值，我们使用了 SASS 随机函数:

```
@for $i from 1 through (60) {
  .star:nth-of-type(#{$i}) {
    top: random(100)+vh;
    left: random(100)+vw;
    width: random(4)+px;
    height: random(4)+px;
    animation-delay: random(5)+s;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

random 函数将一个整数作为参数，并返回一个介于 1 和传递的整数之间的随机值。所以`random(100)`会返回一个 1 到 100 之间的随机数。然后我们添加我们想要的单位作为后缀。我们希望星星遍布整个屏幕，所以我用 vw/vh 单位来表示`top`和`left`属性。它会将星星放置在屏幕上的随机位置，因为随机函数是在循环中定义的，所以它会为每个星星调用，每次都生成一个新位置。

这很容易设置，但我们有一个问题。如果你看星星，你会发现它们不是圆的。这是因为 height 和 width 属性被赋予了不同的值，这些值是由 random 函数的不同实例生成的。为了解决这个问题，我们需要将随机函数的结果存储在一个变量中:

```
@for $i from 1 through (60) {
  $size: random(4)+px;
  .star:nth-of-type(#{$i}) {
    top: random(100) + vh;
    left: random(100) + vw;
    width: $size;
    height: $size;
    animation-delay: random(5) + s;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

开始了。我们的星星还没有动画，但我们稍后会解决这个问题。

[![](../Images/a6d5de29a74201e015bc101551ff2463.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eYHW3pH7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xzwyevvf3ceyyzabgntp.jpg)

* * *

让我们为我们的背景添加更多的细节:

月亮是用一个简单的边界半径创建的，结合`box-shadow`属性创建一个围绕它的光晕:

```
.moon {
  width: 80px;
  height: 80px;
  top: 25%;
  right: 10%;
  border-radius: 50%;
  z-index: 2;
  background-color: $x-light;
  box-shadow: 0 0 10px $x-light, 0 0 20px $x-light, 0 0 30px $x-light, 0 0 40px $x-light, 0 0 50px $aqua, 0 0 100px $x-light;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/42dd8b5b220ba9b442952f3fd67bf214.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_xsiLXXw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b4s203xvp9yqr34x5gmu.jpg)

对于山脉，我们再次使用 Pug 循环来创建四个元素。

```
.mountains
  - for (var i = 0; i < 4; i++)
    .mountain 
```

Enter fullscreen mode Exit fullscreen mode

每座山本质上都是一个旋转了 45 度的正方形，一半隐藏在`.sea`元素后面。然后对于每一个，我们使用`:after`选择器来添加渐变。我们可以使用`nth-child`选择器给它们不同的位置和大小。

```
.mountains {
  width: 100%;
  height: 250px;
  bottom: 65px;
  z-index: 3;
  .mountain {
    width: 250px;
    height: 250px;
    background-color: $medium;
    right: 50px;
    bottom: -40px;
    transform: rotate(45deg);
    border-radius: 3px;
    &:after {
      width: 100%;
      height: 100%;
      opacity: 0.7;
      background-image: linear-gradient(135deg, $dark 0%, $medium 20%, $aqua 40%);
    }
  }
  .mountain:nth-child(2) {
    right: 220px;
    width: 240px;
    height: 240px;
    z-index: 2;
  }
  .mountain:nth-child(3) {
    right: 350px;
    width: 260px;
    height: 260px;
  }
  .mountain:nth-child(4) {
    right: 130px;
    width: 200px;
    height: 200px;
    z-index: 3;
    bottom: -70px;
    &:after {
      background-image: linear-gradient(135deg, $dark 0%, $medium 6%, $aqua 20%);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/49ebac0e36dd8e3ff6a26f75550811d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZOxF5LdA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6cosroux6dw2bbe6uoh9.jpg)

为了给大海添加波浪，我再次使用了 Pug 和 SASS 循环:

```
.sea
  - for (var i = 0; i < 30; i++)
    .wave 
```

Enter fullscreen mode Exit fullscreen mode

```
.wave {
  background-color: $x-light;
  height:3px;
  border-radius: 100%;
  opacity: 0.2;
  animation: wave 5s linear infinite;
}
@for $i from 1 through (30) {
  $size: random(100) + 50px;
  .wave:nth-of-type(#{$i}) {
    bottom: random(170) + px;
    left: random(100) + vw;
    width: $size;
    opacity: random (5) * 0.1;
    animation-delay: random(3) + s;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/2a8108aafe931bdde843288ad9ecf24c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9LMS1yKl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r7bv5ag94dcubmpxui2l.jpg)

背景中的最后一个元素是船。它由三个主要部分组成:一个底座和两个帆。为了创建船帆的底部和三角形，让我们使用[裁剪路径](https://css-tricks.com/almanac/properties/c/clip/)属性。[这个工具](https://bennettfeely.com/clippy/)非常适合动态创建剪辑路径形状。

```
.boat {
  width: 90px;
  height: 90px;
  bottom: 90px;
  .base {
    width: 110px;
    height: 25px;
    bottom: -20px;
    clip-path: polygon(0 0, 100% 0, 100% 100%, 20% 100%);
    background-color: $dark;
  }
  .sail:nth-child(1) {
    width: 90px;
    height: 80px;
    left: 5px;
    clip-path: polygon(50% 0%, 0% 100%, 50% 100%);
    background: linear-gradient($light 0%, $dark 60%);
  }
  .sail:nth-child(2) {
    width: 80px;
    height: 70px;
    left: 15px;
    bottom: 10px;
    transform: scaleX(-1);
    clip-path: polygon(50% 0%, 0% 100%, 50% 100%);
    background: linear-gradient($light 0%, $dark 60%);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 第二个帆上的 transform: scaleX(-1)用于水平翻转它。

使用`:before`和`:after`选择器，我们可以添加阴影和轨迹。属性`z-index:-1`确保这些元素被放置在它们的兄弟元素之后:

```
.boat {
  width: 90px;
  height: 90px;
  bottom: 90px;
  &:after {
    height: 8px;
    width: 200px;
    background: linear-gradient(90deg, transparentize($x-light, 0.3) 30%, rgba(255, 255, 255, 0) 100%); 
    border-radius: 40%;
    top: 105px;
    left: 20px;
    z-index: -1;
  }
  &:before {
    width: 92px;
    height: 50px;
    left: 25px;
    bottom: -70px;
    background: linear-gradient(to bottom, transparentize($x-dark, 0.2) 30%, transparentize($x-dark, 1) 100%);
    z-index: -1;
  }
  //...
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/c193628d97aa96bcde102bb530ec07d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CdGtcbLK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i3fonc0e737a07nv63rz.jpg)

#### 使用 CSS 变换

我们插图的背景部分已经完成。我们去灯塔吧。

我们将使用一个`.lighthouse-group` div 作为这部分插图的主容器。它有两个主要组成部分，土地和实际的灯塔。让我们通过指定大小和位置将`.lighthouse-group`放置在文档上:

```
.lighthouse-group {
    width: 50%;
    height: 100%;
    bottom: 0;
    left: 0;
    z-index: 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

先把那块地加上:

```
.land {
  width: 400px;
  height: 60px;
  left: -30px;
  bottom: 0;
  background-image: linear-gradient(to top, $x-dark 80%, $medium 100%);
  transform: skewX(35deg);
  border-radius: 10px;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 transform 属性，我们可以[倾斜](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/skew)元素，然后将其向左移动并隐藏左侧。

[![](../Images/36ac2004dd858290e97a89087e989a34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rW7V4CEP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g91wkkzdx6hpke979tfx.jpg)

现在对于实际的灯塔，我们可以从给灯塔组一个大小和位置开始:

```
.lighthouse-holder {
  height: 480px;
  width: 100px;
  bottom: 80px;
  left: 180px;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，为了使灯塔的顶部更窄，我们将使用两个变换属性，`perspective`和`rotateX`。

`perspective`属性为 lighthouse 元素设置一个 3D 空间，然后`rotateX`属性在这个 3D 空间的 X 轴上旋转它。从技术上来说，灯塔的顶部离我们更远，而底部更近，但这给了我们想要的空中飞人效果。

```
.lighthouse {
  width: 100%;
  height: 100%;
  transform: perspective(600px) rotateX(20deg);
  background-color: $x-light;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/bbb89608e38d2326d81afa4ef63802a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--joegRnmv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tvra2td6po862hb3wcyi.jpg)

如果你想了解更多关于 CSS 3D 转换的知识，这里有一个很好的链接。

接下来，创建条纹，让我们使用[重复渐变](https://css-tricks.com/snippets/css/css-repeating-gradients/) :

```
.lighthouse {
  width: 100%;
  height: 100%;
  transform: perspective(600px) rotateX(20deg);
  background-color: $x-light;
  background-image: repeating-linear-gradient(
    -40deg,
    transparent,
    transparent 60px,
    $dark 60px,
    $dark 120px
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/a8c9a6c35cc88f3d2255cdfe9ba940f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M4-GYhTJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/10ctszbb0bnu3b9c3um1.jpg)

我还想添加一些阴影与另一个梯度层在顶部。我们将使用`:after`伪选择器:

```
.lighthouse {
  width: 100%;
  height: 100%;
  transform-style: preserve-3d;
  transform: perspective(600px) rotateX(20deg);
  background-color: $x-light;
  background-image: repeating-linear-gradient(
    -40deg,
    transparent,
    transparent 60px,
    $dark 60px,
    $dark 120px
  );
  &:after {
    width: 100%;
    height: 100%;
    opacity: 1;
    background-image: linear-gradient(
      90deg,
      transparentize($x-light, 0.4) 0%,
      $x-dark 8%,
      transparent 70%,
      transparentize($x-light, 0.6) 100%
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要在灯塔底部增加一个阴影。我们可以再次使用`skewX`属性:

```
.shadow {
  width: 117px;
  height: 50px;
  left: -32px;
  bottom: -70px;
  background: $x-dark;
  transform: skewX(-45deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/1e6af3d3ee144f0d5c7c141a8bb8c748.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5xi7crhZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eatw3qr8uqjwgcrei82t.jpg)

为了创建窗口，我们首先需要向 HTML 添加四个`.window`元素。为了将它们隔开，我们可以像以前一样使用一个 SASS 循环。然而这一次，我们可以做一个简单的加法，而不是使用 random 函数。

首先，我们将底部属性的初始值设置为`90px`。然后，在循环的每一次运行中，我们给这个值加上 90px。最终结果是四个相同的`.window`元素，每个元素在垂直方向上相隔 90px。

```
.windows
  - for (var i = 0; i < 4; i++)
    .window 
```

Enter fullscreen mode Exit fullscreen mode

```
.windows {
  height: 100%;
  width: 100%;
  .window {
    background-color: $x-dark;
    height: 25px;
    width: 15px;
    left: 43px;
    border-bottom: 2px solid rgba($light, 0.7);
    border-radius: 25px 25px 0 0;
  }
  $bottom: 90px;
  @for $i from 1 through (4) {
    .window:nth-of-type(#{$i}) {
      bottom: $bottom;
    }
    $bottom: $bottom + 90px;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/4d852377150552a6a3037ec5b5b45115.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v-FDvE9V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6j102usz8fadj9q2kbrs.jpg)

这门很简单。对于楼梯，让我们再次使用`perspective`和`rotateX`的组合来得到一个梯形。那么重复的梯度将产生台阶。

```
.door {
  background-color: $x-dark;
  height: 40px;
  width: 25px;
  left: 38px;
  bottom: -2px;
  border-radius: 2px 2px 0 0;
  .stairs {
    width: 27px;
    height: 28px;
    background-color: $x-dark;
    top: 34px;
    left: -1px;
    transform: perspective(100px) rotateX(45deg);
    background-image: repeating-linear-gradient(
      to bottom,
      $x-dark,
      $x-dark 4px,
      $light 4px,
      rgba(white, 0.1) 5px
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/7ef3480d788f77f86673cc52a5560120.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LWU-mVfa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v0t1utcm75t0uli6i7p5.jpg)

好了，灯塔的底部完成了。让我们继续看最上面的部分。

首先我们需要添加一些 HTML 元素:

```
.top
  .light-container
    .light
  .rail
  .middle
  .roof
    .roof-light
  .glow 
```

Enter fullscreen mode Exit fullscreen mode

我们首先为`.top`容器设置大小和位置，然后，我们可以开始处理结构的`.base`部分。我们再次使用`perspective` + `rotateX`技术给它一些形状，以及一个重复的渐变来创建轨道。

```
.top {
  width: 94px;
  height: 60px;
  left: 3px;
  top: -15px;
    .rail {
      width: 100%;
      height: 17px;
      bottom: 1px;
      border: 3px solid $x-dark;
      border-radius: 1px;
      transform: perspective(1000px) rotateX(-35deg);
      background-image: repeating-linear-gradient(
        90deg,
        $x-dark,
        $x-dark 3px,
        $grey 3px,
        $yellow 10px
      );
      background-position: -2px 0;
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/ea4e77e299ba760c77080a2a0a440a05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--27mhgPY7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e4ubomtk8yzuu6i91e6o.jpg)

除了我们不需要转换之外,`.middle`部分非常相似。我们还使用了`:before`选择器在它后面添加了一个漂亮的光晕:

```
.middle {
  width: 88px;
  height: 35px;
  left: 3px;
  bottom: 14px;
  border: 2px solid $x-dark;
  border-radius: 3px;
  background-image: repeating-linear-gradient(
    90deg,
    $x-dark,
    $x-dark 4px,
    $grey 4px,
    rgba(255, 255, 255, 0) 21px
  );
  background-position: -2px 0;
  &:before {
    width: 100%;
    height: 100%;
    z-index: -1;
    background-color: $yellow;
    box-shadow: 0 0 10px $x-light, 0 0 20px $yellow, 0 0 30px $yellow,
    0 0 40px $yellow, 0 0 70px $yellow;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/faf6b055000c085f0cd7b42bec2ca0e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v5Ibm76b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yl45romslg3q239sytxk.jpg)

对于屋顶，我们可以使用[边界技术](https://css-tricks.com/snippets/css/css-triangle/)来创建一个三角形。

有几种方法可以在 css 中创建三角形。之前我们使用了裁剪路径的方法来创建船的三角帆。然而在这种情况下，我想使用`:before`和`:after`来创建屋顶的顶部，但是`clip-path`不让我这么做。

```
.roof {
  width: 0px;
  height: 0px;
  left: -3px;
  bottom: 45px;
  border-left: 50px solid rgba(255, 255, 255, 0);
  border-right: 50px solid rgba(255, 255, 255, 0);
  border-bottom: 40px solid $x-dark;
  &:before {
    width: 14px;
    height: 14px;
    left: -7px;
    bottom: -7px;
    background-color: $x-dark;
    border-radius: 50%;
  }
  &:after {
    width: 4px;
    height: 14px;
    left: -2px;
    bottom: 5px;
    background-color: $x-dark;
    border-radius: 3px;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/143991f46bbda4554f1f73ab5b1330c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ALPe63Y---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4a9zpun7hdyay79ltxog.jpg)

最后，我们需要在屋顶上添加一些光线。我们已经用完了伪选择器，所以我们需要创建一个新的 HTML `.roof-light`元素。这一次我使用了`clip-path`属性来确保渐变包含在屋顶中:

```
.roof-light {
  width: 100px;
  height: 40px;
  left: -50px;
  clip-path: polygon(50% 0, 0% 100%, 100% 100%);
  background-image: linear-gradient(
    135deg,
    $x-dark 40%,
    rgba($yellow, 0.5) 100%
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/e12d26fb51bbe4c29f3e0199a3485b0c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V2HtelRm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/derwf40plbkhmo1thju8.jpg)

我们插图的最后一个元素是光。因为我们马上要在 3D 空间中制作动画，所以我们需要两个元素。`.light-container`元素将用于从后向前旋转灯光。元素是用来给灯光一个梯形的形状:

```
.light-container {
  height: 40px;
  width: 35vw;
  bottom: 4px;
  left: 40px;
  transform-style: preserve-3d;
  transform-origin: left bottom;
  transform: perspective(500px) rotateY(0deg);
  .light {
    width: 100%;
    height: 100%;
    transform-style: preserve-3d;
    transform-origin: left center;
    transform: perspective(500px) rotateY(-35deg);
    background: linear-gradient(90deg, $yellow 40%, rgba(255, 255, 255, 0) 100%);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后一点是当光线转向我们时增加一点光亮。我们现在将设置它的不透明度为 0:

```
.glow {
  width: 100px;
  height: 60px;
  top: 0;
  left: 0;
  background-color: $yellow;
  opacity: 0;
  border-radius: 50%;
  box-shadow: 0 0 10px $yellow, 0 0 20px $yellow, 0 0 30px $yellow, 0 0 40px $yellow, 0 0 50px $yellow, 0 0 60px $yellow, 0 0 70px $yellow, 0 0 80px $yellow;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/cf7325812f933f2ee8224cd4b476c8e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F9ezMFh7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fqpawnoixmhq1ropbleh.jpg)

我们的插图完成了！这是大量的工作，但我们还没有完全完成。动画时间到了！

以下是我们要制作动画的元素:

*   星星会闪烁
*   波浪将从左向右移动
*   船将在屏幕上移动
*   船的影子会随着它远离月球而改变
*   灯光将从后向前旋转
*   当光线对着我们时，会出现辉光

星形动画很简单，因为我们只制作了不透明度属性的动画。我们之前已经给星星元素添加了一个随机的动画延迟，这可以确保动画不会对所有的星星同时运行。

```
@keyframes twinkle {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.3;
  }
}

.star {
  //...
  animation: twinkle 5s linear infinite;
} 
```

Enter fullscreen mode Exit fullscreen mode

波浪动画也很简单:

```
@keyframes wave {
  0%,
  100% {
    transform: translateX(0);
  }
  50% {
    transform: translateX(-10px);
  }
}

.wave {
  //...
  animation: wave 5s linear infinite;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们给船做动画。最初，它需要离开屏幕。我们可以使用`translateX`属性将船移动到屏幕的最右边。在动画中，船会出现在右边，然后慢慢移动到左边，然后又从屏幕上消失。我还添加了`scale(0.8)`值来稍微减小它的大小。

```
@keyframes boat {
  0% {
    transform: translateX(120vw) scale(0.8);
  }
  80%,
  100% {
    transform: translateX(-25vw) scale(0.8);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要制作它的影子在月亮前面经过，然后离开的动画:

```
@keyframes boatShadow {
  0% {
    transform: skewX(35deg) translateX(15px);
  }
  50%,
  100% {
    transform: skewX(-55deg) translateX(-40px);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$boatSpeed: 100s;

.boat { 
  //... 
  animation: boat $boatSpeed linear infinite; 
  &:after { 
    //... 
    animation: boatShadow $boatSpeed linear infinite; 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

灯光动画是最复杂的一个。它需要先旋转到后面，再旋转到前面，然后在动画结束时回到初始状态。

初始状态是我们已经在`.light-container`选择器中定义的状态。为了将它旋转到后面，我们将它在 Y 轴上旋转 35 度。然后旋转到前面，我们做同样的事情，但是用一个负值:

```
@keyframes lightRotate {
  0%,
  100% {
    // initial and final state
    transform: perspective(500px) rotateY(0deg);
  }
  25% {
    //rotates to the back
    transform: perspective(500px) rotateY(35deg);
  }
  75% {
    // rotates to the front
    transform: perspective(500px) rotateY(-35deg);
  }
}

$lightSpeed: 40s;

.light-container {
  //...
  animation: lightRotate $lightSpeed linear infinite;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们还需要在光线朝向我们旋转的时候让光晕出现:

```
@keyframes lightGlow {
  0%, 50%, 100% {
    opacity: 0;
  }
  75% {
    opacity: 1;
  }
}

.glow {
  //...
  animation: lightGlow $lightSpeed linear infinite;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们完了！

> 3D 变换可能有点难以理解，如果你有点纠结，这里有一个提示:为所有具有 3D 变换的元素添加边框。它将显示所有元素的形状和位置，包括隐藏的元素。

例:

```
.light-container {
  //...
  border: 1px solid white;
  .light {
    border: 1px solid pink;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是 Codepen 的最后一个项目。

[https://codepen.io/agathaco/embed/JaGQPr?height=600&default-tab=result&embed-version=2](https://codepen.io/agathaco/embed/JaGQPr?height=600&default-tab=result&embed-version=2)

# 下一步

因此，你已经按照教程，并建立和动画你的第一个 CSS 图像。现在怎么办？如果你不知道从哪里开始，我建议从复制现有的插图开始。在 [Dribbble](https://dribbble.com/) 上看，用自己喜欢的简单插画作为参考(只要给插画师加个赞，链接原图，完全没问题！).从一个模型开始会带走创造性，让你专注于代码和逻辑。当你对这个过程足够熟悉时，你会发现从头开始创建你自己的 CSS 图像会更容易。

根据经验，最好是“平面设计”的外观，有几何形状，没有纹理和平面颜色，因为它们更容易用 CSS 复制。不过话说回来，只要有一点想象力和实验，你就能取得惊人的成果。以下是一些令人印象深刻的纯 CSS 插图示例:

*   戴安娜·阿德里安娜
*   大卫·库尔希德的《在绳子上摇摆的猫》
*   朱丽娅·穆扎法罗娃的纯 CSS 小胡子保姆
*   只有 CSS 纸鸟作者中谷佑介
*   卡西迪·威廉姆斯的 CSS 相机
*   发光的流星

# 有用的工具和资源

*   [CSS 梯度发生器](https://cssgradient.io/)
*   [调色板生成器](https://coolors.co/)
*   [裁剪路径生成器](https://bennettfeely.com/clippy/)
*   [密码笔](https://codepen.io/)
*   [SASS](https://sass-lang.com/)
*   帕格

就是这样，伙计们！我希望您喜欢这个系列，并且在这个过程中学到了一些东西。在 [CodePen](https://codepen.io/agathaco) 上关注我，看看我在做什么，或者在 [Twitter](https://twitter.com/agathacco) 上联系我，保持联系。
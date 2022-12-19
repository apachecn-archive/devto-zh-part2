# 如何在 CSS3 中按顺序制作单杠动画？

> 原文：<https://dev.to/forethought_de/how-to-animate-horizontal-bars-sequentially-in-css3---2jn1>

我通常使用 JavaScript 来使网页上的项目按顺序显示。最近，我明白了我可以使用 CSS3 关键帧来按顺序制作项目动画。为了证明这一点，我在这里按顺序制作了条形动画(宽度从左到右增加)。

所以，我的 HTML 结构是这样的；在这里，每个条都用一个 div HTML 元素表示，并带有一个类 *bar* 。

```
<div class="bar-container">

  <div class="bar bar_0">

  </div>
  <div class="bar bar_1">

  </div>
  <div class="bar bar_2">

  </div>
  <div class="bar bar_3">

  </div>
  <div class="bar bar_4">

  </div>
  <div class="bar bar_5">

  </div>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

酒吧是一个接一个的动画；不同宽度的条形及其实际条形动画持续时间取决于它们的宽度。我在这个例子中使用了 SASS。

我首先设置了酒吧的宽度和高度以及它们的背景，如下图所示。

```
.bar {
  width: 100px; 
  height: 50px;
  background-color: rgba(66, 237, 148, 0.6);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我使用它们唯一的 CSS 类名为每个条设置特定的宽度。正如你所看到的，每个条设置不同的宽度和长度。bar_5 没有定义样式，因为我想让它继承 common 类的 width 属性。栏和其他属性将在以后设置。我使用了与浏览器窗口宽度相关的百分比值。

```
.bar {
  width: 100px;
  height: 50px;
  background-color: rgba(66, 237, 148, 0.6);

  &.bar_0 {
    width: 30%;
  }
  &.bar_1 {
    width: 50%;
  }
  &.bar_2 {
    width: 60%;
  }
  &.bar_3 {
    width: 70%;
  }
  &.bar_4 {
    width: 75%;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的 HTML 和 CSS 一起将呈现为。

[![Bars](../Images/c2146bbfce93def1d19ba7cb130cd711.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J1VZ_YXP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gasjfjva9z0oe63zdhi8.png)

现在最有趣的部分，**动画**。因为有 6 个小节，我们必须有 6 个不同的*@关键帧*来按顺序激活它们。让我先展示一下*@关键帧*，然后我会详细解释它们。

```
$percentages: (30, 50, 60, 65, 75, 100);
$factor: 20;
$interval: 0.5;
$previousAnimation: 0;
@for $i from 1 through 6 {
  @keyframes bar_animate_#{$i - 1} {
    @if $i > 2      
     {
      $previousAnimation: $previousAnimation + ($factor * nth($percentages, $i - 1) / 100) + $interval;
      0%, #{($previousAnimation)}% {
      transform: scaleX(0);
      transform-origin: left;
     } 
    }
    @else if $i == 2{
      $previousAnimation: $previousAnimation + ($factor * nth($percentages, $i - 1) / 100) + $interval;
      0%, #{$previousAnimation }%
      {
      transform: scaleX(0);
      transform-origin: left;
      }
    }
    @else
    {
      0%
      {
      transform: scaleX(0);
      transform-origin: left;
      }
    }

    #{$previousAnimation + ($factor * nth($percentages, $i) / 100)}%, 100%
      {
      transform: scaleX(1);
      transform-origin: left;
      }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我用*变换原点*从 scaleX(0)到 scaleX(1)制作动画。使用 transform-origin 的原因是告诉 transform 属性从左侧开始缩放，而不是从中心开始缩放(默认情况下)。

重要的部分是**动画开始计时**；这里用%(百分比)表示。让我们给 bar 元素添加动画。

```
.bar {
  width: 100px;
  height: 50px;
  background-color: rgba(66, 237, 148, 0.6);
  animation-duration: 10s; //Added now

  &.bar_0 {
    width: 30%;
    animation-name: bar_animate_0; //Added now
  }
  &.bar_1 {
    width: 50%;
    animation-name: bar_animate_1; //Added now
  }
  &.bar_2 {
    width: 60%;
    animation-name: bar_animate_2; //Added now
  }
  &.bar_3 {
    width: 70%;
    animation-name: bar_animate_3; //Added now
  }
  &.bar_4 {
    width: 75%;
    animation-name: bar_animate_4; //Added now
  }
  &.bar_5{
    animation-name: bar_animate_5; //Added now
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我添加了动画-持续时间和动画-名称；所有元素整体动画持续时间相同，但实际的条形图动画持续时间和开始时间不同。如果你仔细观察，我会将 bar_0 从 0 动画到 0.6 秒(10 秒中的 0%到 6%)，并且动画持续时间的其余部分保持不变，在此期间，所有其他的 bar 都不会动画，因为它们被设置为变换:scaleX(0)，基本上将宽度设置为 0(隐藏)。然后，我给下一个小节元素. 5s (.5%)的持续时间来开始动画，然后下一个小节动画持续 1 秒，同样的逻辑进行到最后一个小节元素。

这就是我如何能够仅用 CSS3 @关键帧按顺序制作元素动画的方法。

[CodePen 演示](https://codepen.io/anon/pen/OZpeWN)

如果您有任何问题或建议，请写在下面。谢谢
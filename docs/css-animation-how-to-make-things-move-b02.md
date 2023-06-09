# CSS 动画:如何让事物移动

> 原文：<https://dev.to/neshaz/css-animation-how-to-make-things-move-b02>

在 CSS 中使用动画的第一步是[过渡属性](https://kolosek.com/css-transition/)。当预定义的过渡不够或者不合适时，就有 CSS 动画。

**注意:本帖没有使用 JavaScript！**(连我都相信这个是清楚的)

动画属性有两个部分:关键帧和动画属性。关键帧定义动画的阶段和样式，animation 属性将关键帧分配给元素，并指定动画如何在定义的元素上播放。

## [关键帧](#keyframes)

通过定义关键帧，您实际上为动画设置了步骤。这部分动画的语法是单词`@keframes`，后跟动画名称，再后跟关键帧选择器及其样式。

```
 @keyframes animation-name{
     0%{CSS}
     50%{CSS}
     100%{CSS}
 } 
```

动画是通过由关键帧选择器设置的从一种风格逐渐改变到另一种风格来创建的。这些选择器的值可以是 0%-100%(或者单词“from”和“to”分别代表 0%和 100%)。我相信非常清楚，0%表示动画的开始，100%表示结束。

你可以制作动画的属性不是无限的，所以[有一个列表](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties)。

现在，关于 CSS 中的@keyframes 选项最令人震惊的是:
**`!important`在关键帧内部不仅使你的代码变得重要，而且它还忽略了整个代码行...！冲击**

## 动画属性

一旦定义了关键帧，你最好确保工作有回报并使用它们。在你想要制作动画的选择器内部添加`animation`属性。该属性是八个动画属性的简写。

```
 animation
 =
 animation-name
 animation-duration
 animation-timing-function
 animation-delay
 animation-iteration-count
 animation-direction
 animation-fill-mode
 animation-play-state 
```

**需要添加的主要属性有`animation-name`和`animation-duration`。没有`animation-name`你不能链接动画到关键帧，没有`animation-duration`持续时间将被设置为 0，同样，没有动画。**

其他属性用于更好地定义动画。`Animation-timing-function`指定动画的速度曲线。它可以用预定义的值(线性、缓动、缓入、缓出、缓出)来定义，也可以用[步长](https://designmodo.com/steps-css-animations/)或[三次贝塞尔曲线](http://cubic-bezier.com/#.17,.67,.83,.67)值来自定义。

`Animation-delay`属性与[过渡](https://kolosek.com/css-transition/)中的工作非常相似一它定义了动画的开始。

默认情况下，动画将播放一次。使用`animation-iteration-count`，您可以将其更改为任何数字或定义为`infinite`。

`Animation-direction`属性指定动画循环一是向前播放、向后播放还是交替循环播放。定义这些状态的选项有`normal`、`reverse`、`alternate`(向前然后向后)和`alternate-reverse`(向后然后向前)。

属性定义了动画结束时或开始前元素的样式。默认状态是`none`，在这种状态下，动画不会对动画元素应用任何样式。选项`both`将设置元素的第一个关键帧值，直到它开始，最后一个关键帧值结束后。选项`forwards`和`backwards`定义了与`both`分开的选项。

最后，用`animation-play-state`你可以暂停动画并再次播放。所以它有两个选项，`running`(默认)和`paused`。

*即使`animation`属性是所有列出选项的简写，通常也只是将`animation-name`和`animation-duration`与其他属性分开列出。*

```
 animation: icon-animation 2s;
 animation-timing-function:ease;
 animation-delay: 5s;
 animation-iteration-count:infinite;
 animation-direction:normal;
 animation-fill-mode:both;
 animation-play-state:running; 
```

为了便于记录，可以向元素中添加多个动画。

还有一个关于[前缀](https://kolosek.com/understanding-css-vendor-prefixes/)的简短说明:对于所有动画属性和关键帧，你都应该使用`-webkit-`。对于一些特定的已知问题，请检查[can use](https://caniuse.com/#feat=css-animation)。

## 例子

动画本身在设计中并不是一个有特定目的的东西。另一方面，它可以在任何地方使用，它总是会增值。我会说特定的动画(用这个属性定义的)用在特效上最多，用过渡定义的动画用在更常见的元素上，比如悬停和其他状态。

此外，悬停效果的一些影响，甚至只是一个不寻常的行为线是用动画属性定义的。

所以，如果你需要在你已经完成的设计中添加一些东西，你可以在背景中或者一些特定的元素上添加一个特殊的效果(加载、滚动、悬停...).

一个不错的博客，有一个更好的帖子，有最好的 CSS 动画示例:[https://www . creative bloq . com/inspiration/CSS-animation-examples](https://www.creativebloq.com/inspiration/css-animation-examples)。

## 后记

在我看来，动画属性是 CSS 中最高的属性之一，所以如果不是因为 wow 效果，你需要它来实现你的“主控 CSS”目标。如果你对 UX 感兴趣(如果不感兴趣，你可能应该感兴趣)，看看来自 invisionapp 团队的一篇非常好的关于动画的文章。

如果你需要帮助使用这个 CSS 属性，一定要联系 Kolosek 团队。你可以通过[科洛塞克的推特账号](https://twitter.com/kolosekit)或者给 office@kolosek.com[发邮件](mailto:office@kolosek.com)找到我们的设计师。他们迫不及待地想听听你的想法！

本文最初发表于 [Kolosek 博客](https://kolosek.com/css-animation/)。
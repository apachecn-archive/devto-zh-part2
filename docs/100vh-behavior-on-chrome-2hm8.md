# Chrome 上的 100vh 行为

> 原文：<https://dev.to/peiche/100vh-behavior-on-chrome-2hm8>

在我的一个 WordPress 主题中，帖子和页面上的特色图片占据了整个屏幕。它们作为背景图像，标题垂直和水平居中。在屏幕的底部，有一个小箭头图标，你可以点击它(或者点击，如果你用的是触摸设备的话)，这将触发滚动到页面的开始。

在 CSS 中，100%的高度是这样实现的:

```
min-height: 100vh; 
```

Enter fullscreen mode Exit fullscreen mode

(我最初使用高度，但我发现长标题在小屏幕上会被截断，比如在手机和平板电脑上。通过使用最小高度，我能够确保如果需要的话，长标题可以简单地将高度提高到 100%以上。)

## 问题

因为 Cover2 是一个响应式主题，所以我尽量确保它在不同的浏览器和屏幕尺寸下看起来都不错。我注意到，Android 上的 Chrome 并不是这样的。

Android 上的 Firefox 和 Chrome 都是这样做的，当你向下滚动时，浏览器的地址栏会向上滑动，直到你向上滚动，然后地址栏再次出现。(这其实就跟 Cover2 实现自己的 navbar 一样。)不过，浏览器对屏幕高度的处理是不同的。

在 Firefox 中，当地址栏可见时，全高(CSS 中的`100vh`)是地址栏和屏幕底部之间的高度。)地址栏隐藏时，全高是屏幕上下之间的高度。

在 Chrome 中，全高是屏幕顶部和底部之间的高度，无论地址栏是否可见。

## 解

我以前写过我有多不喜欢用户代理嗅探。(有趣的旁注:我最近发现我妻子觉得这个词很好笑。)然而，这是一个我认为我无法回避的情况。

解决方案是降低 Chrome 的全屏高度，这样地址栏就不会被考虑在内了。地址栏的高度是`56px`(据我通过反复试验所知)，但我仍然需要只针对 Android 上的 Chrome。

WordPress 已经做了一些用户代理嗅探，并用它发现的内容给`<body>`标签分配类。例如，在我的电脑上，它指定了类`os-windows`和`browser-gecko`(如果你想知道的话，这是 Firefox。)但问题来了:WordPress 为 Android 上的每一个浏览器分配了类`browser-android`，包括 Chrome 和 Firefox，而不仅仅是 Chrome 成为事实上的标准之前 Android 自带的股票浏览器。这显然行不通，所以我需要找到更具体的东西。进入 CSS `@supports` at-rule。

我更喜欢功能查询，所以我将`@supports`与 Chrome 特有的[浏览器黑客](http://browserhacks.com/#ch)结合使用。这本身将针对 Chrome、Safari、Edge 和 Opera，所以我将它与基于 WordPress 的用户代理操作系统类结合起来:

```
.page-header {
  @supports (-webkit-appearance:none) {
    .os-android & {
      min-height: calc(100vh - 56px);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，Chrome 的导航条就有了合适的全高行为。

* * *

*本文最初发表于[eichefam.net](https://eichefam.net/2018/04/14/full-screen-weirdness/)。*
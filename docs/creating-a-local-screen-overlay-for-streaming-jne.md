# 为流创建本地屏幕覆盖

> 原文：<https://dev.to/mortoray/creating-a-local-screen-overlay-for-streaming-jne>

我的新电脑的屏幕比上一台大。这很棒，除了它不再匹配典型的 1920x1080 流式尺寸。我无法在 Twitch 和 YouTube 上调整这些尺寸而不遭遇缩放问题。我只能广播我的流的一部分，但我看不到那个区域的大小。我需要一个本地覆盖图来显示正在发生的事情。

[https://www.youtube.com/embed/s5Kmng09H9Q](https://www.youtube.com/embed/s5Kmng09H9Q)

我探索了几个选项，然后才找到有用的东西。

首先我尝试了 ImageMagick 的`display`。这个工具包通常拥有基本图像操作所需的一切，前提是您能够理解极其复杂的命令行界面。唉，我找不到任何选项来显示我想要的图像。

考虑到合成是桌面的一部分，我研究了小部件。KDE 有一个媒体小组。尽管操作起来有些笨拙，但它确实能正确显示图像:没有背景，而且融合得很好。不幸的是，它不能放在其他窗口之上，因为它是桌面上的一个小部件。

继续前进，我找到一个叫做`qiv`的程序，一个简单的图像浏览器。我玩了一段时间，因为它看起来可能会工作，但也许我的选择是错误的。它似乎有适当的窗口形状(忽略鼠标输入)，但它不是合成的。合成是指与桌面上的其他窗口进行完全的 alpha 混合。取而代之的是，它在棋盘背景上进行混合，为窗口管理器设置完全不透明或透明像素的阈值。

我一度沮丧地放弃了。我以为我想要的很简单，但看起来是不可能的。我想要一个横截面的功能，所有我知道的可能的地方，但没有程序暴露。

我尝试了几种不同的方法。我用`wmctrl`来移动窗口。它允许保持一个窗口在其他窗口之上，但是没有改变输入处理的选项。

`xprop`是通用的 X 协议属性工具。一个叫做`WM_HINTS`的设置包括输入控制。弄清楚如何修改这个字段有点困难。供参考:

```
xprop -id 0x08a00003 -f WM_HINTS 32i WM_HINTS
xprop -id 0x08a00003 -f WM_HINTS 32i -set WM_HINTS "67, 1, 1, 0, 0, 0, 0, 0, 144703489" 
```

Enter fullscreen mode Exit fullscreen mode

我不知道所有这些值是什么，也找不到关闭输入处理的组合。可能没有。

我偶然发现了另一个程序，叫做`pqiv`。这个实现了全屏合成，但是输入处理仍然是个问题。在找到这个之前，我看到了另一个用 Python 写的程序。它关闭了通过 X Shape 系统的处理，但是缺少合成。带着这个想法，我搜索了一下`pqiv`的各期，找到了我想要的东西:[第 125 期](https://github.com/phillipberndt/pqiv/issues/125)。

这链接到另一个问题，其中有一些代码。我把它拉在一起，添加了一些命令行选项，创建了一个[小分支](https://github.com/phillipberndt/pqiv/pull/131)。

这正是我想要的。

```
pqiv --click-through --keep-above --transparent-background --hide-info-box /projects/Current/mortoray.com/artwork/stream_display_frame.png 
```

Enter fullscreen mode Exit fullscreen mode

它合成了我的流覆盖图并禁用了鼠标操作。

> 还有`--position`选项，出于某种原因，它不支持负值。事实证明，窗口管理器 API 是愚蠢的，因为`wmctrl`也不能为 position 设置负值。这闻起来像是来自过去的一些*聪明的*代码。
> 
> 如果你喜欢我的文章，那么[在推特上关注我](https://twitter.com/edaqa)或者[成为赞助人](https://www.patreon.com/mortoray)。感谢您的阅读。
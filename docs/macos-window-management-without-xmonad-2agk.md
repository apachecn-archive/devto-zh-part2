# 没有 xmonad 的 macOS 窗口管理

> 原文：<https://dev.to/luke/macos-window-management-without-xmonad-2agk>

在家开发时，我已经“适应”了我的 MacBook Pro(大约 2012 年)，在很大程度上还可以。拥有多个“空间”/桌面和可调节的分屏就足够了；其中一个空间在终端/编辑器和网络浏览器之间分开，并且可能在另一个空间上有另一个网络浏览器，具有 Spotify 等。委托给后续空间。

但是去年一个同事推荐了 [xmonad](https://hackage.haskell.org/package/xmonad) 我也没有回头。

它极大地改变了我的开发效率。

## xmonad

对于任何不熟悉 xmonad 的人来说，这是一个用于 X 的 tilling window manager，这是一个你最喜欢的*nix 操作系统中常见的窗口系统。例如，你可以在 Ubuntu 中使用它来代替默认的窗口管理器。

平铺窗口管理器允许您在屏幕上排列窗口，使它们不会重叠。例如:

```
+------------+---------------+
|            |               |
| Terminal   | Web Browser   |
|            |               |
|            |               |
+------------+---------------+ 

+------------+---------------+
| Terminal   | Editor        |
|            |               |
+------------+---------------+
| Terminal   | Web Browser   |
|            |               |
+------------+---------------+

+------------+---------------+
| Terminal   | Web Browser   |
|------------|               |
| Terminal   |               |
|------------|               |
| Terminal   |               |
+------------+---------------+ 
```

Enter fullscreen mode Exit fullscreen mode

为了构建这些布局，xmonad 提供了一系列相当简单的组合键来生成终端、四处移动图块、调整布局大小、更改当前布局算法、更改当前查看的空间等。

这使得在使用键盘进行开发时，工作流程变得非常快速。我发现自己在使用浏览器时只能求助于鼠标。我也改用 vim 而不是 sublime，给了鼠标更多的自由。

# macOS

但是 macOS 呢？xmonad 能用在 macOS 上吗？

理论上是的...但在尝试按照 OS X 豹(2007 年首次发布)的一些古老说明安装它失败后，世界似乎已经放弃了让这种特殊的组合工作。macOS 确实附带了 X，xmonad 与之接口的窗口服务器，但是最近似乎没有这方面的文档。

## 替代品

有替代物，即[紫水晶](https://github.com/ianyh/Amethyst)，我用它取得了一些成功。它试图模仿 xmonad，但没有提供预期的完全相同的体验。

你最好在 Mac 上运行 Ubuntu(除非你关心 macOS 或 Aqua (macOS 默认 GUI)或任何没有 linux 等价物的 macOS 应用程序)。我实际上没有尝试过，因为我是(我确信)许多喜欢运行 macOS 的人之一。

## 另类另类

在这个问题的几个不同解决方案之间翻来翻去，我最终选定了一个次优但有点令人满意的设置。这可能不会给人留下深刻印象，但是这种简单性已经允许了与在 Ubuntu 上使用 xmonad 相似的效率。它主要围绕 macOS 的分屏和空间切换，使用触摸板上的 4 指横向滑动手势。

### 空间 1

#### 用浏览器拆分多标签终端。

在屏幕最左侧的一列中有多个终端选项卡，我可以为每个选项卡分配一项任务:

1.  编辑:每日任务日志/日记
2.  输出:自动构建日志
3.  编辑器:代码
4.  bash:用于添加依赖项、repls、git

能够在选项卡之间创建/删除/移动对于我的大多数工作流来说已经足够了。不幸的是，重新排序标签只能用鼠标来完成。Cmd-tab 当然可以在浏览器和终端之间切换。

最右边的一栏是一个网络浏览器，用于我正在开发的文档或网络应用程序。

### 第二空间

#### 网络浏览器

这是不言自明的。通常包含更多的文档/Twitter/YouTube/等。

### 第三空间

#### 桌面

因为谁不想要一张他们最近拍摄的漂亮光滑的照片，上面杂乱地摆放着一些零散的窗口和图标呢？

# 结论

总之，那是我在做我自己。你做你的！所有操作系统都有大量的窗口管理选项，我强烈建议不要依赖于某个特定的操作系统。

而且越快越好——也许你正在浪费几十秒钟去寻找那个漂浮在你 20 个空间中的某个地方的窗口。或者你知道你刚才正在看的那个浏览器标签...

这些秒钟在你不知情的情况下累积起来！总是质疑你当前的设置/编辑器/操作系统是否适合你！😊

请随意评论 xmonad 或您管理开发设置的经验。

跟我学更多像这样的🚀
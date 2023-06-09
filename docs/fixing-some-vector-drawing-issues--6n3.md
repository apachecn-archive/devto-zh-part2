# 修复一些矢量绘图问题

> 原文：<https://dev.to/mortoray/fixing-some-vector-drawing-issues--6n3>

我修复了 [Fuse](https://fusetools.com/) 中矢量图的几个问题。其中一些似乎有些显而易见，所以对于所有这些问题，我想知道它们为什么会在那里。在这篇事后分析中，我查看了一些修复，并试图理解它们是如何发布的。

## 除了安卓屁股什么都没有

我们收到一份报告，称指定`LineCap="Round"`在 Android 上不起作用。你最终只会得到屁股帽。这类报告总是让我担心，因为它们听起来像是设备兼容性问题。我的大脑立即进入变通模式，当我浏览代码时，担忧占据了我的思想。

我找到相关的代码:

```
var strokedPaint = CreateStrokedPaint(stroke.Width * _pixelsPerPoint,
(int)stroke.LineJoin, (int)stroke.LineCap, stroke.LineJoinMiterLimit); 
```

Enter fullscreen mode Exit fullscreen mode

看起来还行。这是 Uno 代码和原生 Java 代码之间的典型桥梁。我们传入`stroke.LineJoin`和`stroke.LineCap`的值。跳到 Java 代码，我看到了什么:

```
 paint.setStrokeCap(Paint.Cap.BUTT);
    paint.setStrokeJoin(Paint.Join.MITER); 
```

Enter fullscreen mode Exit fullscreen mode

没错。它只是硬编码，尽管有争论。告诉我未使用的参数的 linter 选项可以捕捉到这一点。

> 我不知道我们的工具链是否支持为这个后端添加这个警告。这也是在一个成熟的项目中添加的恼人的事情之一:我们可能会从零到一百万个警告。

但是我们在手工测试中怎么没有发现这一点呢？当我编写最初的核心图形代码时，我已经测试了笔画末端。然后，我将我的特别测试转移到用于编写其他后端的测试套件中。原来我们没有测试线帽。我们现在就做！

## DotNet 被关闭的欲望

我们发布了一个允许绘制部分曲线的特性。给定一个`Path`元素，您可以指定一个起始值和长度值来绘制 SVG 路径的一部分。对于动画来说，这是一个美丽的特性。

大约在发布后的一天，当我准备在我的[直播流](https://www.twitch.tv/mortoray)中演示时，我注意到预览中出现了奇怪的线条。例如，当画一个圆的一部分时，它会在两端画一条连接线！

我的脑袋又一次进入了令人担忧的兼容性模式。找到缺陷的来源没有帮助:

```
case LineSegmentType.Move:
    path.CloseFigure();
    prevPoint = to;
    break; 
```

Enter fullscreen mode Exit fullscreen mode

当 SVG 数据包含一个`Move`操作时，我们为什么要关闭图形？移除那个位元，它就能修复它。但是我们为什么要这么做呢？这是某个后端问题的解决方法吗？是否有一些丢失的测试用例需要它？希望我没有打碎什么东西。

我怎么会错过这个更好的问题？原来问题只出现在 DotNet 后端。我们有三种不同的矢量绘图后端:DotNet、Android 和 CoreGraphics (iOS/Mac)。我在 Mac 上做了大部分测试，在 iOS/Android 上做了一点。我没有考虑彻底测试所有后端，因为我只是重用现有的 API。

有可能我们以前从未有过部分形状。这种缺陷会发生在实体封闭的形状上，但是由于视觉上的结果是一样的，所以你不会注意到。如果路径中只有一行，缺陷不会触发。只有在多线段的情况下才会发生。这不是后端测试的一部分。

## DotNet 中风正在节食

在修复上面的问题时，我注意到了一些奇怪的事情:我的一个演示中的一个箭头和附加的线不一样宽。

这次我认为我在演示中犯了一个错误。经过调查，我意识到我没有。点网后端的笔画宽度没有考虑屏幕密度。这似乎是我们早就注意到的事情？

在大多数 Windows 机器上，像素到点的密度是 1，而 Android、iOS 和 Mac 的密度不为零。Android 和 iOS/OSX 后端处理了密度问题。Windows 上的 DotNet 测试没有显示出问题。

> 预览是 Fuse 工具的一部分，它可以让你在工作时看到应用的实时预览。它可以在 Windows 和 Mac 上运行，但是在这两个平台上都使用 DotNet 后端。然而，Mac 上的原生构建使用与 iOS 共享的 CoreGraphics 后端。

我们有显示笔画宽度的测试案例，但是没有一个有比较点。在 Mac 上的 preview 中查看测试，它看起来很好。只有当你有一个参考点时，你才会注意到宽度是错误的。现在很容易想到一种测试可以发现这一点，但我不确定什么样的方法可以首先阻止它。更专注的代码审查是可能的——但是通常，我们关注的是已经存在的代码，而不是不存在的代码。

## 结论？

很容易说，所有这些都可以通过一点改进的测试来发现。这是关于验尸的一件奇怪的事情:回想起来，一切都显而易见。在我们进行大量测试的时候，我们甚至构建了一个[辅助测试应用](https://mortoray.com/2017/05/22/improve-testing-and-lower-costs-with-assisted-manual-testing/)来提供帮助。

我们在主要设备目标上测试了我们的预期用例:Android 和 iOS。新特性的引入暴露了现有代码中的缺陷，但只是在特定的平台上。一个功能有这么多潜在用途，肯定会有问题。面临的挑战是在不过度分配测试时间和精力的情况下找到它们。

> 我在 [Fuse](http://fusetools.com/) 上的工作充满了有趣的编码。[在 Twitter 上关注我](https://twitter.com/edaqa)或[观看我的视频](https://www.twitch.tv/mortoray)以获得更多用户界面和算法的乐趣。
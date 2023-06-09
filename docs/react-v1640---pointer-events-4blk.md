# 反应 v16.4.0 -指针事件

> 原文：<https://dev.to/superkarolis/react-v1640---pointer-events-4blk>

2018 年 5 月 23 日，React 的一个[新次要版本 v16.4.0](https://github.com/facebook/react/releases/tag/v16.4.0) 已经推出。总的来说，这是一个相当平淡无奇的版本，主要是修复各种各样的错误。值得注意的是，它增加了对[指针事件](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events)的支持，这是 React 几乎整个存在过程中经常要求的特性。

指针事件本质上与鼠标事件(mousedown、mouseup 等)非常相似。)但是与硬件无关，因此可以处理所有输入设备，例如鼠标、触笔或触摸。这很好，因为它消除了为每个设备单独实现的需要，并使跨设备指针的创作更容易。

## 指针事件

指针事件 API 的工作方式与现有的各种事件处理程序相同。指针事件作为属性添加到 React 组件中，并被传递一个接受事件的回调。在回调中，我们处理事件。React DOM 中添加了以下指针事件:

*   onPointerDown
*   onPointerMove
*   onPointerUp
*   onPointerCancel
*   onGotPointerCapture
*   onLostPointerCapture
*   onPointerEnter
*   onpointeleave
*   onPointerOver
*   onPointerOut

在 MDN 指针事件文档中很好地解释了何时触发指针事件，因此请将其作为深入解释每个事件的参考。请注意，在撰写本文时，Safari 不支持指针事件。

关于如何将指针事件整合到你自己的应用程序中的例子，请看下面由 [Philip Spiess](https://github.com/philipp-spiess) 编写的例子。我添加了 [PEP](https://github.com/jquery/PEP) polyfill，让它也能在 Safari 上工作。请注意，`onGotPointerCapture`和`onLostPointerCapture`事件仍然无法使用 polyfill。

在代码沙箱上查看这个[演示，了解新指针事件的可能性。提示:尝试在各种设备上拖动圆圈。](https://codesandbox.io/embed/1wrqxkxl64)
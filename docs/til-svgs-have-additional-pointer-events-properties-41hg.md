# TIL:SVG 有额外的指针事件属性

> 原文：<https://dev.to/stefanjudis/til-svgs-have-additional-pointer-events-properties-41hg>

今天，我遇到了这个例子，关于你可以在 CSS 中用`pointer-events`做的某些事情。

```
.foo {
  pointer-events: visiblePainted;
} 
```

Enter fullscreen mode Exit fullscreen mode

`visiblePainted`是我以前从未见过的，事实证明 SVGs 为`pointer-events`属性提供了其他几个值:

*   `visiblePainted`
*   `visibleFill`
*   `visibleStroke`
*   `visible`
*   `painted`
*   `fill`
*   `stroke`
*   `all`

您可以非常精细地控制 SVG 中的元素应该如何表现，并且它不局限于典型的“web 开发盒”。如果您想了解更多信息，请访问。:)
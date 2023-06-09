# 将表情符号用作光标

> 原文：<https://dev.to/kyleakelly/use-emojis-as-cursors-3di0>

我构建了一个简单的工具，可以生成将表情符号用作光标所需的 CSS。

[https://www.emojicursor.app/](https://www.emojicursor.app/)

## 这不是已经存在了吗？

算是吧。目前的其他解决方案要么使用:

1.  预先生成的图像文件，或
2.  使用 JavaScript 和 canvas 动态生成图像

两者都有利弊。

预生成的图像文件是最一致的，并且具有最大的跨浏览器支持，但是它们需要更多的准备时间来创建图像文件，并且所使用的表情符号风格可能与用户系统和熟悉度不一致。

使用 JavaScript 和 canvas 是使用已安装的系统表情符号的一种方式，但需要在光标可用之前运行 JavaScript。

## 有什么新手法？

这种技术的关键区别是使用 SVG，并将表情符号视为文本。通过在 cursor url 属性中使用 inline SVG，我们可以使用系统表情样式，而不需要外部图像或 JavaScript 依赖。此外，用 less 或 sass 来生成不同的表情符号光标或调整参数也很简单。

这种技术最大的缺点是缺乏浏览器支持。没有 IE 或者 Edge。

## 去背包

该代码获得麻省理工学院许可，可从以下网址获得:

[https://github.com/kylekelly/emoji-cursor](https://github.com/kylekelly/emoji-cursor)
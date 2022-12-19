# 写更好的 CSS 的三个技巧

> 原文：<https://dev.to/hybrid_alex/three-tips-for-writing-better-css-260f>

这并非没有挑战，但在我看来，在使用 HTML 或 JavaScript 时，没有什么是我没有经历过的。

这里有一些我最近一直在思考的关于写好 CSS 的小技巧。

## 1。减少深远的风格

在全局范围内设计原始的 HTML 元素时要小心。小心使用梯级。全局变量是强大的，在引入之前需要充分考虑。

例如，当我看到标题在默认的`font-weight`和`font-size`之外变得过于具体时，我会有点紧张。当你开始引入颜色、文本转换时，我经常看到它们不得不在文档中被覆盖。

试着减少你不得不撤销你之前已经声明的事情的次数。

## 2。空白

如果你使用一个构建系统来缩小你的 <abbr title="Cascading Stylesheets">CSS</abbr> ，空白是一个免费的文档工具。这是我从 Harry Roberts 那里学到的一个小而方便的技巧，他很好地解释了[空格是如何语义化的](https://csswizardry.com/2017/05/writing-tidy-code/)。

## 3。定期检查输出

有些人可能会觉得这很傻，“我知道输出会是什么样子，是我写的！”，但是 Sass 和工具都很好地掩盖了您可能期望的输出。

定期检查输出经常导致我对我的 <abbr title="Cascading Stylesheets">CSS</abbr> 进行一点重构，以便更好地完成项目。

* * *

这篇文章最初出现在我的网站上。
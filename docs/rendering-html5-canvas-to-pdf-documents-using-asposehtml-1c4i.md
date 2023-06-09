# 使用 Aspose.HTML 将 HTML5 画布渲染为 PDF 文档

> 原文：<https://dev.to/asposepdf/rendering-html5-canvas-to-pdf-documents-using-asposehtml-1c4i>

在许多情况下，HTML 文档用于为各种报告创建模板，随后转换为 PDF 文档。Canvas API 是一个用于创建图像的强大工具，它也可以用于在 PDF 文档中放置图形。这个 API 是通过编写 JS 脚本来使用的，这些脚本可以通过一组完整的绘图函数来访问画布区域，从而允许动态生成图形。

Aspose.HTML 支持两种访问画布元素的方法:

*   通过 JavaScript 代码——这是一种传统的方法，当我们使用标准的 HTML 文档时更可取；
*   通过 Aspose.HTML API——这种方法对于创建内存中的 HTML 文档更方便；

由于第一种方法非常流行，我们将只考虑它的用法的一个小例子。
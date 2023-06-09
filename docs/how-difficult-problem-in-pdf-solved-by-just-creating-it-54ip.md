# 如何通过创建 pdf 来解决 pdf 中的难题

> 原文：<https://dev.to/offbase/how-difficult-problem-in-pdf-solved-by-just-creating-it-54ip>

是啊！在编程中，给我打击的是创建包含 URL 图像的 PDF。

我用了很多方法，但都是一些鸡眼和优点。例如，如果你能够加载 PDF 格式的表格副本，那么问题是加载 URL 图像，如亚马逊 S3，谷歌图像等。在这里，我将向您描述我使用了什么技术，应用了什么方法来生成 pdf。

***技术:***

JavaScript(客户端)，NodeJS(服务器端)

***方法或套餐:***

[jsPDF](https://parall.ax/products/jspdf) ， [PDFMake](http://pdfmake.org/#/) ， [PDFkit](http://pdfkit.org/)

***技术及包描述:***

在使用这些技术和方法时，我有几点可以帮助你有效地使用

使用 jsPDF 和 PDFMake，您可以轻松生成包含静态数据和图像的 PDF 文件。

当涉及到基于 URL 的图像时，很难直接加载到 PDF 文件，我们可以通过使用一些 JavaScript 方法将图像加载到 PDF 文件，如 canvas，它首先使用 URL 图像作为其函数参数，然后将其转换为 Base64 字符串，然后可以由 jsPDF 或 PDFMake 的方法使用。

现在，如果我们有图像和表格的数据，但会有另一个问题转换成 base64 的图像数量，这是应用程序挂起。

所以我找到了一种转换 PDF 文件的方法，那就是 PDFkit。这是转换 PDF 文件的做法，它唯一需要的是点击和跟踪，根据我的看法，最好的方法是 PDFkit。这都需要服务器端的支持，比如文件系统、请求包

```
e.g var fs = require(‘fs’), 
var request = require(‘request’); 
```

Enter fullscreen mode Exit fullscreen mode

Request 方法基本上克服了 base64 的问题，它获取图像 URL 并使其缓冲 pdfkit 使用的缓冲数据并将其加载到 PDF 页面，而无需挂起应用程序。pdfkit 甚至支持许多属性，如用字体属性装饰文本，几何图形，如直线，三角形，正方形和许多你只需要应用点击和试验，你会得到一个几乎相同的动态图像示例表，你想为车辆检查创建 pdf。

感谢阅读；)

你可以在推特上找到我，我的名字是 [@shubhu_off_base](https://twitter.com/shubhu_off_base)
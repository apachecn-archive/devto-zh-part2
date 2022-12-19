# Aspose.PDF 云:使用 CSharp SDK 转换文档(第 3 部分)

> 原文：<https://dev.to/asposepdf/asposepdf-cloud-converting-documents-using-csharp-sdk-part-3-1kjn>

完整的样本可以从这里下载。

在[上一部分](https://dev.to/andruhovski/asposepdf-cloud-converting-documents-using-c-sdk-part-2-1ifj)中，我们学习了如何将 PDF 文档转换成其他格式，但只能使用默认设置。现在将尝试自定义一些转换设置，并获得更灵活的结果。设置的数量和目的取决于转换格式。还需要注意的是，有些格式是不需要额外设置的，比如 PDF 转 XPS 或者 PDF 转 PDF/A，因此，我们从简单的转换开始，学习更复杂的转换。

在这一部分中，我们继续使用 ASP.NET 的 MVC5 应用程序，但有所变化:

*   我们应该重写`Index.cshtml`，针对不同的格式调用不同的动作；
*   我们需要为`HomeController`中的每种格式添加新的动作；

`Index.cshtml`的新版本如下图:
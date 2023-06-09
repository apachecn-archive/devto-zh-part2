# Aspose.PDF 云:使用 CSharp SDK 转换文档(第 2 部分)

> 原文：<https://dev.to/asposepdf/asposepdf-cloud-converting-documents-using-c-sdk-part-2-1ifj>

在[的前一部分](https://dev.to/andruhovski/asposepdf-for-cloud-converting-documents-using-c-sdk-4a9d)中，我们考虑了从其他格式到 PDF 的转换过程。现在，我们继续探索 Aspose.PDF 云的 API，我们将了解如何将 PDF 文档转换为其他格式:DOC/DOCX、EPUB、HTML、LaTeX、TIFF、PPTX、SVG、XLS。显然，转换过程对于每种格式都是特定的，因此，我们首先考虑通用算法，然后针对某些格式调整附加参数。

与前一篇文章不同，我们将使用 ASP.NET mv C5 应用程序模板。这将允许我们看到同步和异步方法的运行。

因此，假设我们需要编写一个 ASP.NET mv C5 应用程序，向用户显示云存储中某个文件夹的内容，用户可以将该文件夹中的 PDF 文件转换为适当的格式。

## 第 0 步。基本准备

*   添加名为`MvcConverter`的新 ASP.NET Web 应用程序，并选择`No Authentication`和`MVC`模板选项
*   为 Aspose 添加包。云:
    *   `Install-Package Aspose.Storage-Cloud`
    *   `Install-Package Aspose.PDF-Cloud`
*   更新其他包
    *   `Update-Package`
*   更新`_Layout.cshtml`中的布局
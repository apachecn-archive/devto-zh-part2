# Aspose.PDF 云:使用 C# SDK 转换文档

> 原文：<https://dev.to/asposepdf/asposepdf-for-cloud-converting-documents-using-c-sdk-4a9d>

在[的上一篇文章](https://dev.to/andruhovski/three-simple-steps-to-start-using-asposecloud-storage-3mmc)中，我们教了上传文件到 Aspose.Cloud。现在我们将考虑用 Aspose 转换文档的基本步骤。
对于第一个例子，我们将使用转换 HTML 为 PDF。为简单起见，假设源文件将位于默认存储的`"Html-Demo"`文件夹中，我们将结果文件存储在`"Pdf-Demo"`文件夹中。

和前一篇文章一样，我们创建了一个控制台应用程序，但现在我们将添加两个包:

*   管理存储(`Install-Package Aspose.Storage-Cloud`)
*   操作文档(`Install-Package Aspose.PDF-Cloud`)

UPD:这个职位的有效期是。PDF-Cloud v.18.8

下一步是在我们的类中声明必要的字段和常量。
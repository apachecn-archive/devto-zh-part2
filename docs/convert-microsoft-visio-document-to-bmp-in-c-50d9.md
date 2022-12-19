# 在 C#中将 Microsoft Visio 文档转换为 BMP

> 原文：<https://dev.to/asposediagram/convert-microsoft-visio-document-to-bmp-in-c-50d9>

开发人员可以将任何 Microsoft Visio 文档转换为 C# ( *或任何其他语言的 BMP ( *位图图像文件*)。NET 框架支持的语言，如 VB.NET 等。*)用[比喻。Diagram](https://products.aspose.com/diagram) 是一个轻量级、可扩展、易于使用的 API，帮助开发人员在客户端和服务器端创建、操作 Microsoft Visio 文件并将其转换为各种格式，而无需安装 Microsoft Visio 或使用任何类型的 Microsoft Office automation。

**支持的平台**

> 阿斯波斯。图表支持两者。NET 和 Java 平台。请参见 [Aspose。的图表。NET](https://products.aspose.com/diagram/net) 和 [Aspose。图为 Java](https://products.aspose.com/diagram/java) 供大家参考。

# 示例微软 Visio 文档- VSD

为了进行演示，我们将使用 Aspose 将下面的[示例 Microsoft Visio 文档](https://github.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/blob/master/Convert-Microsoft-Visio-Document-to-BMP/SampleConvertMicrosoftVisioDocumentToBMP.vsd)转换为 C#中的 BMP。但是，您可以在示例代码中使用您选择的任何 Microsoft Visio 文档，它也将被转换为 BMP fine。

[![Sample Microsoft Visio Document to be converted to BMP using Aspose.Diagram API.](../Images/3e47d9a60a7922c0e4716e1a62b749e9.png "Sample Microsoft Visio Document to be converted to BMP using Aspose.Diagram API.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rxXI8Be5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Convert-Microsoft-Visio-Document-to-BMP/Microsoft-Visio-Document-to-be-convert-BMP-Aspose.Diagram.png)

# 发牌

请注意，如果您将在没有预先设置 Aspose 的情况下执行代码。图许可，你会在你的输出文档中得到*评估水印*。更多信息，请通过[许可](https://docs.aspose.com/display/diagramnet/Licensing)。

# 样本代码

以下示例代码通过执行以下步骤将 Microsoft Visio 文档转换为 BMP。

*   将 Microsoft Visio 文档加载到*页面。图表.图表*对象。
*   指定 *ImageSaveOptions* 设置清晰图像的分辨率。
*   将*保存为建议。Diagram.Diagram* 对象转换成 BMP 格式，带有指定的 *ImageSaveOptions* 。
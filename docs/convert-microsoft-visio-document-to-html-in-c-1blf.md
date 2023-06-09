# 用 C#将 Microsoft Visio 文档转换为 HTML

> 原文：<https://dev.to/asposediagram/convert-microsoft-visio-document-to-html-in-c-1blf>

开发人员可以用 C# *(或任何其他语言)将任何 Microsoft Visio 文档转换成 HTML ( *超文本标记语言*)。使用[Aspose . NET 框架支持的语言](https://products.aspose.com/diagram)*如 VB.NET 等。Diagram 是一个轻量级、可扩展、易于使用的 API，它帮助开发人员在客户端和服务器端创建、操作 Microsoft Visio 文件并将其转换为各种格式，而无需安装 Microsoft Visio 或使用任何类型的 Microsoft Office automation。

**支持的平台**

> 阿斯波斯。图表支持两者。NET 和 Java 平台。请参见 [Aspose。的图表。NET](https://products.aspose.com/diagram/net) 和 [Aspose。图为 Java](https://products.aspose.com/diagram/java) 供大家参考。

# 示例微软 Visio 文档- VSD

出于演示目的，我们将使用 Aspose 将下面的[示例 Microsoft Visio 文档](https://github.com/shakeel-faiz/Screenshots-and-Sample-Files/blob/master/Convert%20Microsoft%20Visio%20Document%20to%20HTML/sampleConvertMicrosoftVisioDocumentToHTML.vsd)转换为 C#中的 HTML。图表 API。但是，您可以在示例代码中使用您选择的任何 Microsoft Visio 文档，它也将被转换为 HTML fine。

[![Sample Microsoft Visio Document to be converted to HTML using Aspose.Diagram API.](img/50fa9097b767dfefb66e23a4f33276ea.png "Sample Microsoft Visio Document to be converted to HTML using Aspose.Diagram API.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sO0bIoxp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/shakeel-faiz/Screenshots-and-Sample-Files/master/Convert%2520Microsoft%2520Visio%2520Document%2520to%2520HTML/Microsoft-Visio-Document-converted-HTML-Aspose.Diagram.png)

# 发牌

请注意，如果您将在没有预先设置 Aspose 的情况下执行代码。图许可，你会在你的输出文档中得到*评估水印*。更多信息，请通过[许可](https://docs.aspose.com/display/diagramnet/Licensing)。

# 样本代码

以下示例代码通过执行以下步骤将 Microsoft Visio 文档转换为 HTML。

*   将 Microsoft Visio 文档加载到*页面。Diagram.Diagram* 对象。
*   将*保存为建议。Diagram.Diagram* 对象转换成 HTML 格式。
# 将文档上传到 Aspose。单词云存储(C# /。网)

> 原文：<https://dev.to/asposewords/uploading-a-document-to-asposewords-cloud-storage-c--net-3e3f>

才能使用 [Aspose。Words Cloud API](https://products.aspose.cloud/words) (或其他[Aspose Cloud API](https://products.aspose.cloud/))为了读取和更新现有文档，需要将文件加载到云中的存储中。

蜘蛛。存储云 API 可以通过 [REST API](https://apireference.aspose.cloud/storage/) 直接访问，但是这个示例将使用[云 SDK。NET](https://github.com/aspose-storage-cloud/aspose-storage-cloud-dotnet) 让代码更简单。

首先，您需要下载 SDK(DLL 或源代码),并通过创建帐户和请求一组 API 密钥来注册一组凭证。参见[https://dashboard.aspose.cloud/](https://dashboard.aspose.cloud/)

下面的示例代码显示了一个简单的函数，通过提供 API 键、本地文件名和您希望在存储上存储文件的位置，将文件上传到存储。

基本步骤是:

1.  使用您的应用密钥打开 StorageApi
2.  创建一个 PutCreateRequest 并传入文件的细节
3.  调用 PutCreate 方法
4.  检查结果
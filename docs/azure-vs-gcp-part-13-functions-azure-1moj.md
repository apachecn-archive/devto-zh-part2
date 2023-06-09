# Azure vs GCP 第 13 部分:函数(Azure)

> 原文：<https://dev.to/kenakamu/azure-vs-gcp-part-13-functions-azure-1moj>

在这篇文章中，我研究了 Azure 和 GCP 的函数。为什么功能很重要？这个问题有很多答案，我不会在这篇文章中解释，因为我只是想比较 Azure 和 GCP 作为 C#开发者的情况。

# 天蓝色

Azure 提供函数 App 支持函数技术。

*   支持多种语言:C#、F#、Javascript、Java 等。详情见[此处](https://docs.microsoft.com/en-us/azure/azure-functions/supported-languages)。
*   支持多个触发器:HTTP 请求，定时器，几个 web 服务 webhook，几个存储触发器。详情见[此处](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview#what-can-i-do-with-functions)。
*   缩放:它支持多种缩放选项。详情见[此处](https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale)。
*   灵活定价:详见[此处](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview#pricing)。
*   与平台集成:安全、监控、其他服务等。详情见[此处](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview#integrations)。
*   IDE 支持:Visual Studio 和 Visual Studio 代码扩展、本地调试的本地运行时等。
*   内部支持:有趣的是，微软为 Function App 提供内部运行时，并在云和内部提供相同的体验。模式详情见[此处](https://docs.microsoft.com/en-us/azure/azure-functions/functions-runtime-overview)。

### 1.x 和 2.x

目前，Function App 有两个主要版本。

*   1.x:生产就绪。它是全速运转的。网
*   2.x:还是预览。它还在继续。NET core，意味着它是跨平台的。

在[查看更多细节，比较 1.x 和 2.x](https://docs.microsoft.com/en-us/azure/azure-functions/functions-versions)

### 装订

其中一个重要的概念是绑定。我可以将许多东西“绑定”到我的函数中，并以同样的方式对待它们。有几种绑定类型。

*   触发器:我可以绑定触发器类型来“触发”我的函数。例如，当在 blob 存储中创建对象时，或者当在事件网格中接收到事件时，可以触发该函数。
*   输入:我可以集成服务作为输入来获取数据，如 Microsoft Graph、One Drive、Blob、Auth token 等。
*   输出:类似于输入，但我可以将结果输出到输出绑定。

在这里查看[支持的绑定](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings#supported-bindings)

# 编写代码

功能那么多，就一个一个来吧。

### 基本功能

1.转到 [Azure 门户](https://portal.azure.com)并创建新资源。选择功能应用程序并创建。
T3![function](img/026d045fbd36224e664b781c2229573c.png)T5】

2.输入唯一的应用程序名称，然后选择操作系统。这次我选择了“视窗”。点击“创建”。
[![funciton](img/f20aa21534cf410c5d5752840e2fd01d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--MN3-6Gor--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9sgsk9xitallzirzh419.PNG)

3.一旦创建好了，就转到资源。选择“功能”并点击“+”，然后点击“新功能”。
[![function](img/49593d739039bfdc97c44468971d1aab.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--WsZoENnf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w50mchognsbwc6v5yjw0.PNG)

4.选择您想尝试的触发器和语言。我这里用的是 HTTP 触发器和 C#。
[![function](img/cd6efc66df248a667943097f673e40d4.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--wvKX1YKR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bo1lhbd5q068cby6q4d9.PNG)

5.使用默认值单击“创建”。也许改名是个好主意。
[![function](img/21b0a7e0d2cbad11f3056263afbe74d5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--WQ7gBDE0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8zsfu1uvh69sgp3kc9l5.PNG)

6.此时，该函数通过 http 被触发，并返回 http 响应作为输出。点击“获取功能 URL”并复制地址。
[![function](img/6464642e81745bc325025417f1834ab0.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wt-3oDDR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nzs0m7vld3no17qpr33l.PNG)

7.通过 postman 或 curl 等工具点击 URL。添加名称参数。
[![function](img/b6231bb337fe3e749335ac09fe372f0f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--XsvDKL9m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bum05yfbwknjb8zme7yj.PNG)

### 输入绑定

现在，让我们尝试整合。因为我已经有了存储帐户，它是在我创建函数 App 时自动创建的，所以我将它用作我的输入和输出。

1.选择集成。我可以把 HTTP 看作触发器和输出，没有输入。
[![input](img/eb10ff5e7daa737cffb2666c3e866613.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--5sG8hUpF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7anqfovwnnrzn14ooc6e.PNG)

2.点击“新输入”并选择“Azure Blob 存储”。然后点击“选择”。
[![input](img/af056010c3d16f1b9c2675eec30b839b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--WixQBdXv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/491zh3meob5xdozb746w.PNG)

3.单击存储帐户连接旁边的“新建”。选择要连接的存储帐户。向导关闭时，单击“保存”。输入名为“inputBlob ”,路径为“incontainer”。我对这个函数使用容器级，所以我没有指定对象级路径。
[![input](img/dec1307835c96fd74ba74bca86b1c4b2.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--4-mGQwbq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sqjdgp1bmc9b3jrmevfb.PNG)

4.返回函数，用下面的代码更新代码。

```
using System.Net;
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;

public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req, 
    CloudBlobContainer inputBlob,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    log.Info($"Container name: {inputBlob.Name}");
        // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;
    await inputBlob.CreateIfNotExistsAsync();
    CloudBlockBlob blob = inputBlob.GetBlockBlobReference(name);
    int i = 0;
    try
    {
        i = int.Parse(await blob.DownloadTextAsync());
    }
    catch(Exception ex)
    {
        // Do nothing
    }
    i++;

    await blob.UploadTextAsync((i).ToString());
    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, $"Hello {name}. It's {i} times.");
} 
```

Enter fullscreen mode Exit fullscreen mode

5.点击右边的“Test ”,添加带有某个名称的参数“name”。
[![input](img/227098270b0c70c7e598323007ac862c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--oYHsM5Oj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8plr85gpbfz78z2qt51i.PNG)

6.点击“保存并运行”

### 开发者体验

如我所说，有几种工具。

#### Visual Studio 扩展

[Azure 功能和 Web 作业工具](https://marketplace.visualstudio.com/items?itemName=VisualStudioWebandAzureTools.AzureFunctionsandWebJobsTools)

它提供了几样东西:

*   项目模板
*   运行时间
*   本地调试环境
*   部署到 Azure

#### Visual Studio 代码扩展

[Azure 功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

它提供与上述相同的功能。

# 持久功能

我需要另一篇文章来解释这一点，但它基本上编排了多个函数，当你有许多函数需要执行某些命令时。

[文档](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-overview)包含有用的场景。

在下一篇文章中，我将研究 GCP 的函数选项。

# 引用

[天蓝色功能](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview)
[耐久功能](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-overview)
# 蓝色 vs GCP 第 14 部分:功能(GCP)

> 原文：<https://dev.to/kenakamu/azure-vs-gcp-part-14-functions-gcp-38en>

在[上一篇文章](https://dev.to/kenakamu/azure-vs-gcp-part-13-functions-azure-1moj)中，我手握 Azure Functions App。在这篇文章中，我研究了 GCP 函数。

然而，问题是，它不支持 C#。Node.js 上的 Javascript 是目前唯一支持的语言和执行环境。

# GCP

GCP 还提供“云功能”服务来支持功能技术。

*   一种语言支持:目前，云函数支持 Node.js 6.11.5 上的 Javascript
*   支持多个触发器:HTTP 请求，几个 web 服务 webhook，几个存储触发器。详情见[此处](https://cloud.google.com/functions/docs/concepts/events-triggers)。
*   缩放:它支持多种缩放选项。详情见[此处](https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale)。
*   灵活定价:详见[此处](https://cloud.google.com/functions/pricing-summary/)。
*   与平台集成:安全、监控、其他服务等。

# 编写代码

如上所述，Javascript 是目前唯一的选择。

### 基本功能

1.进入[谷歌云控制台](https://console.cloud.google.com)，点击云壳图标。

[![function](img/a0c030ed83585e7f514710fc3d227923.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FHbOmGRe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1xz2t4o57gdue6qrnjut.PNG)

2.创建一个目录并将目录更改为该目录。

```
mkdir cloudcomparefunction
cd cloudcomparefunction 
```

Enter fullscreen mode Exit fullscreen mode

3.创建 index.js 并添加以下代码。

```
exports.cloudcomparefunction = (req, res) => {
  res.send('Hello World!');
}; 
```

Enter fullscreen mode Exit fullscreen mode

4.运行以下命令进行部署。如果这是第一次使用云函数，它会要求您启用云函数 API。键入 yes 以启用它。

```
gcloud beta functions deploy cloudcomparefunction --trigger-http 
```

Enter fullscreen mode Exit fullscreen mode

5.一旦部署完毕，检查 url 并使用诸如 postman 或 curl 之类的工具调用它。我在这里用过邮递员。

[![function](img/f6dc20d8e9e5e0af0779b565c3cf5f95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HzRUF8S_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gwpk1jxls4zoa4fqgki9.PNG)

### 本地调试经验

虽然我没有解释 Azure 的本地调试体验，因为它实际上和其他的一样。NET 应用，我尝试对云存储进行调试。我可以使用本地仿真器函数和 Visual Studio 代码来调试它。

1.首先，安装本地模拟器。它说支持 npm 版本 6.11.5，所以我安装了这个版本用于测试。

```
npm install -g @google-cloud/functions-emulator 
```

Enter fullscreen mode Exit fullscreen mode

2.虽然我可以下载我已经创建的代码，但我只是在本地 pc 上从 scrach 下载。创建文件夹，添加文件并初始化 npm 项目。

```
cd <to anywhere you want>
mkdir function_demo
cd function_demo
npm init --yes 
```

Enter fullscreen mode Exit fullscreen mode

3.通过 Visual Studio 代码打开文件夹，并在文件夹中添加 index.js，然后添加以下代码。

```
exports.cloudcomparefunction = (req, res) => {

     res.send('Hello World!');
}; 
```

Enter fullscreen mode Exit fullscreen mode

4.运行函数模拟器，并在本地部署。

```
functions start
functions deploy cloudcomparefunction --trigger-http --local-path=<path to the project folder> 
```

Enter fullscreen mode Exit fullscreen mode

5.部署完成后，我可以看到部署的 URL。

[![debug](img/97b9b5c5cbbddb336fd21f3066f47565.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9SqG2pMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5v5ln4sqn05ocmiajhw1.PNG)

6.访问网址，以确认它的工作。

7.返回 Visual Studio 代码，并选择调试菜单。然后点击“齿轮图标”，选择“Node.js”

[![debug](img/264002a718b61a6ad998a5fa3f8a7945.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C7fFrl34--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9064ti7fupe08cg8tk2d.PNG)

8.默认情况下，它会添加“启动”配置。通过开始键入来添加“附加”类型。

[![debug](img/69c990bb2959c7bd1526e03aeb9e750f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qBgMyZx---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kc6571pphoj6bg5qn5ke.PNG)

9.运行以下命令启动调试器。

```
functions debug cloudcomparefunction 
```

Enter fullscreen mode Exit fullscreen mode

10.它应该向您显示调试器端口。对我来说是 5858，所以将端口更改为 5858。

[![debug](img/618d4524f4e8d0780d12806fa5d9897c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YJ-zZlP4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a85dhmv8t1xfbssamzwx.PNG)

11.然后，在 Visual Studio 代码中，npm -从下拉列表中选择“Attach”并点击 F5。然后将断点设置为 index.js 并进行测试。

[![debug](img/74cec1e1b5ff0307798111c770a36625.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b-t5tZgW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rxy6c9oa8smx4f6l197f.PNG)

### 其他调试选项

GCP 提供了一个很棒的工具，叫做“堆栈驱动程序”,它可以在生产中实现断点。它只是向我显示了上下文的快照，并没有停止应用程序，这非常酷。

# 总结

根据我的经验，我认为 GCP 需要更多的时间来提供完整的功能来与 Azure 竞争。Azure 目前在功能技术方面有巨大的领先优势，因为:

*   Azure 支持多种语言，而 GCP 只支持 Node.js。
*   Azure 支持更多种类的触发器。
*   Azure 提供“绑定”来轻松处理输入/输出
*   Azure 为 GCP 提供了更多的 IDE 集成。
*   调试体验对 Azure 来说更容易。
*   耐用功能:非常独特的功能

另一方面，GCP 还提供了非常吸引人的功能，如:

*   堆栈驱动程序，调试器可以处理任何服务。
*   简单的 UI(我自己的观点)
*   Node.js 的云软件开发包

顺便说一下，这两种云都提供了相似但有趣的特性。

*   门户内的云壳
*   在线编辑器，你可以像 IDE 一样在浏览器中编写代码。
*   与其他服务的集成
*   监控、自动秤等

在下一篇文章中，我将研究微服务。

# 引用

[用 Visual Studio 代码调试](https://github.com/GoogleCloudPlatform/cloud-functions-emulator/wiki/Debugging-with-Visual-Studio-Code)
[云函数仿真器](https://github.com/GoogleCloudPlatform/cloud-functions-emulator)
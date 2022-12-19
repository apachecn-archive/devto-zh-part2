# 使用节点库从 ASP.NET 核心应用程序生成 pdf

> 原文：<https://dev.to/galdin/generating-pdfs-from-an-aspnet-core-app-using-a-node-library-3bp8>

马克·巴赫曼(Marc Bachmann)使用节点库将 ASP.NET 核心应用程序的 HTML 转换为 PDF 的快速博客文章，该文章名为 [html-pdf](https://github.com/marcbachmann/node-html-pdf) 。如果你想看最后的东西，我还设置了一个基于 docker 的[示例 github 库](https://github.com/gldraphael/PdfGenerationNodeServicesSample)。

## 创建新项目

让我们使用命令行工具快速创建一个新的 ASP.NET 核心项目:

```
# create a new project
dotnet new webapi --name PdfSample
# run the project
cd PdfSample
dotnet run
# browse to localhost:5000
# you should see a 404 error 
```

## 编写节点脚本

安装[html-pdf](https://github.com/marcbachmann/node-html-pdf):T2

```
npm install html-pdf --save 
```

并将 ASP.NET 应用程序要调用的节点脚本添加到`Node`文件夹:

```
// File: Node/createPdf.js
const pdf = require('html-pdf');
module.exports = function (result, html, options) {
    pdf.create(html, options).toStream(function(err, stream) {
        stream.pipe(result.stream);
    });
}; 
```

该脚本从`html-pdf`包中调用`create()`,并将其输出通过管道传输到节点服务可访问的双工流`result`。参数`html`和`options`将在调用脚本时从 ASP.NET 应用程序中传递。

## 创建一个调用节点脚本的动作

让我们为`/`路由创建一个控制器动作，它调用我们的节点脚本并生成一个示例 PDF:

```
// File: Controllers/HomeController.cs
public class HomeController : Controller
{
    [HttpGet("/")] // action to invoke for the "/" route
    public async Task<IActionResult> Index(
        [FromServices]INodeServices nodeServices)
    {
        var html = "<h1>Hey!</h1>"; // html to be converted
        var options = new { }; // html-pdf options

        var stream = await nodeServices.InvokeAsync<Stream>(
            "./Node/createPdf.js", // script to invoke
            html,
            options
        );
        return File(
            fileStream: stream, 
            contentType: "application/pdf"
        );
    }
} 
```

*   我们使用`[Route("")]` & `[HttpGet("")]`为`/`路线创建一个动作。
*   它使用`[FromServices]`注释从 DI 容器中获得一个`INodeServices`实例。
*   我们使用相对于项目根的模块名和要传递给脚本的参数来调用脚本。

## 向 DI 注册节点服务

在运行它之前，我们需要向 DI 注册。

我们在`Startup`类的`ConfigureServices()`方法:
中使用了一个扩展方法

```
services.AddNodeServices(); 
```

## 运行应用程序

使用`dotnet run`运行应用程序，PDF 应在`localhost:5000`提供。

[![Output Screenshot](../Images/246d119579d857b8360c587a76303116.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P80T0zAD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://gldraphael.com/blog/content/images/2018/04/output-screenshot.png)

## 发布设置

`createPdf.js`需要成为发布输出的一部分。您可以通过编辑`.csproj`文件并在`<Project></Project>`标签中添加一个如下所示的部分来实现这一点:

```
<ItemGroup>
  <Content Include="Node\createPdf.js">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </Content>
</ItemGroup> 
```

现在可以使用
发布应用程序

```
dotnet publish -c Release 
```

默认情况下，输出将位于`./bin/Release/publish`目录中。

注意，`node_modules`文件夹没有发布。您可以使用 MSBUILD 通过编辑上面的`.csproj`文件来复制构建/发布时的文件夹，或者运行`npm install html-pdf`作为部署脚本的一部分。

我更喜欢部署脚本，因为我不想发布来自`node_modules`的前端包。

## 设置 docker

我花了 8 个多小时试图让 Docker 上的设置工作，这就是为什么我首先决定写这篇文章。

在编写 docker 文件时，我遇到了两个问题，都与 PhantomJS 有关。第一个错误是在构建时试图使用 npm 安装`html-pdf`时出现的。`html-pdf`下载使用 bzip2 压缩的 PhantomJS 的预编译二进制文件。下面是错误消息:

```
tar (child): bzip2: Cannot exec: No such file or directory
tar (child): Error is not recoverable: exiting now
tar: Child returned status 2
tar: Error is not recoverable: exiting now 
```

第二个错误是运行时错误，我无法得到正确的错误消息——应用程序会突然崩溃。

诀窍是安装`bzip2`以使`html-pdf`安装成功，并安装`libfontconfig`以使幻象按预期工作。你可以在基于 debian 的系统上使用
来实现

```
apt install bzip2
apt install libfontconfig 
```

这是完整的文档。将它添加到您的项目的根目录，并使用
运行它

```
docker build -t aspnetpdf .
docker run -d -p 8080:80 aspnetpdf 
```

[![Sample repo screenshot](../Images/65b71d9503f1f6b501d9a35b9d487a37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xfiA3AYK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://gldraphael.com/blog/content/images/2018/04/pdf-screenshot.png)

## 结论

就是这样。我们已经看到了如何在 ASP.NET 核心应用程序中使用 Marc Bachmann 的`html-pdf`和 NodeServices 将 HTML 转换成 PDF。如果你问我，我会觉得很酷！

如果您已经走到这一步，您应该完全检查并运行 [GitHub 示例](https://github.com/gldraphael/PdfGenerationNodeServicesSample)。没有借口，如果你已经有你的机器上的 docker😁

* * *

如果您正在考虑在实际项目中采用这种方法，这里有一些可以节省您时间的建议:

*   PhantomJS 目前在 Windows 上有自定义字体的问题。该字体需要安装在 Windows 实例上才能工作。
*   PhantomJS 是基于 WebKit 的，它在 Windows 下使用 GDI+。因此，我们不能在传统的 Azure Web 应用中使用它。[更多信息请点击这里](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)。我们最终切换到 Azure Web 应用程序的容器。
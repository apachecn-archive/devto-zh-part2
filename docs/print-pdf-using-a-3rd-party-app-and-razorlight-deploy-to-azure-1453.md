# 使用 API 和 RazorLight 在 Azure 上打印 PDF

> 原文：<https://dev.to/catriname/print-pdf-using-a-3rd-party-app-and-razorlight-deploy-to-azure-1453>

# 问题

我最近在使用微软报告服务和 RDLC 文件等将报告打印成 PDF 格式时遇到了问题。类似于[这个](https://docs.microsoft.com/en-us/sql/reporting-services/report-builder/exporting-to-a-pdf-file-report-builder-and-ssrs?view=sql-server-2017)的东西。不幸的是，它在开发中工作得很好，但一旦部署到 Azure 中就无法工作了。无论我做什么，我都无法回避我不断得到的 GDI 错误，显然这通过各种 PDF 导出扩展继续，所有这些都依赖于 GDI 进行导出。结果，[我并不是独自面对这个问题，所以，我决定找到一个解决方案。](https://www.reddit.com/r/dotnet/comments/8pbflb/html_to_pdf_or_use_pdf_library_for_net_47/)

# 解

我的总体想法是使用一些东西来呈现我的 PDF 视图，将该视图作为一个长 html 字符串发送到@alvercarto 的这个[免费 PDF 微服务，并获得 PDF 作为回报。](https://github.com/alvarcarto/url-to-pdf-api?utm_campaign=explore-email&utm_medium=email&utm_source=newsletter&utm_term=weekly)

## 将视图渲染为 HTML

我向 PrintAsync 函数传递了一个 ID，将它的内容检索到我的视图模型中，但是需要将视图模型传递到一个视图中，呈现它，获取它的 HTML 并将其发送给 API。

### 剃刀灯

为了在 Razor 中渲染模型，我使用了 [@toddams RazorLight](https://github.com/toddams/RazorLight) 。(如果您使用的是 Core 2.0，请确保使用 2.0-beta1 版本)

这就像声明一个宿主环境变量(用来获取视图的路径)一样简单。为了方便访问，我在 wwwroot 下创建了一个“pdf”文件夹:

```
private readonly IHostingEnvironment _hostingEnvironment; 
```

Enter fullscreen mode Exit fullscreen mode

在构造函数中实例化:

```
_hostingEnvironment = hostingEnvironment; 
```

Enter fullscreen mode Exit fullscreen mode

并使用它来引入您的 web 根路径。

```
var engine = new RazorLightEngineBuilder()
              .UseFilesystemProject(_hostingEnvironment.WebRootPath + "\\pdf\\")
              .UseMemoryCachingProvider()
              .Build();

var view = await engine.CompileRenderAsync("PDF.cshtml", invoiceVM); 
```

Enter fullscreen mode Exit fullscreen mode

### 查看笔记

特别注意你是如何建立你的观点的。不需要把模型放在上面，为了让我的布局文件的路径更容易，我只是把它放在和我的 PDF 视图相同的文件夹中。还要注意它是如何引用@Model 的。下面是我的 PDF 视图中的一个片段:

```
@{
    Layout = "CleanLayout.cshtml";
}
<div class="container">
    <div class="columns">
        <div class="column"><b>Invoice Date: </b>@Model.InvoiceDate</div>
        <div class="column"><b>Account #: </b>@Model.Account</div>
        <div class="column"><b>Invoice #: </b>@Model.InvoiceNumber</div>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 传递给 API，获取字节到 PDF

现在我已经有了我需要的 HTML 字符串，我可以轻松地创建一个 JSON 请求并提交给 api:

```
var httpWebRequest = (HttpWebRequest)WebRequest.Create("https://url-to-pdf-api.herokuapp.com/api/render");

//2cm margins with emulateScreenMedia for css
httpWebRequest.ContentType = "application/json";
httpWebRequest.Method = "POST";

            var json = new {
                html = view,
                emulateScreenMedia = false,
                pdf = new {
                    margin = new {
                        top ="2cm", 
                        left = "2cm", 
                        right = "2cm", 
                        bottom = "2cm"
                    }
                }
            };

var asJson = JsonConvert.SerializeObject(json);

using (var streamWriter = new StreamWriter(httpWebRequest.GetRequestStream()))
{
    streamWriter.Write(asJson);
    streamWriter.Flush();
    streamWriter.Close();
} 
```

Enter fullscreen mode Exit fullscreen mode

这是成功的，唯一的问题是 httpresponse 以字节返回，需要保存为 PDF 文件。解决方案如下:

```
var httpResponse = (HttpWebResponse)httpWebRequest.GetResponse();

Stream objStream = httpResponse.GetResponseStream();
var response = File(objStream, "application/pdf"); 

return response; 
```

Enter fullscreen mode Exit fullscreen mode

所以，我简单地用一个打印按钮来调用这个方法:

```
@Html.ActionLink("Print Claim", "PrintAsync", new { id = Model.Id }, new { @class = "button is-info" }) 
```

Enter fullscreen mode Exit fullscreen mode

部署到 Azure 上，我得到了一个微软。Win32 错误，所以我必须将它添加到我的。csproj 文件:

```
<MvcRazorExcludeRefAssembliesFromPublish>false</MvcRazorExcludeRefAssembliesFromPublish> 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。当然，我还需要在 PDF 的样式和边距上做些工作，但这已经足够开始了。
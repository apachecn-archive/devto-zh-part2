# 使用 API 和 RazorLight 在 Azure 上打印 PDF(更新 1)

> 原文：<https://dev.to/catriname/print-pdfs-on-azure-using-an-api-and-razorlight-update-1-20kb>

在我的第一篇教程中提到的 API[被删除了，所以我不得不做一些更新。首先，选择另一个第三方 API。我目前正在试用](https://dev.to/catriname/print-pdf-using-a-3rd-party-app-and-razorlight-deploy-to-azure-1453) [HTML 2 PDF Rocket](https://www.html2pdfrocket.com/) ，并稍微修改了一下我的代码:

```
 [HttpGet]
    public async Task<FileStreamResult> PrintAsync(int id)
    {
        InvoiceVM invoiceVM = new InvoiceVM();
        invoiceVM = invoiceRepository.Get(id);
        var engine = new RazorLightEngineBuilder()
          .UseFilesystemProject(_hostingEnvironment.WebRootPath + "\\pdf\\")
          .UseMemoryCachingProvider()
          .Build();
        var view = await engine.CompileRenderAsync("PDF.cshtml", invoiceVM);
        string apiKey = "xxxxxxxxxxxxxxxxxxxx";
        using (var client = new WebClient())
        {
            // Build the conversion options
            NameValueCollection options = new NameValueCollection();
            options.Add("apikey", apiKey);
            options.Add("value", view);
            options.Add("MarginLeft", "10");
            options.Add("MarginRight", "10");
            options.Add("MarginTop", "10");
            options.Add("MarginBottom", "10");
            options.Add("PageSize", "Letter");
            MemoryStream ms = new MemoryStream(client.UploadValues("http://api.html2pdfrocket.com/pdf", options));
            return new FileStreamResult(ms, "application/pdf");
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这直接基于 HTML 2 Rocket 文档。其余的代码参见第一篇教程。

…
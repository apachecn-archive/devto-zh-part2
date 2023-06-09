# 带 PayTrace 的 ASP.NET 核心(Mime 类型修复)

> 原文：<https://dev.to/catriname/aspnet-core-with-paytrace-mime-type-fix-563p>

今年我正在重写我以前的支付解决方案(从 PHP 到。NET)和项目清单上的第一个是信用卡。我们使用 PayTrace 和他们的客户端加密技术，因此不必担心 PCI 合规性。

## 未找到  文件

我已经到了这样的地步:

*   一个预付费模型，包含所有需要通过 JSON 发送的字段
*   请求发送令牌的方法(使用演示用户名和 pass)
*   一个 JS 文件，帮助使用 USB 设备扫描信用卡
*   我从 PayTrace 网站下载的一个测试 PEM 文件
*   PayTrace 的第三方 JS 库，用于加密卡信息并附加在表单帖子上

我全部加载，扫描卡，点击提交，我得到这个:

```
XML Parsing Error: no element found 
```

Enter fullscreen mode Exit fullscreen mode

这是出现在控制台的检查在火狐浏览器。原来这是一个普通的错误，Firefox 在期待一个文件时抛出，但什么也没有得到。

我的路径是正确的，但是“public_key.pem”文件没有附加到我的帖子中。问题？哑剧类型。

## 使其工作

首先，在 Startup.cs 中，我需要这样做来添加 MIME 类型:

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var provider = new FileExtensionContentTypeProvider();
    provider.Mappings[".pem"] = "application/x-pem-file"; 
```

Enter fullscreen mode Exit fullscreen mode

在同一个函数中添加这个:

```
app.UseStaticFiles(); // For the wwwroot folder
app.UseStaticFiles(new StaticFileOptions()
{
    FileProvider = new PhysicalFileProvider(Path.Combine(Directory.GetCurrentDirectory(), @"wwwroot", "api-keys")),
    RequestPath = new PathString("/api-keys"),
    ContentTypeProvider = provider
}); 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记 app。UseStaticFiles()；为您的通用 wwwroot 文件夹。

我把 public_keys.pem 放在“api-keys”里就搞定了！

这里面似乎有点不对劲，所以如果你发现了请纠正我，但是它现在工作了！
# 发送电子邮件。使用 FluentEmail 的网络核心

> 原文：<https://dev.to/lukencode/sending-email-in-net-core-with-fluentemail-4k4m>

我已经很久没有发布关于开源软件 FluentEmail 的文章了。我在 2010 年创建的. net 电子邮件包。在此期间(很大程度上归功于[本·卡尔](https://benjii.me)的出色工作)，流畅的电子邮件已经更新为支持。网芯。

[![FluentEmail](img/13087066bbeb47b0455b6efe9ad01a3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wFNL2ynY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/lukencode/FluentEmail/master/assets/fluentemail_logo_64x64.png)

我最近又一次动手编写代码来改进。net 核心支持——特别是依赖注入。以下是在 ASP.NET 核心 web 应用程序中使用 FluentEmail 的演练。

### 必需的包

`PM> Install-Package FluentEmail.Core`

核心 FluentEmail 类和接口

`PM> Install-Package FluentEmail.Razor`

一个 Razor 的实现`ITemplateRenderer`这个使用 RazorLight 来渲染 Razor 模板作为邮件正文。

`PM> Install-Package FluentEmail.Smtp`

一个使用 System 的`ISender`的 Smtp 实现。引擎盖下的邮件。

有几个其他的包可以用来代替 smtp 或 razor:

*   [FluentEmail.Mailgun](https://github.com/lukencode/FluentEmail/blob/master/src/Senders/FluentEmail.Mailgun)
*   [FluentEmail.SendGrid](https://github.com/lukencode/FluentEmail/blob/master/src/Senders/FluentEmail.Sendgrid) < !-- - [FluentEmail.DotLiquid](https://github.com/lukencode/FluentEmail/blob/master/src/Renderers/FluentEmail.DotLiquid) -- >

### 依赖注入

在以前版本的 FluentEmail 中，电子邮件是使用静态帮助器方法编写的。由于测试和可维护性的困难，现在不推荐这样做。FluentEmail 库现在附带了用于微软依赖注入的扩展方法。下面的代码应该添加到 Startup.cs.
中的 ConfigureServices 方法中

```
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddFluentEmail("defaultsender@test.test")
        .AddRazorRenderer()
        .AddSmtpSender("localhost", 25);
} 
```

Enter fullscreen mode Exit fullscreen mode

`AddFluentEmail("defaultsender@test.test")`扩展方法接受一个默认的发送者地址，并具有设置 IRenderer 的扩展方法，在本例中，RazorLight renderer 使用`.AddRazorRenderer()`，SMTP 发送者使用`.AddSmtpSender("localhost", 25)`。MailGun 和 SendGrid 包也有扩展方法助手。

这些方法将在您的应用程序中提供两个接口，分别用于发送单封电子邮件的`IFluentEmail`和多封电子邮件的`IFluentEmailFactory`。

### 发送邮件

下面是发送电子邮件的最基本的例子。此代码将获取上面配置的 IFluentEmail，并使用 SmtpSender 发送。电子邮件将按照启动时的配置从“ [defaultsender@test.test](mailto:defaultsender@test.test) 发送(这可以用`email.SetFrom()`方法覆盖)

```
public async Task<IActionResult> SendEmail([FromServices]IFluentEmail email)
{
    await email
        .To("test@test.test")
        .Subject("test email subject")
        .Body("This is the email body")
        .SendAsync();

    return View();
} 
```

Enter fullscreen mode Exit fullscreen mode

IFluentEmail 上还有很多其他有用的方法，比如添加抄送或密件抄送收件人、添加附件、设置优先级或设置回复地址。

### 使用 Razor 模板渲染邮件

发送基本的纯文本电子邮件是好的，但是大多数时候你会想要混合一些丰富的数据。FluentEmail 允许你通过使用`.UsingTemplate<T>(string template, T model)`而不是`.Body()`将邮件正文设置为模板而不是普通字符串。这将作为一个模型与一个对象相结合，并被传递给已经设置好的 IRenderer 在我们的例子中是 FluentEmail。剃刀要处理。这里有一个简单的例子:

```
public async Task<IActionResult> SendEmail([FromServices]IFluentEmail email)
{
    var model = new
    {
        Name = "test name"
    };

    var template = "hi @Model.Name this is a razor template @(5 + 5)!";

    await email
        .To("test@test.test")
        .Subject("test email subject")
        .UsingTemplate(template, model)
        .SendAsync();

    return View();
} 
```

Enter fullscreen mode Exit fullscreen mode

这封邮件的正文将呈现为“嗨，测试名称，这是一个 razor 模板 10！”。Razor 渲染器将允许您编写任何有效的 Razor 代码。传递给渲染器的模型可以是任何对象类型——我经常发现使用匿名对象很方便，如上例所示。

FluentEmail 有几个助手方法可以让模板的使用更加简洁。我个人喜欢嵌入式资源选项，因为它使得跨项目共享模板变得容易。

来自文件的渲染模板:

```
.UsingTemplateFromFile($"{Directory.GetCurrentDirectory()}/Mytemplate.cshtml", model) 
```

Enter fullscreen mode Exit fullscreen mode

来自嵌入资源的渲染模板:

```
.UsingTemplateFromEmbedded("Example.Project.Namespace.template-name.cshtml", 
    model, 
    TypeFromYourEmbeddedAssembly.GetType().GetTypeInfo().Assembly) 
```

Enter fullscreen mode Exit fullscreen mode

**注为。NET Core 2 用户:**您需要在包含任何嵌入式 razor 视图的项目中添加下面一行。

`<MvcRazorExcludeRefAssembliesFromPublish>false</MvcRazorExcludeRefAssembliesFromPublish>`

### 在同一范围内发送多封邮件

如果你需要发送来自同一个类或动作的多个电子邮件，你会希望使用 IFluentEmailFactory，而不是重用 IEmail 的一个实例。IFluentEmailFactory 有一个 Create()方法，它将为您提供一个 IFluentEmail 的新实例来使用。

```
public async Task<IActionResult> SendMultiple([FromServices] IFluentEmailFactory emailFactory)
{
    await emailFactory.Create()
        .To("test1@test.test")
        .Subject("test email subject")
        .UsingTemplate("hi @Model.Name this is the first email @(5 + 5)!", new { Name = "test name" })
        .SendAsync();

    await emailFactory.Create()
        .To("test1@test.test")
        .Subject("test email subject")
        .UsingTemplate("hi @Model.Name this is the second email @(5 + 5)!", new { Name = "test name 2" })
        .SendAsync();

    return View();
} 
```

Enter fullscreen mode Exit fullscreen mode

### 延伸

正如我多次提到的，FluentEmail 可以通过实现 ISender 或 ITemplateRenderer 轻松扩展。ISender 决定了电子邮件的实际发送方式。ITemplate 渲染器接受模板字符串和模型，并返回渲染结果。

更多信息请查看 FluentEmail Github 库 。
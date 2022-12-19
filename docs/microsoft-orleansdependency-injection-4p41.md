# 微软奥尔良—依赖注入

> 原文：<https://dev.to/kritner/microsoft-orleansdependency-injection-4p41>

### 微软奥尔良—依赖注入

[![](../Images/3a127c665e9066dd3751eeef47eeea16.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9t84Tn9F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/0%2AJnJHfNGV4tkv8UPw.png)

依赖注入是编写松散耦合、易于测试的代码的重要部分。我以前写过一些关于它的文章，但不是在微软奥尔良的背景下。

微软奥尔良，最喜欢(所有？)应用程序，可以利用依赖注入。我们在奥尔良是怎么做的？幸运的是，它的实现方式与您在工作中已经习惯的方式非常相似。网芯！

如果你不熟悉。net core DI，快速示例:

```
public interface IStuffDoer
{
 void DoStuff();
}

public class StuffDoer : IStuffDoer
{
 public void DoStuff()
 {
  Console.WriteLine("Stuff has been done");
 }
} 
```

(通常)在你的 Startup.cs 或其附近:

```
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
 services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

 // Register services for DI
 services.AddSingleton<IStuffDoer, StuffDoer>();
} 
```

这就是它的全部内容(因为它与 MVC/WebApi 站点相关)。当类构造函数中需要`IStuffDoer`的实例时，一个实例被注入到类中——在本例中是同一个实例，因为我们将其注册为单例。你可以在这里阅读更多关于依赖注入的内容:

[ASP.NET 核心依赖注入](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.1)

#### 我们如何将此应用于奥尔良？

[![](../Images/f5f503bca3dfebb14c8e84c3587c0331.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--doE3m2Lq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AnuH6GgCOQHGVz8Mv) 

<figcaption>照片由[萨拉·巴赫什](https://unsplash.com/@sarabakhshi?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

我们可以在奥尔良通过构建一个新的`IOrleansFunction`来演示这个依赖注入概念。请注意，此功能是为我的 Orleans 系列创建的，位于:

[更新新奥尔良项目，为新奥尔良范例做好更多准备！](https://dev.to/kritner/updating-orleans-project-to-be-more-ready-for-new-orleans-examples-41n2)

首先，让我们从与谷物无关的代码开始——我们将使用并向 IOC 容器注册的东西。

邮件发送界面:

```
public interface IEmailSender
{
 Task SendEmailAsync(string from, string[] to, string subject, string body);
} 
```

和一个实现:

```
public class FakeEmailSender : IEmailSender
{
 private readonly ILogger<FakeEmailSender> _logger;

 public FakeEmailSender(ILogger<FakeEmailSender> logger)
 {
  _logger = logger;
 }

 /// <summary>
 /// Pretend this actually sends an email.
 /// </summary>
 /// <param name="from"></param>
 /// <param name="to"></param>
 /// <param name="subject"></param>
 /// <param name="body"></param>
 /// <returns></returns>
 public Task SendEmailAsync(string from, string[] to, string subject, string body)
 {
  var emailBuilder = new StringBuilder();
  emailBuilder.Append("Sending new Email...");
  emailBuilder.AppendLine();
  emailBuilder.Append($"From: {from}");
  emailBuilder.Append($"To: {string.Join(", ", to)}");
  emailBuilder.Append($"Subject: {subject}");
  emailBuilder.Append($"Body: {Environment.NewLine}{body}");

  _logger.LogInformation(emailBuilder.ToString());

  return Task.CompletedTask;
 }
} 
```

我们可以在我们的`ISiloHostBuilder`中注册这个`FakeEmailSender`。我使用一个小助手类将我所有的 DI 注册保存在它自己的区域中，与 ISiloHostBuilder 分开。

助手类:

```
public static class DependencyInjectionHelper
{
 public static void IocContainerRegistration(IServiceCollection serviceCollection)
 {
  serviceCollection.AddSingleton<IEmailSender, FakeEmailSender>();
 }
} 
```

从`ISiloHostBuilder`调用助手类:

```
.ConfigureServices(DependencyInjectionHelper.IocContainerRegistration) 
```

整个`ISloHostBuilder`方法现在看起来像这样:

```
private static async Task<ISiloHost> StartSilo()
{
 // define the cluster configuration
 var builder = new SiloHostBuilder()
  .UseLocalhostClustering()
  .Configure<ClusterOptions>(options =>
  {
   options.ClusterId = "dev";
   options.ServiceId = "HelloWorldApp";
  })
  .Configure<EndpointOptions>(options => options.AdvertisedIPAddress = IPAddress.Loopback)
  .AddMemoryGrainStorage(Constants.OrleansMemoryProvider)
  .ConfigureApplicationParts(parts =>
  {
   parts.AddApplicationPart(typeof(IGrainMarker).Assembly).WithReferences();
  })
       .ConfigureServices(DependencyInjectionHelper.IocContainerRegistration)
  .UseDashboard(options => { })
  .ConfigureLogging(logging => logging.AddConsole());

 var host = builder.Build();
 await host.StartAsync();
 return host;
} 
```

#### 既然有了注册服务，那就一粒粒用吧！

我将使用新的依赖注入服务，来实现发送电子邮件的功能。是的，我们可以只在粒度内部编写电子邮件发送，但是我还想展示一下依赖注入，这样我们就可以在没有(少量)与粒度相关的样板文件的情况下交换一个“真正的”实现。

新颗粒界面:

```
public interface IEmailSenderGrain : IGrainWithGuidKey, IGrainInterfaceMarker
{
 Task SendEmail(string from, string[] to, string subject, string body);
} 
```

和实施:

```
public class EmailSenderGrain : Grain, IEmailSenderGrain, IGrainMarker
{
 private readonly IEmailSender _emailSender;

 public EmailSenderGrain(IEmailSender emailSender)
 {
  _emailSender = emailSender;
 }

 public async Task SendEmail(string from, string[] to, string subject, string body)
 {
  await _emailSender.SendEmailAsync(from, to, subject, body);
 }
} 
```

在上面的例子中，我们接受了一个在`IEmailSenderGrain`契约的实际实现中使用的`IEmailSender`的实例。通过我们在`ISiloHostBuilder`中的设置，`FakeEmailSender`被自动传递到类中。

#### 在控制台应用程序中连接新的谷物调用

现在，我们需要将新的谷物调用连接到我们的控制台应用程序菜单中——幸运的是，这很简单，因为上面的博文中指出了重构。

添加一个实现`IOrleansFunction`的新类:

```
public class DependencyInjectionEmailService : IOrleansFunction
{
 public string Description => "Shows off dependency injection within a grain implementation.";

 public async Task PerformFunction(IClusterClient clusterClient)
 {
  var grain = clusterClient.GetGrain<IEmailSenderGrain>(Guid.NewGuid());
  Console.WriteLine("Sending out a totally legit email using whatever service is registered with the IEmailSender on the SiloHost");

  var body = @"
     .-'---`-.
   ,' `.
   | 
   | 
    _ 
   , _ ,'-,/-)
   ( *  ,' ,' ,'-)
    `._,) -',-')
      / ''/
    ) / /
      / ,'-'
  ";

  await grain.SendEmail(
   "someDude@somePlace.com", 
   new[] { "someOtherDude@someOtherPlace.com" }, 
   "ayyy lmao",
   body
  );
 }
} 
```

#### 装运它！

让我们看看这是什么样子的运行。

*   建设
*   `dotnet run`silo host
*   `dotnet run`客户端
*   选择新谷物的最终选择

输出:

[![](../Images/535d300dd6bf926be6e56dd1ffbcd146.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CRJ8J8MO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUgf8DPMcgQKHI6r00LuQPQ.png)

请注意，上面的“电子邮件”显示在 Orleans 控制台中，因为`FakeEmailSender`告诉它只“记录”，从函数运行的上下文来看，它会点击 Orleans 日志，而不是菜单式控制台应用程序。

这就是全部了！

此时的代码在 GitHub 资源库的这个版本中:

[krit ner-Blogs/OrleansGettingStarted](https://github.com/Kritner-Blogs/OrleansGettingStarted/releases/tag/v0.40)

相关:

*   [微软奥尔良入门](https://medium.com/@kritner/getting-started-with-microsoft-orleans-882cdac4307f?source=friends_link&sk=1fc3451d71a19dcb49f2c8bbeb6b079e)
*   [微软奥尔良——再利用谷物和谷物状态](https://medium.com/@kritner/microsoft-orleans-reusing-grains-and-grain-state-136977facd42?source=friends_link&sk=f19cfa3f17665c3d700bfe0df56e27a9)
*   [微软奥尔良—报告仪表板](https://medium.com/@kritner/microsoft-orleans-reporting-dashboard-16465d255199)
*   使用多态性更新新奥尔良项目，为新奥尔良范例做好准备！
*   博客文章结束时的代码— [v0.40](https://github.com/Kritner-Blogs/OrleansGettingStarted/releases/tag/v0.40)
*   [微软文档—依赖注入](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.1)
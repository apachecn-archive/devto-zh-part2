# 微软奥尔良——提醒和谷物呼唤谷物

> 原文：<https://dev.to/kritner/microsoft-orleansreminders-and-grains-calling-grains-1ip5>

### 微软奥尔良—提醒和谷物呼叫谷物

琳达·佩雷兹·约翰森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的封面图片

Orleans 是一个 actor model 框架——一个用于跨机器集群轻松创建分布式系统的框架。在本帖中，我们将探索奥尔良的“提醒”功能。

首先，文件:

[计时器和提醒|微软奥尔良文档](https://dotnet.github.io/orleans/Documentation/grains/timers_and_reminders.html)

在奥尔良，提醒可以用来或多或少地执行“时间表”上的任务。我很难想出一个实际上有意义的简单示例，因此，我们将使用“一切正常”警报:

[https://www.youtube.com/embed/Yj7XA3tiGUg](https://www.youtube.com/embed/Yj7XA3tiGUg)

#### 要建立一切正常的警报，我们需要做几件事:

*   在`ISiloHostBuilder `中启用提醒服务——为了简单起见，我们将使用内存提醒服务，并且不需要依赖额外的基础设施
*   一种新的提醒谷物
*   提醒谷物要做的事情

我认为我们可以使用:

[微软奥尔良—依赖注入](https://dev.to/kritner/microsoft-orleansdependency-injection-4p41)

为了发送“假邮件”一切正常的通知。

### 启用提醒服务

从[https://github . com/krit ner-Blogs/OrleansGettingStarted/releases/tag/v 0.40](https://github.com/Kritner-Blogs/OrleansGettingStarted/releases/tag/v0.40)开始，我们将通过向我们的 ISiloHostBuilder 添加以下代码行来启用内存提醒服务。

```
.UseInMemoryReminderService() 
```

完整的方法是:

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
  .UseInMemoryReminderService()
  .ConfigureLogging(logging => logging.AddConsole());

 var host = builder.Build();
 await host.StartAsync();
 return host;
} 
```

其他一些可能使用的提醒服务包括`AzureTableReminderService`和`AdoNetReminderService`。

### 提醒纹

让我们创造我们的一切都好报警粮！我在写这篇文章的时候发现，在“提醒”之间有一个 1 分钟的最小时间间隔，所以不幸的是，我们将不会使用最初计划的 3 秒钟:(

#### 反正……我们的纹界面:

```
public interface IEverythingIsOkGrain : IGrainWithStringKey, IRemindable
{
 Task Start();
 Task Stop();
} 
```

在上面的例子中，我们做了一个非常标准的颗粒接口，并额外实现了`IRemindable`。接口上附加了两个方法，一个启动提醒，一个停止提醒。注意，`IRemindable`接口要求实现类实现:

```
Task ReceiveReminder(string reminderName, TickStatus status); 
```

#### Grain Implementation—亦 Grainception！

正如我之前提到的，我们将使用从之前的帖子中创建的`FakeEmailSender`，以及让我们要创建的颗粒利用其他颗粒(颗粒接收)！

这可能看起来像:

```
[StorageProvider(ProviderName = Constants.OrleansMemoryProvider)]
public class EverythingIsOkGrain : Grain, IEverythingIsOkGrain
{
 IGrainReminder _reminder = null;

 public async Task ReceiveReminder(string reminderName, TickStatus status)
 {
  // Grain-ception!
  var emailSenderGrain = GrainFactory
   .GetGrain<IEmailSenderGrain>(Guid.Empty);

  await emailSenderGrain.SendEmail(
   "homer@anykey.com",
   new[] 
   {
    "marge@anykey.com",
    "bart@anykey.com",
    "lisa@anykey.com",
    "maggie@anykey.com"
   },
   "Everything's ok!",
   "This alarm will sound every 1 minute, as long as everything is ok!"
  );
 }

 public async Task Start()
 {
  if (_reminder != null)
  {
   return;
  }

  _reminder = await RegisterOrUpdateReminder(
   this.GetPrimaryKeyString(),
   TimeSpan.FromSeconds(3),
   TimeSpan.FromMinutes(1) // apparently the minimum
  );
 }

public async Task Stop()
 {
  if (_reminder == null)
  {
   return;
  }

  await UnregisterReminder(_reminder);
  _reminder = null;
 }
} 
```

上面有一些值得注意的事情:

*   我们让颗粒有状态，这样(理论上)提醒会在关机时持续。注意，在我们的例子中不会，因为使用了内存存储，我*认为*会。
*   `IGrainReminder _reminder = null;` —保存对我们已启动提醒的引用，用于停止提醒。
*   这是我们实际定义提醒发生时会发生什么的方法。
*   `var emailSenderGrain = GrainFactory.GetGrain<IEmailSenderGrain>(Guid.Empty);` —这里我们使用一种稍微不同的方法来检索一个颗粒，因为我们实际上是从`SiloHost`而不是`Client`开始做的。注意，这个被拉取的颗粒也利用了依赖注入，但是它的依赖只被注入到实际需要它的颗粒中，而不是这个提醒颗粒中。

### 新增谷物菜单选项

像往常一样，我们将创建一个新的`IOrleansFunction`具体化，用于我们的菜单系统；新的谷物也将从我们的`IOrleansFunctionProvider`返回。

```
public class EverythingIsOkReminder : IOrleansFunction
{
 public string Description => "Demonstrates a reminder service, notifying the user that everything is ok... every three seconds...";

 public async Task PerformFunction(IClusterClient clusterClient)
 {
  var grain = clusterClient.GetGrain<IEverythingIsOkGrain>(
   $"{nameof(IEverythingIsOkGrain)}-{Guid.NewGuid()}"
  );

  Console.WriteLine("Starting everything's ok alerm after key press.");
  Console.ReadKey();

  Console.WriteLine("Starting everything's ok reminder...");
  await grain.Start();

  Console.WriteLine("Reminder started. Press any key to stop reminder.");
  Console.ReadKey();

  await grain.Stop();

  ConsoleHelpers.ReturnToMenu();
 }
} 
```

### 试一试

按照常规，我们将开始`SiloHost`、`Client`，并尝试新的纹理。

[![](../Images/753060561fdd8a31c78fad17ae4c1c8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--weKecRQn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AohX0WbFAIMozjHwyBvElHA.png) 

<figcaption>没事提醒</figcaption>

在上面，你可以看到我们的“FakeEmail”发送到了奥尔良日志，声明一切正常。

由于在之前的帖子中添加了[奥尔良仪表板](https://medium.com/@kritner/microsoft-orleans-reporting-dashboard-16465d255199),我们可以看到的另一件很酷的事情是:

[![](../Images/9bd5606041088be66f1ceec972192f45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XFHV8C4V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjIRcL58X2ZgDvqG2QDgASg.png) 

<figcaption>仪表盘上显示提醒</figcaption>

整洁！

在这篇文章中，我们了解了一点奥尔良的另一个特色——提醒！您可以在以下网址找到这篇文章的代码:

[krit ner-Blogs/OrleansGettingStarted](https://github.com/Kritner-Blogs/OrleansGettingStarted/releases/tag/v0.45)

#### 相关:

*   [微软奥尔良入门](https://medium.com/@kritner/getting-started-with-microsoft-orleans-882cdac4307f?source=friends_link&sk=1fc3451d71a19dcb49f2c8bbeb6b079e)
*   [微软奥尔良——再利用谷物和谷物状态](https://medium.com/@kritner/microsoft-orleans-reusing-grains-and-grain-state-136977facd42?source=friends_link&sk=f19cfa3f17665c3d700bfe0df56e27a9)
*   [微软奥尔良—报告仪表板](https://medium.com/@kritner/microsoft-orleans-reporting-dashboard-16465d255199)
*   使用多态性更新新奥尔良项目，为新奥尔良范例做好准备！
*   [微软奥尔良—依赖注入](https://medium.com/@kritner/microsoft-orleans-dependency-injection-6379d52a7169?source=friends_link&sk=6c3883a5213d65eb251b56c717e0e4f2)
*   发布后的代码— [v0.45](https://github.com/Kritner-Blogs/OrleansGettingStarted/releases/tag/v0.45)
*   微软奥尔良文档— [提醒和定时器](https://dotnet.github.io/orleans/Documentation/grains/timers_and_reminders.html)
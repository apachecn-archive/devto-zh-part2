# 。net core 控制台应用程序选项{T}配置

> 原文：<https://dev.to/kritner/net-core-console-application-ioptionst-configuration-42od>

[![](img/1f3cef8459647f5848655177c290a9e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JE3VsIfH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/863/1%2AZh5hmHx-tlmpZDoB4LFkBQ.png)

所以，我对我的谷歌技能失去了信心，似乎没有一个一站式商店，我可以找到关于建立一个带有`IServiceProvider`和利用`IOptions<T>`的. net 核心控制台应用程序的信息。

在控制台应用程序中第一次需要配置的过程中——我知道这很疯狂。该项目目前使用 [AutoFac](https://autofac.org/) 作为它的 IOC 容器——尽管还需要进一步研究。netcore 的内置 IOC 容器，我可能想切换到它！

第一次想要为应用程序使用配置的基础是根据环境为外部资源使用不同的端点。这些配置值将在应用程序入口点(或其附近)加载，并需要在应用程序内部进行深入访问，很可能甚至不在同一个项目中。

我怎样才能做到这一点，而不必在任何地方设置一些静态成员来访问所有内容呢？这让我找到了`IOptions<T>` ( [Doc](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-2.1) ) — T 是一个配置类。`IOptions<T>`允许将配置值注入到一个类中，这正是我所需要的，并且避免了我所担心的在整个调用堆栈中传递配置集合，或者在应用程序中的某个地方使用静态成员。

在控制台应用程序中，我们需要做的第一件事是创建一个配置文件。

appsettings.json:

```
{  "Environment":  "whatever"  } 
```

并将其加载到我们的控制台应用程序的入口点

程序. cs:

```
public static class Program 
{
 static Program() 
 {
   var builder = new ConfigurationBuilder()
     .SetBasePath(Directory.GetCurrentDirectory())
     .AddJsonFile($"appsettings.json", optional: false, reloadOnChange: false)
     .AddEnvironmentVariables();

   var configuration = builder.Build();
 } 
} 
```

好吧！文件已加载，它目前什么也不做！接下来，我们想要加载一个特定于环境的 json 文件，但是为了做到这一点，我们需要一个环境的概念。似乎环境通常是通过“环境变量”来控制的(没有关系？).根据操作系统的不同，设置环境变量有多种方法。其中一些方法包括:

*   [窗户](http://www.dowdandassociates.com/blog/content/howto-set-an-environment-variable-in-windows-command-line-and-registry/)
*   [Mac](https://apple.stackexchange.com/a/106823)
*   [Ubuntu](https://askubuntu.com/a/58828/823773)

我将为一个看似标准的`ASPNETCORE_ENVIRONMENT`设置一个新的环境变量为“local”。我们将使用的另一个示例环境是“test”。

现在我们可以着手为其他环境创建一些新的配置文件。

appsettings.local.json:

```
{  "Environment":  "local"  } 
```

appsettings.test.json

```
{  "Environment":  "test"  } 
```

现在我们有了一个“基本”配置 appsettings.json，以及特定于环境的配置 appsettings.local.json 和 appsettings.test.json。这再加上我们新的环境变量，应该允许我们以一种有意义的方式(很快)开始使用一些配置。

现在，让我们看看加载不同的环境配置文件是什么样子的。从我们最初的 Program.cs 示例中

```
public static class Program 
{
 static Program() 
 {
   var builder = new ConfigurationBuilder()
     .SetBasePath(Directory.GetCurrentDirectory())
     .AddJsonFile($"appsettings.json", optional: false, reloadOnChange: false)
     .AddEnvironmentVariables();

   var configuration = builder.Build();
 } 
} 
```

让我们做一些更新:

```
public static class Program 
{
 static Program() 
 {
   string env = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");
   if (string.IsNullOrWhiteSpace(env))
   {
     // TODO this could fall back to an environment, rather than exceptioning?
     throw new Exception("ASPNETCORE_ENVIRONMENT env variable not set.");
   }

   Console.WriteLine($"Bootstrapping application using environment {env}");

   var builder = new ConfigurationBuilder()
     .SetBasePath(Directory.GetCurrentDirectory())
     .AddJsonFile($"appsettings.json", optional: false, reloadOnChange: false)
     .AddJsonFile($"appsettings.{env}.json", optional: false, reloadOnChange: false)
     .AddEnvironmentVariables();

   var configuration = builder.Build();
 } 
} 
```

在上面的例子中，你可以看到我们正在将存储在环境变量 ASPNETCORE_ENVIRONMENT 中的值加载到 env 中，当/如果这个变量不可用，我们(当前)会抛出一个异常。然后我们打印我们加载的环境变量值，最后加载适当的 appsettings。根据环境变量 ASPNETCORE_ENVIRONMENT 的值，您可以看到加载的环境发生了变化。

既然我们已经成功地加载了基于环境变量的配置文件，那么让我们进入一些选项。

我们首先需要一个配置类，我们将做一些简单的事情，比如针对 API 端点的特定于环境的配置:

ApiConfig.cs

```
public class ApiConfig
{
 public string RootUrl { get; set; }
 public int Port { get; set; }
} 
```

在上面我们已经定义了一个类，我们将创建 json 来表示我们每个环境配置文件中的那些类。

appsettings.local.json

```
{  "Environment":  "local",  "ApiConfig":  {  "RootUrl":  "http://localhost",  "Port":  5001  }  } 
```

appsettings.test.json

```
{  "Environment":  "local",  "ApiConfig":  {  "RootUrl":  "https://some.url.com",  "Port":  51321  }  } 
```

我们现在已经有了足够的“东西”，可以将一些东西加载到一个`IOptions<T>`——我们的 ApiConfig，或`IOptions<ApiConfig>`。

我们将对我们的程序 ctor 进行另一项更改:

```
public static class Program 
{
 private static readonly IServiceProvider ServiceProvider;

 static Program() 
 {
   string env = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");
   if (string.IsNullOrWhiteSpace(env))
   {
     // TODO this could fall back to an environment, rather than exceptioning?
     throw new Exception("ASPNETCORE_ENVIRONMENT env variable not set.");
   }

   Console.WriteLine($"Bootstrapping application using environment {env}");

   var builder = new ConfigurationBuilder()
     .SetBasePath(Directory.GetCurrentDirectory())
     .AddJsonFile($"appsettings.json", optional: false, reloadOnChange: false)
     .AddJsonFile($"appsettings.{env}.json", optional: false, reloadOnChange: false)
     .AddEnvironmentVariables();

   var configuration = builder.Build();

   var serviceCollection = new ServiceCollection();
   serviceCollection.AddOptions();
   serviceCollection.Configure<ApiConfig>  (configuration.GetSection(nameof(ApiConfig)));

   this.ServiceProvider = serviceCollection.BuildServiceProvider();
 } 
} 
```

上面几个新东西:

*   serviceCollection —我们注册服务的地方
*   互联网服务商

我们现在可以按照普通的 dotnetcore 解析器来“解析”我们注册的组件，我们新的`IOptions<T>`可以这样使用:

SomeClass.cs

```
public class SomeClass
{
 private readonly IOptions<ApiConfig> _apiConfig;

 public SomeClass(IOptions<ApiConfig> apiConfig)
 {
   _apiConfig = apiConfig;
 }

 public void DoStuff()
 {
   Console.WriteLine(_apiConfig.Value.RootUrl);
   Console.WriteLine(_apiConfig.Value.Port);
 }
} 
```

加载的环境配置将决定我们将`ApiConfig`的哪个“实例”注入某个类。

最后一个小问题是，我实际上使用的是 AutoFac，而不是。net 核心 IOC 容器。因此，我所有的决议都是通过 AutoFac 完成的，而我的`IOptions<T>`是通过注册的。net core 的 IOC 容器。

这是另一个非常简单的变化(尽管我至少花了一段时间才弄明白)。最后，我创建了一个新的 helper 方法，它包含了我的 IServiceProvider 和 ContainerBuilder(来自 AutoFac 注册)。helper 方法看起来像:

```
public static void RegisterConfigurationInjectionsAutoFac(IServiceProvider serviceProvider, ContainerBuilder builder)
{
 builder.Register(context => serviceProvider.GetService<IOptions<ApiConfig>>());
} 
```

并且可以从我们的普通组合根/入口点直接调用，该组合根/入口点将 AutoFac 容器放在一起。
# 第 006 集-配置-ASP.NET 核心:从 0 到过度杀伤

> 原文：<https://dev.to/joaofbantunes/episode-006---configuration---aspnet-core-from-0-to-overkill-3ei8>

在这一集里，我们来看看 ASP.NET 核心中的配置，可能的资源，如何读取它们，选项模式，最后是开发时间的秘密。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/2x6tMPcBJqY](https://www.youtube.com/embed/2x6tMPcBJqY)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

与 ASP.NET 相比，在 ASP.NET 核心中管理配置的方式确实发生了变化，在那里我们有`web.config`文件(以及其他相同格式的文件)。在这篇文章中，我们将看看这个新的配置，看看它有多强大。

关于这个话题更深入的信息，请务必查看这里的文档。

## 配置提供者

ASP.NET 核心配置带来的一大优势是能够拥有多个配置提供者，而且能够以一致的方式在代码中使用这些配置，而不管它们的来源。

例如，我们可以通过环境变量、命令行参数和 JSON 文件进行配置，然后使用它们，就好像它们都来自同一个地方。

(从文档中复制)我们目前有以下可用的提供商:

| 供应者 | 从提供配置 |
| --- | --- |
| Azure Key Vault 配置提供程序 | 蓝色钥匙保险库 |
| 命令行配置提供程序 | 命令行参数 |
| 自定义配置提供程序 | 委托源 |
| 环境变量配置提供程序 | 环境变量 |
| 文件配置提供程序 | 文件(INI、JSON、XML) |
| 每文件密钥配置提供程序 | 目录文件 |
| 内存配置提供程序 | 内存中集合 |
| 用户机密(机密管理器) | 用户配置文件目录中的文件 |

正如您在上表中看到的，我们也可以创建自己的提供程序，以访问不同的配置格式或位置，但这超出了本文的范围，我们将只使用一些现有的提供程序。

为了让大家了解一下我们可以用 ASP.NET 核心的配置基础设施做些什么，在这篇文章中，我们将对命令行、文件和用户机密提供者进行一些探讨。

### 配置提供商

我们需要做的第一件事是配置我们想要使用的配置提供者(或者正如我们将看到的，不是真的😛).

为了配置提供者，我们进入`Program`类，在这里我们创建`IWebHostBuilder`，我们可以调用`ConfigureAppConfiguration`方法来设置我们想要的提供者。

正如我们在前几集看到的，`WebHost` `CreateDefaultBuilder`方法已经自带了许多现成的设置，配置提供者就是其中之一。如果我们看一下这个方法的源代码，我们可以看到已经存在的东西。

```
//...
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var env = hostingContext.HostingEnvironment;

    config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
            .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);

    if (env.IsDevelopment())
    {
        var appAssembly = Assembly.Load(new AssemblyName(env.ApplicationName));
        if (appAssembly != null)
        {
            config.AddUserSecrets(appAssembly, optional: true);
        }
    }

    config.AddEnvironmentVariables();

    if (args != null)
    {
        config.AddCommandLine(args);
    }
})
//... 
```

Enter fullscreen mode Exit fullscreen mode

(在 [GitHub](https://github.com/aspnet/MetaPackages/blob/62d9794c633a9a2c502334d525d81c454ac29264/src/Microsoft.AspNetCore/WebHost.cs#L165) 上的完整源代码)

通过查看源代码，我们可以看到配置了 5 个提供者:

*   2 对于 JSON 文件，第一个名为`appsettings.json`，另一个名为当前环境的名称
*   用户机密提供者，仅在开发环境中
*   环境变量提供程序
*   命令行参数提供程序

需要指出的重要一点是，提供者的配置顺序是相关的。添加的提供程序会用相同的密钥覆盖任何以前的配置。例如，如果我们在`appsettings.json`中设置了一个值，但是在开发模式中我们想要一个不同的值，我们只需在`appsettings.development.json`中放置一个具有相同键的配置。其他供应商也是如此。

鉴于这些提供者已经设置好了，对于这篇文章的目的来说，不需要配置其他任何东西。

## 创建一些配置

在我们看到如何使用代码中的配置之前，先创建一些配置可能是个好主意😀

让我们首先在项目的根目录下创建两个 JSON 文件——`appsettings.json`和`appsettings.development.json`——并向它们添加以下内容。

`appsettings.json`

```
{  "SomeRoot":  {  "SomeSubRoot":  {  "SomeKey":  12345,  "AnotherKey":  "QWERTY"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

`appsettings.development.json`

```
{  "SomeRoot":  {  "SomeSubRoot":  {  "SomeKey":  67890  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

为了通过命令行传递一些配置，我们可以做`dotnet run -- --SomeRoot:SomeSubRoot:CmdLineKey 13579`。冒号用于定义一个层次结构，所以 JSON 中的等价形式应该是:

```
{  "SomeRoot":  {  "SomeSubRoot":  {  "CmdLineKey":  13579  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 IDE 来运行应用程序，可能会有一个菜单让您传递命令行参数。

*   在 Visual Studio 2017 中，您可以->右键单击项目->“属性”->“调试”->“应用程序参数”(注意，这在使用 IIS Express 时不起作用)
*   在我正在使用的 JetBrains Rider 中，你进入运行配置，并将参数添加到“程序参数”输入中

现在我们有一些配置要访问，让我们开始吧。

## 访问配置

让我们从最简单(但不是最棒)的访问配置的方式开始，使用`IConfiguration`。

在我们的`Startup`类中，甚至在我们的控制器或服务中，我们可以注入一个`IConfiguration`,并使用它来访问配置。然后我们可以，例如，做下面的事情来得到一些值:

```
config.GetValue<int>("SomeRoot:SomeSubRoot:SomeKey"); //returns 67890
config.GetValue<int>("SomeRoot:SomeSubRoot:CmdLineKey"); //returns 13579
//or
var section = config.GetSection("SomeRoot:SomeSubRoot");
section.GetValue<string>("AnotherKey"); //returns "QWERTY" 
```

Enter fullscreen mode Exit fullscreen mode

尽管这很有效，而且对于真正简单的东西来说可能就足够了，但这并不是很好，在应用程序中散布像`"SomeRoot:SomeSubRoot:SomeKey"`这样的字符串也不是很好(即使我们把它放在常量中)。

比这种访问我们的配置的方式更好的是拥有代表它们的类，提供一种更干净和类型安全的方式来访问这些值。要解决这个问题，请输入 options 模式。

## 选项模式

ASP.NET 核心中引入的选项模式允许我们轻松地将配置绑定到 POCOs。为了映射我们之前设置的内容，我们创建了几个类:

`SomeRootConfiguration.cs`

```
public class SomeRootConfiguration
{
    public SomeSubRootConfiguration SomeSubRoot { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

`SomeSubRootConfiguration.cs`

```
public class SomeSubRootConfiguration
{
    public int SomeKey { get; set; }
    public string AnotherKey { get; set; }
    public int CmdLineKey { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，为了将配置绑定到这些类，我们可以在`Startup`的`ConfigureServices`方法中添加下面一行:

`Startup.cs`

```
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        //...
        services.Configure<SomeRootConfiguration>(_config.GetSection("SomeRoot"));
        //...
    }
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

然后为了使用它，我们可以进入`GroupsController`并添加`IOptions<SomeRootConfiguration>`来使用我们结构良好的配置类。或者，我们可以注入`IOptionsSnapshot<SomeRootConfiguration>`，它允许应用程序加载运行时更改的配置。

## 忌碘剂注射

尽管 options 模式比我们之前看到的有了很大的改进，但它还可以进一步改进。

除非是为了重载功能，否则注入`IOptions`并不真正有用，并且增加了一个实际上并不需要的额外依赖。当注入控制器时，这个额外的`using`不是什么大问题，因为我们已经在 ASP.NET 核心 MVC 领域。但是把它注入到其他服务/类中呢？强迫一个依赖项只是为了在其他库中注入配置，而这些配置可能在非 ASP.NET 核心应用程序(例如控制台应用程序、使用不同的 web 框架开发的 web 应用程序)中使用，这是一个难以下咽的药丸。

幸运的是，这个问题很容易解决。关于这个问题的更详细的讨论，你可以查看[这篇优秀的文章](https://www.strathweb.com/2016/09/strongly-typed-configuration-in-asp-net-core-without-ioptionst/)，但是我将在这里快速浏览一个解决方案。

我们可以不使用`services.Configure`方法，而是将配置直接绑定到类，然后将其作为单例注入。我们可以这样做:

`Startup.cs`

```
public IServiceProvider ConfigureServices(IServiceCollection services)
{
    //... 
    var someRootConfiguration = new SomeRootConfiguration();
    _config.GetSection("SomeRoot").Bind(someRootConfiguration);
    services.AddSingleton(someRootConfiguration); 
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

这解决了我们的问题，现在，在控制器中，我们可以简单地注入一个`SomeRootConfiguration`，不需要`IOptions`。

为了避免为我们想要添加的所有类型的配置重复这段代码，我们可以创建一个扩展方法来处理这个逻辑。

`ServiceCollectionExtensions.cs`

```
public static class ServiceCollectionExtensions
{
    //...

    public static TConfig ConfigurePOCO<TConfig>(this IServiceCollection services, IConfiguration configuration) 
      where TConfig : class, new()
    {
        if (services == null) throw new ArgumentNullException(nameof(services));
        if (configuration == null) throw new ArgumentNullException(nameof(configuration));

        var config = new TConfig();
        configuration.Bind(config);
        services.AddSingleton(config);
        return config;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用以下内容替换之前的配置注册:

`Startup.cs`

```
public IServiceProvider ConfigureServices(IServiceCollection services)
{
    //... 
    services.ConfigurePOCO<SomeRootConfiguration>(_config.GetSection("SomeRoot"));
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在看起来很像普通的配置注册，但是没有了`IOptions`的负担。

## 用户机密

我们应该考虑的另一个有趣的配置提供者是用户秘密提供者。

用户秘密提供者在开发过程中被用来防止秘密出现在源代码控制中，比如 API 键、连接字符串等等。在生产中，您可能会使用其他方式来提供这些类型的配置，比如环境变量或保密服务(比如 Azure Key Vault)。

用户机密没有被加密，它们只是被存储在不同的路径中，以避免意外地将它们添加到源代码控制中。想了解更多关于用户秘密的信息，请访问[文档](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-2.1)。

在向应用程序添加机密之前，我们需要向它添加一个标识符，这样当启动应用程序时，它可以从机密存储中获取正确的配置。为此，进入应用程序的`csproj`并在`TargetFramework`下的`UserSecretsId`元素中添加一个 GUID。

在这种情况下，我们进入`CodingMilitia.PlayBall.GroupManagement.Web.csproj`并做:

```
<!-- ... -->
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <UserSecretsId>A1CD619E-A374-4208-888B-42F3DC489F14</UserSecretsId>
</PropertyGroup>
<!-- ... --> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以通过做`dotnet user-secrets set "DemoSecrets:SomeKey" "02468"`来添加一些秘密。

为了访问已配置的秘密，我们的做法与其他类型的提供者完全相同，所以我只是创建了另一个 POCO，并像我们在上一节中看到的那样对它进行了配置。

`DemoSecretsConfiguration.cs`

```
public class DemoSecretsConfiguration
{
    public int SomeKey { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Startup.cs`

```
public IServiceProvider ConfigureServices(IServiceCollection services)
{
    //... 
    services.ConfigurePOCO<DemoSecretsConfiguration>(_config.GetSection("DemoSecrets"));
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

我会说非常整洁😀

## 其他

ASP.NET 核心配置介绍到此结束。像往常一样，还有很多要探索的，但我认为这包括了基础。请务必阅读文档以获取更多信息(ASP.NET 核心的文档总体来说相当不错)。

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode006)。

请发送您的任何反馈，以便下一篇帖子/视频可以更好，甚至调整到更有趣的主题。

谢谢你的来访，西阿兹！

* * *

### 链接文章

没有选项的 ASP.NET 核心中的强类型配置<T>T1】
# 基于微服务的应用，采用 ASP.NET 核心通用主机

> 原文：<https://dev.to/jeikabu/microservice-based-application-with-aspnet-core-generic-host-cel>

我们的“Z+”平台的当前实现基本上很好，但我们肯定可以做出改进。我们已经开始制作基于 ASP.NET 核心的“第二代”客户端原型。

## [T1。网络核心通用主机](#net-core-generic-host)

关于[ASP.NET 核心](https://docs.microsoft.com/en-us/aspnet/core/)的大部分报道都集中在[网络主机](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/web-host)上，它通过[中央网络服务器](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel)托管网络应用。然而，它还有一个用于普通应用程序的[“通用主机”](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/generic-host)——那些不处理 HTTP 请求的应用程序。

将`Microsoft.Extensions.Hosting`包添加到您的项目:

```
dotnet <project_name> add package Microsoft.Extensions.Hosting 
```

Enter fullscreen mode Exit fullscreen mode

将扩展方法和类型纳入范围:

```
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting; 
```

Enter fullscreen mode Exit fullscreen mode

在`Program.cs` :

```
class Program
{
    static async Task Main(string[] args)
    {
        var host = new HostBuilder()
        .ConfigureHostConfiguration(configBuilder => {
            //...
        })
        .ConfigureServices((hostContext, services) => {
            //...
        })
        .Build();
        await host.RunAsync();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就其本身而言，这并没有多大作用。但是`ConfigureServices()`用于向服务(“依赖注入”)容器注册服务(“依赖”)。

## 服务

IHostedService 定义了由主机管理的后台任务的方法。[微软。extensions . hosting . background service](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.backgroundservice)是实现长期运行服务的基类。阅读:

*   [托管服务的后台任务](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/hosted-services)
*   [在微服务中实现后台任务](https://docs.microsoft.com/en-us/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)

这里有一个服务启动我们的[ASP.NET 核心网络主机](///2018/08/24/aspnetcore-thrift.html) :

```
public class HttpXy : BackgroundService
{
    public HttpXy(IConfiguration configuration, IZxyContext context)
    {
        //...
    }

    protected override Task ExecuteAsync(CancellationToken token)
    {
        var webHostBuilder = WebHost.CreateDefaultBuilder();
        //...
        webHost = webHostBuilder.Build();
        return webHost.StartAsync(token);
    }

    public override Task StopAsync(CancellationToken token)
    {
        return webHost.StopAsync(token);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

ASP.NET 核心使用基于构造函数的依赖注入(DI ),因此构造函数参数(其依赖项)将从注册的服务中解析。

可以使用 [`AddHostedService()`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionhostedserviceextensions.addhostedservice) 或 [`AddSingleton()`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton) :
向通用主机注册每个`BackgroundService`

```
 // In Program.cs
    var host = new HostBuilder()
    .ConfigureServices((hostContext, services) => {
        //services.AddSingleton(typeof(IHostedService), typeof(HttpXy));
        services.AddSingleton<IHostedService, HttpXy>();
    }) 
```

Enter fullscreen mode Exit fullscreen mode

现在它们的启动和生存期都由主机管理。

## 插件

我们还在用 [MEF2/System。Composition](//./migrating-from-mef1-to-mef2-o87) 将我们的程序分成可加载的共享库。我们的 HTTP 服务的程序集还包含:

```
[Export(typeof(IServicePlugin))]
public class HttpPlugin : IServicePlugin
{
    public string Name => "http";
    public Type GetService()
    {
        return typeof(HttpXy);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们尽可能简单地保持我们导出的类，因为 [GetExports()](https://docs.microsoft.com/en-us/dotnet/api/system.composition.compositioncontext.getexports) 实例化了它们。以前，我们的一些类型有非常重要的初始化代码，因此即使我们最终不使用它们，加载它们也很昂贵。

我们为插件目录中的所有共享库创建复合容器:

```
public class MEF2Plugins
{
    public MEF2Plugins(string path)
    {
        var files = System.IO.Directory.EnumerateFiles(path, "*.dll", System.IO.SearchOption.AllDirectories);
        foreach (var file in files)
        {
            var configuration = new ContainerConfiguration();
            var asm = Assembly.LoadFrom(file);
            configuration.WithAssembly(asm);
            containers.Add(configuration.CreateContainer());
        }
    }

    public List<T> GetExports<T>()
    {
        var ret = new List<T>();
        foreach (var container in containers)
        {
            ret.AddRange(container.GetExports<T>());
        }
        return ret;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

将找到的所有服务添加到 DI 容器:

```
public static void Load(IServiceCollection services, ServiceConfiguration config)
{
    var plugins = mef2.GetExports<IServicePlugin>();
    foreach (var plugin in plugins)
    {
        // Filter enabled plugins
        if (config.IsEnabled(plugin))
        {
            services.AddSingleton(typeof(IHostedService), plugin.GetService());
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个博客向我们介绍了 [Scrutor](https://github.com/khellang/Scrutor) ，它看起来像是使用为 ASP.NET 核心定制的 MEF 的替代方案。我们以后可能会调查它。

## 配置

ASP.NET 核心拥有广泛的应用程序配置系统。背景阅读:

*   很早就看到了这个关于如何使用 JSON 的博客，但是看起来并不完整
*   [这里的](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/index)是关于 ASP.NET 核心配置的 MS 文档
*   与之密切相关的是[保护密码等敏感数据](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets)
*   [这篇博客](https://weblog.west-wind.com/posts/2016/May/23/Strongly-Typed-Configuration-Settings-in-ASPNET-Core)展示了如何通过`IOptions<T>`进行强类型配置
*   和[相关的 MS 文档](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options),这些文档提供了额外的细节
*   [这篇文章](https://www.strathweb.com/2016/09/strongly-typed-configuration-in-asp-net-core-without-ioptionst/)讲述了如何使用 POCO 配置，而不依赖于`Microsoft.Extensions.Options`

在`appsettings.json` :

```
{  "urls":  "http://*:8284",  "zxy":{  "http":{  "port":8283  },  "nng":{  "brokerIn":  "ipc://zxy-brokerIn",  "brokerOut":  "ipc://zxy-brokerOut"  },  },  } 
```

Enter fullscreen mode Exit fullscreen mode

配置数据是分层的:`"zxy"`是一个区段，`"http"`和`"nng"`是它的子区段。

需要将`appsettings.json`复制到输出文件夹。向项目文件添加:

```
<ItemGroup>
    <None Update="appsettings.json" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

在`Program.cs`中添加配置:

```
var host = new HostBuilder()
.ConfigureHostConfiguration(configBuilder => {
    configBuilder.AddJsonFile("appsettings.json");
    configBuilder.AddJsonFile("local.json", optional: true);
    configBuilder.AddCommandLine(args);
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们添加了三个“提供者”:两个 JSON 文件和命令行参数。配置源按指定的顺序处理，在这种情况下从`appsettings.json`开始。如果存在，它将被来自`local.json`、[的值覆盖(它可能是不应被签入 SCC 的本地首选项)。最后，命令行参数具有最高优先级。](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_)

`AddCommandLine()`允许我们从命令行覆盖设置。例如，如果在`launch.json`中使用 Visual Studio 代码:

```
"configurations":  [  {  "args":  ["--zxy:http:port=9001"], 
```

Enter fullscreen mode Exit fullscreen mode

特别要注意的是，“节”和“键”的值由“ **`:`** ”分隔。

因为我们希望将我们的服务视为自包含插件，所以我们不通过 DI:
提供特定的`IOption<>`配置类型

```
class Config
{
    public int Port {get; set;}
}

public class HttpXy : BackgroundService
{
    IConfiguration configuration;
    Config config;
    IZxyContext zxyContext;

    public HttpXy(IConfiguration configuration, IZxyContext context)
    {
        this.configuration = configuration;
        var section = configuration.GetSection("zxy:http");
        config = new Config();
        section.Bind(config);
        zxyContext = context;
    }

    protected override Task ExecuteAsync(CancellationToken token)
    {
        var webHostBuilder = WebHost.CreateDefaultBuilder()
        .UseConfiguration(configuration)
        .UseKestrel(options => {
            // Set the listening port using the `zxy:http:port` value
            var port = config.Port;
            options.Listen(IPAddress.Loopback, port);
        }) 
```

Enter fullscreen mode Exit fullscreen mode

每个服务都依赖于配置根。它现在可以拥有自己的配置节(即`zxy:http`)，可以通过类型安全的方式(通过`Config`实例)访问。

或者，可以用更长的`configuration.GetSection("zxy:http").GetValue<int>("port")`加载单个值。

请注意，`WebHost`也可以隐式配置，因为默认的 WebHostBuilder [会在配置中查找`urls`和其他值](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/web-host#server-urls)。

## 测井

记录系统同样是广泛的。必读[《ASP.NET 岩心录井》](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/)。

将扩展方法纳入范围:

```
using Microsoft.Extensions.Logging; 
```

Enter fullscreen mode Exit fullscreen mode

配置日志记录并添加显示或处理日志记录的“提供者”:

```
 var host = new HostBuilder()
    .ConfigureLogging((hostingContext, loggingBuilder) => {
        loggingBuilder.AddConfiguration(hostingContext.Configuration.GetSection("Logging"));
        // Add providers
        loggingBuilder.AddConsole();
        loggingBuilder.AddDebug(); // Visual Studio "Output" window
        loggingBuilder.AddEventSourceLogger();
    }) 
```

Enter fullscreen mode Exit fullscreen mode

“事件源”选项很有趣，因为它允许您使用 [PerfView](https://github.com/Microsoft/perfview) 。还需要添加包:

```
dotnet <project_name> add package Microsoft.Extensions.Logging.EventSource 
```

Enter fullscreen mode Exit fullscreen mode

可以将[配置](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/index#configuration)添加到`appsettings.json`中，以配置默认和每个类别(如`"System"`)的日志记录级别:

```
{  "Logging":  {  "LogLevel":  {  "Default":  "Debug",  "System":  "Information",  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过 DI ( `T`是日志类别)获得一个 logger 实例`ILogger<T>`，并使用各种`Log{LogLevel}()`方法:
记录消息

```
public class NngBroker : BackgroundService
{
    public NngBroker(ILogger<NngBroker> logger)
    {
        this.logger = logger;
        logger.LogInformation("Broker started");
    }

    protected override async Task ExecuteAsync(CancellationToken token)
    {
        //...
    }

    readonly ILogger<NngBroker> logger;
} 
```

Enter fullscreen mode Exit fullscreen mode

[日志消息使用模板](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/index#log-message-template)进行“语义日志记录”。注意，它使用了可选命名的位置占位符:

```
// OK
logger.LogInformation("{msg} {topic:x}", msg, topic);
logger.LogInformation("{Message} {Topic:x}", msg, topic);
logger.LogInformation("{} {:x}", msg, topic);
// Works, but confusing; output is actually $"{msg} {topic}"
logger.LogInformation("{topic} {msg:x}", msg, topic);
// BAD
logger.LogInformation("{0} {1:x}", msg, topic); 
```

Enter fullscreen mode Exit fullscreen mode

但是等等，还有更多:

*   日志记录函数有接受事件 ID 的[重载](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/indexlog-event-id)
*   [日志范围](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/index#log-scopes)
*   Xunit 提供者(运行测试时获得输出): [this SO](https://stackoverflow.com/questions/46169169/net-core-2-0-configurelogging-xunit-test) ， [this blog](https://www.neovolve.com/2018/06/01/ilogger-for-xunit/)

## Nng

我们一直在使用 [ZeroMQ](http://zeromq.org/) 进行交流。实际上， [NetMQ](https://github.com/zeromq/netmq/) 连同 [NetMQ。WebSockets](https://github.com/NetMQ/NetMQ.WebSockets) 用于 [WebSocket](https://en.wikipedia.org/wiki/WebSocket) 支持(用于我们基于 HTML5 的 UI)。

我们的覆盖和输入挂钩系统(C++)转移到了[nanosg](https://github.com/nanomsg/nanomsg)来支持 IPC/命名管道。我们还注意到它有对 WebSocket 的[“原生”支持，将是 ZeroMQ 的一个很好的替代品。我们做了一些从 C#访问 nanomsg 的初步调查；](https://nanomsg.org/v1.1.4/nn_ws.html)[我们分叉](https://github.com/zplus/NNanomsg) [NNanomsg](https://github.com/mhowlett/NNanomsg) 将其转换为。净标准。

显然[NNG(nanosg-下一代)](https://github.com/nanomsg/nng)正在被开发为 nanosg 的继任者(后者现在处于“维护模式”)。对于 C#，[我们分叉](https://github.com/zplus/csnng) [csnng](https://github.com/zplus/csnng) (再次，将其转换为。Net 标准并修复了一些问题)，但后来我们放弃了它，改用我们自己的 [nng。NETCore](https://github.com/subor/nng.NETCore) 。

下面是一个后台服务，它创建了一个简单的 NNG 代理。它也很好地说明了一切:

```
class NngConfig
{
    public string BrokerIn {get; set;}
    public string BrokerOut {get; set;}
}

public class NngBroker : BackgroundService
{
    public NngBroker(ILogger<NngBroker> logger, FactoryType factory, IConfiguration configuration)
        {
            this.logger = logger;
            this.factory = factory;

            config = new NngConfig();
            var nngSection = configuration.GetSection("zxy:nng");
            nngSection.Bind(config);

            logger.LogInformation("Broker started");
        }

    protected override async Task ExecuteAsync(CancellationToken token)
    {
        // Create simple nng broker that receives messages with puller and publishes them
        var pullSocket = factory.PullerCreate(config.BrokerIn, true).Unwrap();
        var input = pullSocket.CreateAsyncContext(factory).Unwrap();
        var output = factory.PublisherCreate(config.BrokerOut).Unwrap().CreateAsyncContext(factory).Unwrap();

        while (!token.IsCancellationRequested)
        {
            var msg = await input.Receive(token);
            logger.LogInformation("Broker: {} {:x}", msg, topic);
            if (!await output.Send(msg))
            {
                await Console.Error.WriteLineAsync("Failed!");
            }
        }
    }

    readonly ILogger<NngBroker> logger;
    readonly FactoryType factory;
    readonly NngConfig config;
} 
```

Enter fullscreen mode Exit fullscreen mode

其中`FactoryType`是通过 NNG 本地程序集创建 nng 资源[的依赖项(参见](///2018/09/09/native-assembly.html#one-load-context%20to-rule-them-all) [nng。NETCore 来源](https://github.com/subor/nng.NETCore/blob/master/nng.Shared/AssemblyLoadContext.cs):

```
[Export(typeof(ISingletonPlugin))]
public class NngSingletonPlugin : ISingletonInstancePlugin
{
    public Type ServiceType()
    {
        return typeof(FactoryType);
    }

    public object CreateInstance()
    {
        var path = Path.GetDirectoryName(GetType().Assembly.Location);
        var loadContext = new NngLoadContext(path);
        var factory = NngLoadContext.Init(loadContext);
        return factory;
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们已经有了一个新的[层 0](//./more-windows-service-in-c-3keg) 的良好开端:从插件加载的微服务，HTML5 UI 的 HTTP 服务器，配置和日志记录，通信层等。我们仍然需要研究如何简化[第 1 层](//./layer1-17dj)管理，我们是否使用节俭或其他方式进行序列化，以及其他一些关键方面，但已经有很多东西值得喜欢了。

微软几乎不是第一个 DI 解决方案，也很可能不是最好的，但它与 ASP.NET 核心合作得很好。NET 核心和微软生态系统的其余部分。无论如何，我认为它是我们内部微服务应用架构的绝佳替代品。
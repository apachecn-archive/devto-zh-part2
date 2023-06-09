# 使用 IHost。net 核心控制台应用程序

> 原文：<https://dev.to/gary_woodfine/using-ihost-net-core-console-applications-18hg>

控制台应用程序最常见的用途之一是构建所谓的无头服务，这在使用 Docker 容器和 Kubernetes (K8S)时尤其常见。

使用 headless 服务，可以创建一个不分配 IP 地址或转发流量的服务分组，使您能够明确控制您连接和通信的特定 pod。我们可以使用。net 核心控制台应用程序。net core 通用主机。

### 是什么。网络核心通用主机

无头服务的一个常见场景或用例是以一劳永逸的异步模式运行，这有助于后台处理任务，例如处理队列中的消息，这在基于云的本地容器架构中很常见。

通用主机的目标是将 HTTP 管道从 Web 主机 API 中分离出来，以支持更广泛的主机场景。通用主机是 ASP.NET 核心 2.1 中的新功能，不适合 web 托管，但非常适合用于控制台应用程序，这些应用程序通常用于开发无头服务。

通用主机库在 Microsoft 中可用。扩展。托管命名空间，由微软提供。扩展。托管包。

使用添加命名空间。net core CLI

```
dotnet add package Microsoft.Extensions.Hosting 
```

Enter fullscreen mode Exit fullscreen mode

### 介绍 IHost 和 HostBuilder

英寸 NET Core 2.1 通用主机使开发人员能够轻松地为非基于 web 的应用程序设置横切关注点，包括:

*   配置
*   记录
*   依赖注入

IHost 界面提供了许多优势和功能:

*   正常关机
*   依赖注入
*   记录
*   配置

当在 Docker 容器中开发复杂的数据处理任务时，这些特性尤其重要。尤其是正常关闭，因为它有助于保持应用程序状态的一致性。

### 实现 IHost in。net 核心控制台应用程序

IHost 接口和 HostBuilder 类分别为 IWebHost 和 WebHostBuilder 提供了类似的体验。net 核心网络开发人员熟悉时，使用 ASP.net 核心。

IHost 和 HostBuilder 是的新功能集的组件。NET Core 2.1，通过提供添加横切关注点(如依赖注入、配置和日志记录)的模式来简化基于控制台的服务的创建。

您需要记住，实现 IHost 接口的应用程序通常需要异步运行。这通常需要将 Main 方法标记为 async。

```
public static async Task Main(string[] args)
{
    var host = new HostBuilder()
        .Build();

    await host.RunAsync();
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经使用. net 新控制台生成了您的控制台应用程序，您可能需要更新您的项目文件以包含

```
<PropertyGroup>
    <LangVersion>latest</LangVersion>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup> 
```

Enter fullscreen mode Exit fullscreen mode

为了继续开发我们的应用程序，我们需要再添加几个包引用

```
 dotnet add package Microsoft.Extensions.Configuration.CommandLine
    dotnet add package Microsoft.Extensions.Configuration.EnvironmentVariables
    dotnet add package Microsoft.Extensions.Configuration.Json
    dotnet add package Microsoft.Extensions.Hosting
    dotnet add package Microsoft.Extensions.Hosting.Abstractions
    dotnet add package Microsoft.Extensions.Logging
    dotnet add package Microsoft.Extensions.Logging.Configuration
    dotnet add package Microsoft.Extensions.Logging.Console 
```

Enter fullscreen mode Exit fullscreen mode

HostBuilder 不提供允许使用启动类的扩展方法

HostingHostBuilder 提供了配置主机服务的扩展方法。以下分机可用:

*   ConfigureAppConfiguration–应用程序配置
*   Configure container–配置实例化的依赖注入容器
*   配置日志记录–配置日志记录
*   configure services–向依赖注入容器添加服务
*   runconsolesaync–启用控制台支持
*   UseConsoleLifetime–监听关机信号
*   UseContentRoot–指定内容根目录
*   使用环境–指定环境

RunConsoleAsync 将启动服务并等待应用程序中的退出信号。

### 建筑主机

使用 Main 方法创建一个 HostBuilder，并使用扩展方法向 DI 注册服务，读取配置并为您的应用程序配置日志记录。

为了配置主机，我们将使用 ConfigureHostConfiguration 来初始化 IHostingEnvironment，以便稍后在构建过程中使用。默认情况下不添加环境变量配置，所以我们必须调用。

在 host builder 上添加 EnvironmentVariables，以便通过环境变量配置主机。AddEnvironmentVariables 接受可选的用户定义的前缀，按照惯例使用您的应用程序名称在我们的示例中，我们已经声明了一个常量字符串值，用 _prefix 设置了应用程序名称。

使用 ConfigureAppConfiguration 配置提供程序为我们的应用程序构造配置值的最终表示形式。

在下面的例子中，我们首先从 appsettings.json 文件中读取配置值，然后是环境变量，最后是传递给应用程序的任何参数。

ConfigureServices 是我们配置希望应用程序运行的服务的地方，它向 ServiceCollection 注册服务。注册是使用 ServiceCollection 上的扩展方法执行的，一旦完成，就在我们的应用程序中启用 DI。

```
 class Program
    {
        private const string _prefix = "FUZZBIZZ_";
        private const string _appsettings = "appsettings.json";
        private const string _hostsettings = "hostsettings.json";
        public static async Task Main(string[] args)
        {
            var host = new HostBuilder()
                .ConfigureHostConfiguration(configHost =>
                {
                    configHost.SetBasePath(Directory.GetCurrentDirectory());
                    configHost.AddJsonFile(_hostsettings, optional: true);
                    configHost.AddEnvironmentVariables(prefix: _prefix);
                    configHost.AddCommandLine(args);
                })
                .ConfigureAppConfiguration((hostContext, configApp) =>
                {
                    configApp.SetBasePath(Directory.GetCurrentDirectory());
                    configApp.AddJsonFile(_appsettings, optional: true);
                    configApp.AddJsonFile(
                        $"appsettings.{hostContext.HostingEnvironment.EnvironmentName}.json",
                        optional: true);
                    configApp.AddEnvironmentVariables(prefix: _prefix);
                    configApp.AddCommandLine(args);
                })
                .ConfigureServices((hostContext, services) =>
                {
                    services.AddLogging();
                    services.Configure<Application>(hostContext.Configuration.GetSection("application"));
                    services.AddHostedService<FizzBuzzHostedService>();

                })
                .ConfigureLogging((hostContext, configLogging) =>
                {
                    configLogging.AddConsole();

                })
                .UseConsoleLifetime()
                .Build();

            await host.RunAsync();
        }

    } 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 IHostedService

服务的实际实现是使用 IHostedService 接口定义的。当一个应用程序启动时，它将调用 StartAsync 方法。

在关闭时，调用 StopAsync，服务在应用程序被终止之前清理一点。

使用 ConfigureServices 在 DI 容器中定义和注册 IHostedService 实现

通常在 Linux 守护进程中，您的服务实现将是一个单例类——在应用程序生命周期中只有一个服务实例。

它还将配置 POCO (Plain Old C# Object)添加到依赖注入服务中。这实现了中的 IOption 接口。NET Core，它可以接受一个类型，它将尝试根据字段名将 CLI 参数和环境变量绑定到该类型。

```
 using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    public class SampleService : IHostedService
    {
        public Task StartAsync(CancellationToken cancellationToken)
        {
            throw new System.NotImplementedException();
        }

        public Task StopAsync(CancellationToken cancellationToken)
        {
            throw new System.NotImplementedException();
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

带有一个或多个 IHostedServices 的控制台应用程序非常适合开发在 Docker 容器中运行的数据处理的无头服务。当容器关闭时，处理可以正常停止。

依赖注入为您提供了许多测试代码的选项，实现了关注点的分离。

通用主机引入了许多概念，这些概念对于简化微服务的开发至关重要。web 应用程序和服务的单一公共模式，以及对 DI、日志记录和配置的简单访问，是非常受欢迎的。

使用 IHost 的帖子[。net core 控制台应用程序](https://garywoodfine.com/ihost-net-core-console-applications/)最早出现在[加里·伍德费恩](https://garywoodfine.com)上。
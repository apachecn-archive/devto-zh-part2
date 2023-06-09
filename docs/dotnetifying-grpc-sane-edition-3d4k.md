# 网络化 gRPC: Sane 版

> 原文：<https://dev.to/joaofbantunes/dotnetifying-grpc-sane-edition-3d4k>

所以上次我玩 gRPC(并写了相关的文章)时，我有点疯狂地用不同的方式来实现和调用 gRPC 服务。NET(可以在这里看一下[)。虽然这是一个有趣的实验，我甚至可以看到它被使用，但现实是最好坚持更接近官方和支持的做事方式。考虑到这一点，这一次我走了完全相反的方向，所以它将更多地作为如何在. NET 应用程序中集成 gRPC 的指南。我在此基础上构建了一个库，但它真的非常简单，主要供我使用，因为其他人可能会避免添加额外的外部依赖项，因为没有太多的附加值。](https://dev.to/joaofbantunes/dotnetifying-grpc-5ni)

这个帖子的附赠代码是[这里是](https://github.com/CodingMilitia/GrpcExtensions/tree/may-blog-post)。

## 快速 gRPC 服务初始设置演练

也许这有点重复了我之前在 gRPC 上的帖子(以及互联网上的其他帖子)，但为了给这篇帖子的其余部分添加更多的上下文(当我不记得我做了什么时，我不必跳来跳去)，我将从如何开始 gRPC 服务的初始设置开始。

### 服务定义

要做的第一件事是定义服务，就像在实现 SOAP 服务之前创建一个 WSDL 一样(我知道，通常的方法是实现服务，然后获取生成的契约，但这不应该！)，在 gRPC 中，我们用服务定义——它的方法、输入和输出消息——创建一个原型文件(或更多)。

```
syntax = "proto3";

option csharp_namespace 
    = "CodingMilitia.GrpcExtensions.ScopedRequestHandlerSample.Generated";

service SampleService {
    rpc Send(SampleRequest) returns (SampleResponse) {}
}

message SampleRequest {
    int32 value = 1;
}

message SampleResponse {
    int32 value = 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，在这里我用方法`Send`定义了一个名为`SampleService`的服务，该方法接收一个`SampleRequest`参数并返回一个`SampleResponse`——这样的命名方式对吗？
这是一个一元方法定义，简单的请求响应。在 gRPC 中，我们有一些流选项，但是就像我说的，我现在保持它的简单，下次我将使用流。
最后，非常明显的是，`csharp_namespace`选项指明了生成的类所属的名称空间。

### 代码生成

服务定义完成后，我们需要生成在应用程序中使用的代码。代码生成工具与`Grpc.Tools` NuGet 包一起安装。有了工具，对于我描述的这个简单的例子，只需要一个命令就可以获得所需的客户机和基本服务器代码，

```
./protoc service.proto --csharp_out ./Generated/. --grpc_out ./Generated/. --plugin=protoc-gen-grpc=grpc_csharp_plugin 
```

Enter fullscreen mode Exit fullscreen mode

随着代码的生成，我们可以继续使用它了。

## 客户端

在客户端方面，我真的没有向库中添加任何东西，因为生成的库真的不需要太多配置。

为我们的示例生成的类之一是`SampleServiceClient`。为了在一个应用程序中使用它，例如一个 web 应用程序，我们只需要在如下的`Startup`类中配置它。

```
services.AddSingleton(_ =>
{
    var channel = new Channel(
        "127.0.0.1:5050", 
        ChannelCredentials.Insecure
    );
    return new SampleServiceClient(channel);
}); 
```

Enter fullscreen mode Exit fullscreen mode

当然，硬编码的 url 和凭证配置部分仅适用于示例目的。

## 服务器

因为服务器端是我真正感到需要创建一些东西来帮助托管服务的地方。在研究托管之前，让我们先来看看实现服务逻辑时需要考虑的一些问题。

### 实现服务

服务实现在很大程度上是相同的，因为我们必须从生成的基本服务类继承，在本例中是`SampleServiceBase`。现在，在从生成的类继承的类中直接实现我们的逻辑的问题是，它将作为单例类存在，所以如果我们想要有范围依赖，我们需要将依赖注入的东西引入到类中，使它更像一个服务定位器，这不是很好。

因此，当我们应该关注服务的逻辑时，为了减少摆弄 DI 的需要，最好的方法可能是在另一个类中实现服务逻辑，这在最简单的情况下(就像我们作为示例使用的这个一元方法的情况)甚至不需要知道任何关于 gRPC 的事情。

```
public class RandomSampleServiceLogic : ISampleServiceLogic
{
    private static readonly Random RandomSource = new Random();
    private readonly ILogger<RandomSampleServiceLogic> _logger;

    public RandomSampleServiceLogic(
        ILogger<RandomSampleServiceLogic> logger
    )
    {
        _logger = logger;
    }
    public async Task<SampleResponse> SendAsync(
        SampleRequest request, 
        CancellationToken ct
    )
    {
        _logger.LogInformation(
            "Received request with value {requestValue}",
            request.Value
        );

        _logger.LogInformation(
            "Simulating slow operation with a delay for request value {requestValue}", 
            request.Value
        );

        await Task.Delay(1000, ct);

        var response = new SampleResponse
        {
            Value = request.Value + RandomSource.Next()
        };

        _logger.LogInformation(
            "Random response to request with value {requestValue} will be {responseValue}",
            request.Value,
            response.Value
        );

        return response;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，`CancellationToken`是作为参数传递给`SendAsync`方法的，因为在这个简单的例子中，它完全不知道自己是 gRPC 服务的一部分，如果需要的话，它可以作为 REST API 或任何其他类型的 API 的逻辑提供者。

那么要托管的服务实现可能如下所示。

```
public class AnotherSampleServiceImplementation : SampleServiceBase
{
    private readonly IServiceScopeFactory _scopeFactory;

    public AnotherSampleServiceImplementation(
        IServiceScopeFactory scopeFactory
    )
    {
        _scopeFactory = scopeFactory;
    }

    public override async Task<Generated.SampleResponse> Send(
        Generated.SampleRequest request, 
        ServerCallContext context
    )
    {
        using (var scope = _scopeFactory.CreateScope())
        {
            var service = scope.ServiceProvider
                .GetRequiredService<ISampleServiceLogic>();

            var response = await service.SendAsync(
                request.ToInternalRequest(), 
                context.CancellationToken
            );

            return response.ToExternalResponse();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

或者，我创建了一个`IScopedExecutor`可以用来抽象这个，但实际上没有那么多的必要。

```
public class SampleServiceImplementation : SampleServiceBase
{
    private readonly IScopedExecutor _scopedExecutor;

    public SampleServiceImplementation(
        IScopedExecutor scopedExecutor
    )
    {
        _scopedExecutor = scopedExecutor;
    }

    public override async Task<Generated.SampleResponse> Send(
        Generated.SampleRequest request, 
        ServerCallContext context
    )
    {
        return await _scopedExecutor
            .ExecuteAsync<ISampleServiceLogic, Generated.SampleResponse>(
                async (service) =>
                {
                    var response = await service.SendAsync(
                        request.ToInternalRequest(), 
                        context.CancellationToken);

                    return response.ToExternalResponse();
                });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 托管

现在我们已经实现了服务，我们可以托管它了。正如我提到的，这是我真的觉得我可以把事情简单化的地方。

首先，如何托管服务？更简单的方法可能是实例化`Server`类，配置它并调用它的`Start`方法。这种方法的问题是，如果我们想使用 DI，我们需要手工设置它。利用已经为我们完成的工作，一个很好的替代方案是实现一个后台服务，使用`Microsoft.Extensions.Hosting`提供的`IHostedService`接口，然后用`HostBuilder`注册它(很像我们在 ASP.NET 核心应用程序中使用的常用`WebHostBuilder`)。

考虑到这一点，我从实现`GrpcBackgroundService`类开始。这是一个非常简单的类，在构造函数中接收要托管的`Server`实例，所以即使我们想要在多个`Server`中托管多个服务(我们也可以在单个`Server`中托管多个服务)，也只需要一个`GrpcBackgroundService`实例。

```
internal class GrpcBackgroundService : IHostedService
{
    private readonly IEnumerable<Server> _servers;
    private readonly ILogger<GrpcBackgroundService> _logger;

    public GrpcBackgroundService(
        IEnumerable<Server> servers, 
        ILogger<GrpcBackgroundService> logger
    )
    {
        _servers = servers;
        _logger = logger;
    }

    public Task StartAsync(CancellationToken cancellationToken)
    {
        _logger.LogDebug("Starting gRPC background service");

        foreach(var server in _servers)
        {
            StartServer(server);
        }

        _logger.LogDebug("gRPC background service started");

        return Task.CompletedTask;
    }

    public async Task StopAsync(CancellationToken cancellationToken)
    {
        _logger.LogDebug("Stopping gRPC background service");

        var shutdownTasks = _servers
            .Select(server => server.ShutdownAsync()).ToList();

        await Task.WhenAll(shutdownTasks).ConfigureAwait(false);

        _logger.LogDebug("gRPC background service stopped");
    }

    private void StartServer(Server server)
    {
        _logger.LogDebug(
            "Starting gRPC server listening on: {hostingEndpoints}",
            string.Join(
                "; ", 
                server.Ports.Select(p => $"{p.Host}:{p.BoundPort}")
            )
        );

        server.Start();

        _logger.LogDebug(
            "Started gRPC server listening on: {hostingEndpoints}",
            string.Join(
                "; ", 
                server.Ports.Select(p => $"{p.Host}:{p.BoundPort}")
            )
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后为了帮助注册服务，我为`IServiceCollection`接口创建了一些扩展方法。下面我只包括了我认为对这个场景最感兴趣的那些，因为其他的只是注册提供的`Server`到 DI 以及`GrpcBackgroundService`。

```
//...

public static IServiceCollection AddGrpcServer<TService>(
    this IServiceCollection serviceCollection,
    IEnumerable<ServerPort> ports,
    IEnumerable<ChannelOption> channelOptions = null
)
    where TService : class
{
    return serviceCollection.AddGrpcServer(
            serverConfigurator: 
                configurator => configurator.AddService<TService>(),
            ports: ports,
            channelOptions: channelOptions
        );
}

public static IServiceCollection AddGrpcServer(
    this IServiceCollection serviceCollection,
    Action<IGrpcServerBuilder> serverConfigurator,
    IEnumerable<ServerPort> ports,
    IEnumerable<ChannelOption> channelOptions = null
)
{
    if (serviceCollection == null)
    {
        throw new ArgumentNullException(nameof(serviceCollection));
    }

    if (serverConfigurator == null)
    {
        throw new ArgumentNullException(nameof(serverConfigurator));
    }

    if (ports == null)
    {
        throw new ArgumentNullException(nameof(ports));
    }

    if (!ports.Any())
    {
        throw new ArgumentException(
            message: "At least one port must be specified", 
            paramName: nameof(ports)
        );
    }

    var builder = new GrpcServerBuilder(
        serviceCollection, 
        ports, 
        channelOptions
    );
    serverConfigurator(builder);
    builder.AddServerToServiceCollection();
    serviceCollection
        .AddGrpcBackgroundServiceIfNotAlreadyRegistered();

    return serviceCollection;
}

//... 
```

Enter fullscreen mode Exit fullscreen mode

大部分实现都在我展示的第二个版本的`AddGrpcServer`方法中，所以第一个只是使用第二个作为基础的简化版本。

关于方法签名，第一个是注册单个服务的版本(它是作为一般参数传递的`TService`，使用提供的端口和通道选项来配置`Server`。第二个版本允许在同一个`Server`中注册多个服务，使用一个`Action<IGrpcServerBuilder> serverConfigurator`作为目标参数。`IGrpcServerBuilder`接口公开了一个`AddService`方法，允许注册多个期望的服务。
有了所有需要的依赖项，就创建了一个`IGrpcServerBuilder`实现(`GrpcServerBuilder`)的实例，并负责构建一个由应用程序托管的`Server`。

```
internal class GrpcServerBuilder : IGrpcServerBuilder
{
    private readonly IServiceCollection _serviceCollection;
    private readonly IEnumerable<ServerPort> _ports;
    private readonly IEnumerable<ChannelOption> _channelOptions;
    private readonly List<ServiceRegistrationInfo> _registrationInfo;
    private bool _built;

    public GrpcServerBuilder(
        IServiceCollection serviceCollection, 
        IEnumerable<ServerPort> ports,
        IEnumerable<ChannelOption> channelOptions
    )
    {
        if (ports == null)
        {
            throw new ArgumentNullException(nameof(ports));
        }

        if (!ports.Any())
        {
            throw new ArgumentException(
                message: "At least one port must be specified", 
                paramName: nameof(ports)
            );
        }

        _serviceCollection = serviceCollection;
        _ports = ports;
        _channelOptions = 
            channelOptions ?? Array.Empty<ChannelOption>();
        _registrationInfo = new List<ServiceRegistrationInfo>();
        _built = false;

    }

    public IGrpcServerBuilder AddService<TService>() 
        where TService : class
    {
        ThrowIfServerAlreadyBuilt();

        var serviceType = typeof(TService);
        if (
            _serviceCollection.Any(
                s => s.ServiceType.Equals(serviceType)
            ) 
            || 
            _registrationInfo.Any(
                s => s.ServiceType.Equals(serviceType)
            )
        )
        {
            throw new InvalidOperationException(
                $"{typeof(TService).Name} is already registered in the container."
            );
        }

        _serviceCollection.AddSingleton<TService>();
        var serviceBinder = ServerBuildHelpers.GetServiceBinder<TService>();

        //Storing a lambda to use later, because this avoids 
        //reflection tricks later when we don't have access 
        //to the TService type so easily to invoke the binder.

        //Also, not invoking it immediately to keep it lazy.

        _registrationInfo.Add(
            new ServiceRegistrationInfo(
                serviceType, 
                appServices 
                    => serviceBinder(appServices.GetRequiredService<TService>())
            )
        );

        return this;
    }

    public void AddServerToServiceCollection()
    {
        ThrowIfServerAlreadyBuilt();

        if (_registrationInfo.Count == 0)
        {
            throw new InvalidOperationException(
                "Must add at least one service for a server to be created."
            );
        }

        _serviceCollection.AddSingleton(appServices =>
        {
            var server = _channelOptions.Count() > 0 
                ? new Server(_channelOptions) 
                : new Server();

            server.AddPorts(_ports);

            foreach (var serviceDefinition in _registrationInfo)
            {
                server.AddServices(
                    serviceDefinition
                        .ServiceDefinitionProvider(appServices)
                );
            }
            return server;
        });

        _built = true;
    }

    private void ThrowIfServerAlreadyBuilt()
    {
        if (_built)
        {
            throw new InvalidOperationException(
                "Server already build."
            );
        }
    }

    private class ServiceRegistrationInfo
    {
        public Type ServiceType { get; }
        public Func<IServiceProvider, ServerServiceDefinition> 
            ServiceDefinitionProvider { get; }

        public ServiceRegistrationInfo(
            Type serviceType, 
            Func<IServiceProvider, ServerServiceDefinition> 
                serviceDefinitionProvider
        )
        {
            ServiceType = serviceType;
            ServiceDefinitionProvider = serviceDefinitionProvider;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`AddService`方法进行一些初始有效性检查，在 DI 容器中注册服务，并存储一些信息，以便在最终构建服务时使用。构建`Server`时使用的信息中最重要的部分是我称之为`serviceBinder`的部分，它用于将服务实现绑定到注册到`Server`的`ServerServiceDefinition`。我们可以在生成的名为`BindService`的`SampleService`类中看到这个方法。最初，我将这个方法作为参数传递给`AddGrpcServer`方法，但是接下来，我创建了一个助手来获取这个给定的服务实现类。

```
//Using reflection tricks and assumptions on the way 
//the core gRPC lib works so, if Google changes this, 
//it'll break and only be noticed at runtime :)
public static Func<TService, ServerServiceDefinition> GetServiceBinder<TService>()
{
    var serviceType = typeof(TService);
    var baseServiceType = GetBaseType(serviceType);
    var serviceDefinitionType = typeof(ServerServiceDefinition);

    var serviceContainerType = baseServiceType.DeclaringType;
    var methods = serviceContainerType
        .GetMethods(BindingFlags.Public | BindingFlags.Static);

    var binder =
        (from m in methods
            let parameters = m.GetParameters()
            where m.Name.Equals("BindService")
                && parameters.Length == 1
                && parameters.First()
                    .ParameterType.Equals(baseServiceType)
                && m.ReturnType.Equals(serviceDefinitionType)
            select m)
    .SingleOrDefault();

    if (binder == null)
    {
        throw new InvalidOperationException(
            $"Could not find service binder for provided service {serviceType.Name}"
        );
    }

    var serviceParameter = Expression.Parameter(serviceType);

    var invocation = Expression.Call(
        null, 
        binder, 
        new[] { serviceParameter }
    );

    var func = Expression
        .Lambda<Func<TService, ServerServiceDefinition>>(
            invocation, 
            false, 
            new[] { serviceParameter }
        ).Compile();

    return func;
} 
```

Enter fullscreen mode Exit fullscreen mode

这使用反射来遍历服务类的类层次结构，以找到 binder 方法。认为这在 gRPC 库的未来版本中不会改变可能有点冒险，但目前它工作得足够好，并简化了服务器配置。

回到`GrpcServerBuilder`，当所有的服务都注册后，通过调用`AddServerToServiceCollection`，构建`Server`并将其添加到 DI 服务集合中。

结束`AddGrpcServer`方法后，`GrpcBackgroundService`被添加到服务集合中(仅一次),这样它就可以获得注册的`Server`并托管它们。

添加到 DI 中的所有东西都使用了 singleton 范围，因为我们将只有一个后台服务在运行，并且一个(或多个)`Server`一直在监听请求。由于`Server`要求服务实例在启动时注册，所以服务也被注册为 singleton。正如我前面提到的关于实现服务，正是服务负责照顾它的依赖关系的生命周期，因此我提出了建议。

## 配置应用

现在要配置应用程序，只需要创建一个`HostBuilder`并使用前面描述的扩展来配置服务。

```
class Program
{
    static async Task Main(string[] args)
    {
        var serverHostBuilder = new HostBuilder()
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            //...
        })
        .ConfigureLogging((context, logging) =>
        {
            //...
        })
        .ConfigureServices((hostContext, services) =>
        {
            services
            .AddScoped<
                ISampleServiceLogic, 
                RandomSampleServiceLogic
            >()
            .AddScopedExecutor()
            //the most "magic" solution
            .AddGrpcServer<SampleServiceImplementation>(
                new[] { 
                    new ServerPort(
                        "127.0.0.1", 
                        5050, 
                        ServerCredentials.Insecur
                    e) 
                }
            )
            //a more manual solution if the flexibility is required
            //also not using the IScopedExecutor (although it could) 
            //for a more traditional example
            .AddGrpcServer(appServices =>
            {
                var scopeFactory 
                    = appServices.GetRequiredService<
                        IServiceScopeFactory
                    >();
                var server = new Grpc.Core.Server
                {
                    Services = { 
                        SampleService.BindService(
                            new AnotherSampleServiceImplementation(
                                scopeFactory
                            )
                        ) 
                    },
                    Ports = { 
                        new ServerPort(
                            "127.0.0.1", 
                            5051, 
                            ServerCredentials.Insecure
                        ) 
                    }
                };
                return server;
            });
        });

        await serverHostBuilder.RunConsoleAsync();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了开始注册服务，添加了作为作用域的`ISampleServiceLogic`,然后注册了我前面提到的`IScopedExecutor`。

然后，对于这个示例，我使用两个不同的扩展来注册 gRPC 服务实现，因此我们可以看到可用的可能性:
在第一种情况下，最容易使用的解决方案，提供实现服务及其配置的类，允许扩展方法处理剩余的工作。

在第二个例子中，我正在传递一个返回`Server`的工厂。在这种情况下，扩展方法唯一的职责是将工厂传递给 DI 并注册`GrpcBackgroundService`。

配置好一切之后，我们就可以通过调用`HostBuilder`上的`RunConsoleAsync`来开始托管服务了。

## 包装完毕

正如我在开始时提到的，我创建的助手库并不是非常复杂，有些东西可能是不必要的，因此，当我们希望以尽可能类似于 ASP.NET 核心应用程序的方式托管 gRPC 服务时，需要注意以下几点:

*   实现 IHostedService 来托管`Server`
*   请注意，依赖注入并没有嵌入到 gRPC 核心库中，所以要正确使用它，还需要一些额外的限制

关于这个库，它在 GitHub 上，有一天我可能会用它创建一个 NuGet 包，即使只是为了我自己的使用。

过去有迹象表明在 ASP.NET 核心中支持 gRPC 开箱即用(像这里的这个，或[这个](https://github.com/grpc/grpc/issues/15139))，这将简化很多，但我们还没有到那一步。

任何关于改进代码(或文章)的建议请分享。

感谢你通读，cyaz！

PS:原贴[此处](https://blog.codingmilitia.com/2018/05/19/dotnetifying-grpc-sane-edition)
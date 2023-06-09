# 网络化 gRPC

> 原文：<https://dev.to/joaofbantunes/dotnetifying-grpc-5ni>

我开始这个小小的概念验证的简单目标是创建一些助手函数来帮助在中托管 gRPC 服务。NET 的方式类似于 ASP.NET 应用程序。然而，当我在网上搜索、阅读一些文章试图找到最好的方法时，我看到了这篇[文章](http://bartoszsypytkowski.com/c-using-grpc-with-custom-serializers/)，并意识到我可以变得更有创造力一点——也许我最终变得太多了，但至少我一路上都很开心:)。

我会试着涵盖我在这里所做的主要部分，但是如果你发现这里没有的对了解整体情况很重要的东西，请告诉我，我会努力改进解释。

您可以按照 [GitHub](https://github.com/CodingMilitia/Grpc/tree/april-blog-post) 上的代码进行操作(我创建了一个标签，以便于将本文与代码联系起来)。

抓住一些爆米花，这是一个很长的帖子！

## 这是什么“dotnetifying”？

所以，就像我说的，最初的目标只是让托管服务有点类似于 ASP.NET 应用程序。除了托管之外，如果服务客户端注册也可以简化，那也是很好的(而且可能是我开始变得太有创造力之前最容易的部分)。

这些是最初的目标。在阅读了上述文章后，我开始想知道，我可能也可以使服务实现体验不同，首先从实体框架代码中获得灵感，也就是说，不是使用 gRPC 时所需的通常的代码生成，而是可以只创建服务接口、用作请求和响应的消息、创建服务实现并使用它，甚至不需要接触. proto 文件和其他通常的工具——只要您不需要与其他语言互操作，在这种情况下您最好编写它。原型文件，以便其他人可以生成他们的东西。

## 我不想做什么

通过这个概念验证，我并没有试图抽象出核心 gRPC 库，而是在需要时使用它们并公开它们。因此，即使使用这些代码来简化这些服务的开发，也需要底层核心库的知识。

## 通常的方式

在谈论我想要实现的目标之前，最好先简单介绍一下用 gRPC 以正常方式创建一个非常简单的服务。

我们首先创建一个包含服务描述的. proto 文件。

```
syntax = "proto3";

option csharp_namespace = "CodingMilitia.Grpc.GeneratedServerInterop.Generated";

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

然后，我们使用随 Grpc 安装的工具。工具 NuGet 包来生成 C#代码。

```
./protoc service.proto --csharp_out ./Generated/. --grpc_out ./Generated/. --plugin=protoc-gen-grpc=grpc_csharp_plugin 
```

Enter fullscreen mode Exit fullscreen mode

有了生成的代码，我们可以使用生成的客户端(类`SampleServiceClient`)来调用已经运行的服务，或者从生成的服务基础(类`SampleServiceBase`)继承来实现服务器端。

```
//server
 var server = new Server
{
    Services = { Generated.SampleService.BindService(new SampleServiceImplementation()) },
    Ports = { new ServerPort("127.0.0.1", 5000, ServerCredentials.Insecure) }
};
server.Start(); 
```

Enter fullscreen mode Exit fullscreen mode

```
//client
var channel = new Channel("127.0.0.1:5000", ChannelCredentials.Insecure);
var client = new Generated.SampleService.SampleServiceClient(channel);
var request = new Generated.SampleRequest { Value = 1 };
var response = await client.SendAsync(request); 
```

Enter fullscreen mode Exit fullscreen mode

正如我在开始时所说的，我最初的目标只是用一些用于 DI 的辅助方法来包装它，并简化托管，但是后来，我变得不守规矩了...

## 期望的结果

只是为了提供托管和消费服务方面的期望结果的一瞥，这里有一些一厢情愿的代码(实际上是有效的)。

### 在客户端和服务器之间共享

```
[GrpcService("SampleService")]
public interface ISampleService : IGrpcService
{
    [GrpcMethod("Send")]
    Task<SampleResponse> SendAsync(SampleRequest request, CancellationToken ct);
}

[ProtoBuf.ProtoContract]
public class SampleRequest
{
    [ProtoBuf.ProtoMember(1)]
    public int Value { get; set; }
}

[ProtoBuf.ProtoContract]
public class SampleResponse
{
    [ProtoBuf.ProtoMember(1)]
    public int Value { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

消息上面的属性取决于所使用的序列化程序。我使用 [protobuf-net](https://github.com/mgravell/protobuf-net) 来实现序列化器，因此使用了属性。

### 服务器

```
var serverHostBuilder = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.AddGrpcServer<ISampleService, SampleService>(new GrpcServerOptions { Url = "127.0.0.1", Port = 5000 });
    });

await serverHostBuilder.RunConsoleAsync(cts.Token); 
```

Enter fullscreen mode Exit fullscreen mode

### 客户端

```
//in a normal scenario we wouldn't instantiate the ServiceCollection, it's just for demo purposes
var clientServices = new ServiceCollection()
    .AddGrpcClient<ISampleService>(new GrpcClientOptions { Url = "127.0.0.1", Port = 5000 })
    .BuildServiceProvider();
var client = clientServices.GetRequiredService<ISampleService>();
var request = new SampleRequest { Value = 1 };
var response = await client.SendAsync(request, CancellationToken.None); 
```

Enter fullscreen mode Exit fullscreen mode

## 实现它

所以，就像我之前说的，我最后的灵感来自 Horusiath 的[文章。简而言之，他利用生成的代码所使用的核心 gRPC 库类来创建服务和客户端类，而不需要生成任何代码——此外，他使用定制的协议缓冲区序列化程序，而不是通常生成的序列化程序。](http://bartoszsypytkowski.com/c-using-grpc-with-custom-serializers/)

### 这一切的基础

所以，从客户机和服务器的共同点开始。

gRPC 服务由方法组成。有 4 种类型的方法:一元，服务器流，客户端流和双工流-对于这个 PoC，我只实现了最基本的类型，一元，一个简单的请求响应。

为了创建方法定义，我构建了一个助手类`MethodDefinitionGenerator`(我的命名技巧有点弱，很抱歉)来公开一个方法`CreateMethodDefinition`，它返回一个`Method<TRequest, TResponse>`——这个类是核心 gRPC 库的一部分。对于每个服务方法，都需要一个方法类型(现在总是为`Unary`)、服务和方法名称，以及消息的序列化器。序列化器是可配置的，因为我使用第三方库来实现它，所以我认为最好保持它易于更改。

```
using System;
using CodingMilitia.Grpc.Serializers;
using Grpc.Core;

namespace CodingMilitia.Grpc.Shared.Internal
{
    //TODO: review visibility
    public static class MethodDefinitionGenerator
    {
        public static Method<TRequest, TResponse> CreateMethodDefinition<TRequest, TResponse>(
            MethodType methodType,
            string serviceName,
            string methodName,
            ISerializer serializer
        )
            where TRequest : class
            where TResponse : class
        {
            return new Method<TRequest, TResponse>(
                type: methodType,
                serviceName: serviceName,
                name: methodName,
                requestMarshaller: Marshallers.Create(
                    serializer: serializer.ToBytes<TRequest>,
                    deserializer: serializer.FromBytes<TRequest>
                ),
                responseMarshaller: Marshallers.Create(
                    serializer: serializer.ToBytes<TResponse>,
                    deserializer: serializer.FromBytes<TResponse>
                )
            );
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`ISerializer`界面是有史以来最简单的东西(但是在阅读了[这篇文章](https://dev.to/scotthannen/depending-on-functions-instead-of-interfaces---why-and-how-50o6)之后，我可能会改变方法)。它只是公开了一些方法来序列化和反序列化一个通用类型`T`和一个`byte[]`。

```
namespace CodingMilitia.Grpc.Serializers
{
    public interface ISerializer
    {
         byte[] ToBytes<T>(T input);
         T FromBytes<T>(byte[] input);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

除此之外，还有几个属性应用于服务接口和方法，现在只配置将在方法定义中使用的名称。

```
using System;

namespace CodingMilitia.Grpc.Shared.Attributes
{
    [AttributeUsage(AttributeTargets.Interface)] 
    public class GrpcServiceAttribute : Attribute
    {
        public string Name { get; set; }

        public GrpcServiceAttribute(string name)
        {
            Name = name;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
using System;

namespace CodingMilitia.Grpc.Shared.Attributes
{
    [AttributeUsage(AttributeTargets.Method)] 
    public class GrpcMethodAttribute : Attribute
    {
        public string Name { get; set; }

        public GrpcMethodAttribute(string name)
        {
            Name = name;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 服务器

对于服务器端，我最初认为我必须实现一些托管助手，以类似于 ASP.NET 核心的方式将服务托管在控制台应用程序中。然后我意识到这已经在。NET Core 2.1(目前在 preview 1 中)，这将避免大量的工作。因此，利用新的托管选项，非常简单的类`GrpcBackgroundService`实现了`IHostedService`，并简单地启动和停止了一个`GrpcHost`实例。现在的`GrpcHost`，是核心 gRPC 库的`Server`类的一个抽象，处理服务的托管。

```
using CodingMilitia.Grpc.Shared;
using Microsoft.Extensions.Hosting;
using System.Threading;
using System.Threading.Tasks;

namespace CodingMilitia.Grpc.Server.Internal
{
    internal class GrpcBackgroundService<TService> : IHostedService where TService : class, IGrpcService
    {
        private readonly GrpcHost<TService> _host;

        public GrpcBackgroundService(GrpcHost<TService> host)
        {
            _host = host;
        }

        public async Task StartAsync(CancellationToken cancellationToken)
        {
            await _host.StartAsync().ConfigureAwait(false);
        }

        public async Task StopAsync(CancellationToken cancellationToken)
        {
            await _host.StopAsync().ConfigureAwait(false);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
using CodingMilitia.Grpc.Shared;
using System.Threading.Tasks;
using GrpcCore = Grpc.Core;

namespace CodingMilitia.Grpc.Server.Internal
{
    internal class GrpcHost<TService> where TService : class, IGrpcService
    {
        private readonly GrpcCore.Server _server;

        public GrpcHost(GrpcCore.Server server)
        {
            _server = server;
        }

        public Task StartAsync()
        {
            _server.Start();
            return Task.CompletedTask;
        }

        public async Task StopAsync()
        {
            await _server.ShutdownAsync().ConfigureAwait(false);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了获得我们之前在“期望的结果”部分看到的用 DI 注册的简单服务实现，在`IServiceCollection`上有一个扩展方法，它将`GrpcBackgroundService`和`GrpcHost`注册为单例，并将提供的服务实现注册为作用域，以实现类似 ASP.NET MVC 控制器的行为——每个请求一个实例。

```
using CodingMilitia.Grpc.Serializers;
using CodingMilitia.Grpc.Server.Internal;
using CodingMilitia.Grpc.Shared;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

namespace CodingMilitia.Grpc.Server
{
    public static class ServiceCollectionExtensions
    {
        public static IServiceCollection AddGrpcServer<TServiceInterface, TServiceImplementation>(
            this IServiceCollection serviceCollection,
            GrpcServerOptions options,
            ISerializer serializer
        )
            where TServiceInterface : class, IGrpcService
            where TServiceImplementation : class, IGrpcService, TServiceInterface
        {
            serviceCollection.AddScoped<TServiceInterface, TServiceImplementation>();
            serviceCollection.AddSingleton<GrpcHost<TServiceInterface>>(
                appServices => GrpcHostFactory.Create<TServiceInterface>(appServices, options, serializer)
            );

            serviceCollection.AddSingleton<IHostedService, GrpcBackgroundService<TServiceInterface>>();
            return serviceCollection;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

从上面可以看出，除了已经提到的，还有一个对负责创建所需的`GrpcHost`的`GrpcHostFactory.Create`的调用，这里是服务器的大部分代码优先的地方。

```
using CodingMilitia.Grpc.Serializers;
using CodingMilitia.Grpc.Shared;
using CodingMilitia.Grpc.Shared.Attributes;
using System;
using System.Reflection;

namespace CodingMilitia.Grpc.Server.Internal
{
    internal static class GrpcHostFactory
    {
        public static GrpcHost<TService> Create<TService>(
            IServiceProvider appServices,
            GrpcServerOptions options,
            ISerializer serializer
        )
            where TService : class, IGrpcService
        {
            var builder = new GrpcHostBuilder<TService>(appServices);
            builder.SetOptions(options);
            builder.SetSerializer(serializer);
            builder.AddUnaryMethods();
            return builder.Build();
        }

        private static GrpcHostBuilder<TService> AddUnaryMethods<TService>(
            this GrpcHostBuilder<TService> builder
        )
            where TService : class, IGrpcService
        {
            //TODO: right now it goes through every method, these must be validated and filtered
            var serviceType = typeof(TService);
            var serviceName = ((GrpcServiceAttribute)serviceType.GetCustomAttribute(typeof(GrpcServiceAttribute))).Name ?? serviceType.Name;

            foreach (var method in serviceType.GetMethods())
            {
                var requestType = method.GetParameters()[0].ParameterType;
                var responseType = method.ReturnType.GenericTypeArguments[0];

                var handlerGenerator = typeof(MethodHandlerGenerator).GetMethod(nameof(MethodHandlerGenerator.GenerateUnaryMethodHandler));
                handlerGenerator = handlerGenerator.MakeGenericMethod(serviceType, requestType, responseType);
                var handler = handlerGenerator.Invoke(null, new[] { method });

                var addUnaryMethod = typeof(GrpcHostBuilder<TService>).GetMethod(nameof(GrpcHostBuilder<TService>.AddUnaryMethod), BindingFlags.Public | BindingFlags.Instance);
                addUnaryMethod = addUnaryMethod.MakeGenericMethod(requestType, responseType);

                var methodName = ((GrpcMethodAttribute)method.GetCustomAttribute(typeof(GrpcMethodAttribute))).Name ?? method.Name;

                addUnaryMethod.Invoke(builder, new[] { handler, serviceName, methodName });
            }

            return builder;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，这是恶作剧的开始——并不是说它超级难，而是比平常有点棘手，主要是因为我们进入了反射区。开始很简单，在一个`GrpcHostBuilder`实例上设置选项和序列化程序。之后，它开始迭代所有的服务接口方法，假设它们都是一元方法，不执行任何验证(是的，这确实需要改进)。

对于每个方法，它使用`MethodHandlerGenerator`类创建一个处理程序。处理程序基本上是一个`Func`，它使用提供给它的实例包装对服务实现方法的调用。然后，获得的处理程序作为参数传递给构建器的`AddUnaryMethod`方法。

`GrpcHostBuilder`与核心 gRPC 库`ServerServiceDefinition.Builder`一起创建`Server`。

```
using CodingMilitia.Grpc.Serializers;
using CodingMilitia.Grpc.Shared;
using CodingMilitia.Grpc.Shared.Internal;
using Grpc.Core;
using Microsoft.Extensions.DependencyInjection;
using System;
using System.Threading;
using System.Threading.Tasks;

namespace CodingMilitia.Grpc.Server.Internal
{
    internal class GrpcHostBuilder<TService> where TService : class, IGrpcService
    {
        private readonly IServiceProvider _appServices;
        private readonly ServerServiceDefinition.Builder _builder;
        private GrpcServerOptions _options;
        private ISerializer _serializer;

        public GrpcHostBuilder(IServiceProvider appServices)
        {
            _appServices = appServices;
            _builder = ServerServiceDefinition.CreateBuilder();
        }

        public GrpcHostBuilder<TService> SetOptions(GrpcServerOptions options)
        {
            _options = options;
            return this;
        }

        public GrpcHostBuilder<TService> SetSerializer(ISerializer serializer)
        {
            _serializer = serializer;
            return this;
        }

        public GrpcHost<TService> Build()
        {
            var server = new global::Grpc.Core.Server
            {
                Ports = { { _options.Url, _options.Port, ServerCredentials.Insecure } },
                Services =
                {
                    _builder.Build()
                }
            };
            return new GrpcHost<TService>(server);
        }

        public GrpcHostBuilder<TService> AddUnaryMethod<TRequest, TResponse>(
            Func<TService, TRequest, CancellationToken, Task<TResponse>> handler,
            string serviceName,
            string methodName
        )
            where TRequest : class
            where TResponse : class
        {
            _builder.AddMethod(
                MethodDefinitionGenerator.CreateMethodDefinition<TRequest, TResponse>(MethodType.Unary, serviceName, methodName, _serializer),
                async (request, context) =>
                {
                    using (var scope = _appServices.CreateScope())
                    {
                        var service = scope.ServiceProvider.GetRequiredService<TService>();
                        var baseService = service as GrpcServiceBase;
                        if (baseService != null)
                        {
                            baseService.Context = context;
                        }
                        return await handler(service, request, context.CancellationToken).ConfigureAwait(false);
                    }
                }
            );
            return this;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在构建器构造函数中，我们得到一个`IServiceProvider`,这样我们就可以为每个请求获取一个服务实现。SetOptions 和 SetSerializer 方法非常简单明了，Build 方法也是如此，它基本上只是使用提供的参数和`ServerServiceDefinition.Builder`最终产品来创建最终的`Server`实例。

`AddUnaryMethod`方法调用内部构建器 AddMethod，提供方法定义(用前面讨论的`MethodDefinitionGenerator`创建)和一个 lambda 来处理请求。lambda 用`IServiceProvider`创建一个新的作用域，获取一个新的服务实例，并使用处理程序调用所需的方法。如果服务扩展了`GrpcServiceBase`，那么`ServiceCallContext`就会被设置，就像 MVC `Controller`类中的`HttpContext`一样。

还在我身边吗？不错！向前到客户端的东西和一些额外的疯狂。

### 客户端

在客户端，我们基本上需要生成一个代理类，有点像我们在 WCF 生成的 SOAP 服务。开发时间生成基本上就是 gRPC 的工具所做的事情，但是作为一个聪明人，我是在运行时做的(因为，你知道，原因)。

这绝对是这个 PoC 中最复杂的部分，但至少我可以玩一些我以前没有的东西——IL 发射！

就像关于服务器的东西一样，让我从外部开始，逐渐深入到更晦涩的东西。

就像服务器端一样，我在`IServiceCollection`上创建了一个扩展方法，将客户端注册为单例。通过调用`GrpcClientFactory.Create`获得客户端。

```
using CodingMilitia.Grpc.Serializers;
using CodingMilitia.Grpc.Shared;
using Microsoft.Extensions.DependencyInjection;

namespace CodingMilitia.Grpc.Client
{
    public static class ServiceCollectionExtensions
    {
        public static IServiceCollection AddGrpcClient<TServiceInterface>(
            this IServiceCollection serviceCollection,
            GrpcClientOptions options,
            ISerializer serializer
        )
            where TServiceInterface : class, IGrpcService
        {
            serviceCollection.AddSingleton<TServiceInterface>(_ => GrpcClientFactory.Create<TServiceInterface>(options, serializer));
            return serviceCollection;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

工厂只是使用`GrpcClientTypeBuilder`类来创建客户机代理类型，然后返回它的一个新实例。

```
using System;
using System.Linq;
using System.Reflection;
using System.Reflection.Emit;
using CodingMilitia.Grpc.Serializers;
using CodingMilitia.Grpc.Shared;
using CodingMilitia.Grpc.Shared.Attributes;

namespace CodingMilitia.Grpc.Client.Internal
{
    internal class GrpcClientTypeBuilder
    {
        public TypeInfo Create<TService>() where TService : class, IGrpcService
        {

            var assemblyName = Guid.NewGuid().ToString();
            var assemblyBuilder = AssemblyBuilder.DefineDynamicAssembly(new AssemblyName(assemblyName), AssemblyBuilderAccess.Run);
            var moduleBuilder = assemblyBuilder.DefineDynamicModule(assemblyName);

            var serviceType = typeof(TService);
            var typeBuilder = moduleBuilder.DefineType(serviceType.Name + "Client", TypeAttributes.Public, typeof(GrpcClientBase));

            typeBuilder.AddInterfaceImplementation(serviceType);
            AddConstructor(typeBuilder, serviceType);
            AddMethods(typeBuilder, serviceType);

            return typeBuilder.CreateTypeInfo();
        }

        //...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Create`方法从创建一个组装构建器和一个模块构建器开始，现在用一个 GUID 作为名字，也许将来我会想出一些有用的名字，但是现在它不是真正相关的。然后创建一个`TypeBuilder`，开始定义服务客户端类型。客户端类型实现了所提供的服务接口，并从`GrpcClientBase`继承而来，这是一个抽象类，带有一些辅助方法来简化其余的类型定义。
在定义了基类和接口实现之后，调用`AddConstructor`和`AddMethods`，最后返回一个完整的运行时生成的类型，表示服务客户端。

在进入`AddConstructor`和`AddMethod`之前，我们先来快速了解一下`GrpcClientBase`。

```
using CodingMilitia.Grpc.Serializers;
using CodingMilitia.Grpc.Shared.Internal;
using System.Threading;
using System.Threading.Tasks;
using G = Grpc.Core;

namespace CodingMilitia.Grpc.Client.Internal
{
    public abstract class GrpcClientBase
    {
        private readonly G.Channel _channel;
        private readonly G.DefaultCallInvoker _invoker;
        private readonly ISerializer _serializer;

        protected GrpcClientBase(GrpcClientOptions options, ISerializer serializer)
        {
            _channel = new G.Channel(options.Url, options.Port, G.ChannelCredentials.Insecure);
            _invoker = new G.DefaultCallInvoker(_channel);
            _serializer = serializer;
        }

        protected async Task<TResponse> CallUnaryMethodAsync<TRequest, TResponse>(TRequest request, string serviceName, string methodName, CancellationToken ct)
            where TRequest : class
            where TResponse : class
        {
            var callOptions = new G.CallOptions(cancellationToken: ct);
            using (var call = _invoker.AsyncUnaryCall(GetMethodDefinition<TRequest, TResponse>(G.MethodType.Unary, serviceName, methodName), null, callOptions, request))
            {
                return await call.ResponseAsync.ConfigureAwait(false);
            }
        }

        private G.Method<TRequest, TResponse> GetMethodDefinition<TRequest, TResponse>(G.MethodType methodType, string serviceName, string methodName)
            where TRequest : class
            where TResponse : class
        {
            return MethodDefinitionGenerator.CreateMethodDefinition<TRequest, TResponse>(methodType, serviceName, methodName, _serializer);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个类相当简单(至少与接下来要做的相比),简单地利用 gRPC 的核心库进行服务调用。
构造函数接收一些参数来创建通信通道和序列化程序，就像我们前面看到的那样。`CallUnaryMethodAsync`确实如其名，调用由服务公开的一元方法，利用`MethodDefinitionGenerator.CreateMethodDefinition`获得方法定义，就像我们之前看到的那样。

现在，对于第一个 IL 发出部分，调用基类构造函数。

```
private void AddConstructor(TypeBuilder typeBuilder, Type serviceType)
{
    var ctorBuilder = typeBuilder.DefineConstructor(
        MethodAttributes.Public,
        CallingConventions.Standard,
        new[] { typeof(GrpcClientOptions), typeof(ISerializer) }
    );

    var il = ctorBuilder.GetILGenerator();
    il.Emit(OpCodes.Ldarg_0); //load this
    il.Emit(OpCodes.Ldarg_1); //load options
    il.Emit(OpCodes.Ldarg_2); //load serializer
    var clientBaseType = typeof(GrpcClientBase);
    var ctorToCall = clientBaseType.GetConstructor(BindingFlags.NonPublic | BindingFlags.Instance, null, new[] { typeof(GrpcClientOptions), typeof(ISerializer) }, null);
    il.Emit(OpCodes.Call, ctorToCall);//call base class constructor
    il.Emit(OpCodes.Ret);
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，只是为了解决这个问题，我不能用心写 IL，所以我写了我期望获得的代码，去得到 IL 并在这里使用它(使用[)。NET Fiddle](https://dotnetfiddle.net/) 获取 IL)。

总之，它定义了一个接收`GrpcClientOptions`和`ISerializer`实例的公共构造函数，然后将参数加载到堆栈上，然后调用基类构造函数。现在你明白为什么基类？因此，我不需要生成整个 IL，只需要调用基类并像往常一样编写其余部分的代码。

现在为`AddMethods` + `AddMethod`

```
private void AddMethods(TypeBuilder typeBuilder, Type serviceType)
{
    foreach (var method in serviceType.GetMethods())
    {
        AddMethod(typeBuilder, method);
    }
}

private void AddMethod(TypeBuilder typeBuilder, MethodInfo method)
{
    var serviceName = ((GrpcServiceAttribute)method.DeclaringType.GetCustomAttribute(typeof(GrpcServiceAttribute))).Name ?? method.DeclaringType.Name;
    var methodName = ((GrpcMethodAttribute)method.GetCustomAttribute(typeof(GrpcMethodAttribute))).Name ?? method.Name;

    var args = method.GetParameters();
    var methodBuilder = typeBuilder.DefineMethod(
        method.Name,
        MethodAttributes.Public | MethodAttributes.Virtual,
        method.ReturnType,
        (from arg in args select arg.ParameterType).ToArray()
    );
    var il = methodBuilder.GetILGenerator();
    il.Emit(OpCodes.Ldarg_0); //load this
    il.Emit(OpCodes.Ldarg_1); //load request
    il.Emit(OpCodes.Ldstr, serviceName); //load constant service name as argument
    il.Emit(OpCodes.Ldstr, methodName); //load constant method name as argument
    il.Emit(OpCodes.Ldarg_2); //load cancellation token
    var clientBaseType = typeof(GrpcClientBase);
    var methodToCall = clientBaseType.GetMethod("CallUnaryMethodAsync", BindingFlags.Instance | BindingFlags.NonPublic);

    il.Emit(
        OpCodes.Call,
        methodToCall.MakeGenericMethod(new[]{ //TODO: must check arguments and stuff
            method.GetParameters()[0].ParameterType,
            method.ReturnType.GetGenericArguments()[0]
        })
    ); //call base class method

    il.Emit(OpCodes.Ret); //return (the return value is already on the stack )

    typeBuilder.DefineMethodOverride(methodBuilder, method);
} 
```

Enter fullscreen mode Exit fullscreen mode

`AddMethods`只是遍历服务接口的方法，但是`AddMethod`是事情再次变得疯狂的地方。

它首先从前面讨论的定制属性中简单地获取服务和方法名。然后代码类似于关于构造函数的讨论——定义方法，从我们现在使用的`MethodInfo`中获取参数类型，然后开始发出 IL 将参数加载到堆栈中，然后调用基类`CallUnaryMethodAsync`方法。因为`CallUnaryMethodAsync`是一个泛型方法，所以需要跳过一些额外的反射环，但是方法调用就像在构造函数代码中一样。总结一下，return 语句被发出，新方法被定义为类型生成器中的一个重写。

## 总结

现在这篇文章比我预期的要长，但是我希望它能有所帮助。

现在总结一下，这个有什么用，没有什么用？正如我一路上所说的，我试图只涵盖最基本的场景，以了解可以做些什么，通过查看结果，我认为再做一点工作，就有可能支持核心 gRPC 库提供的所有常用选项，同时使它与其他的更相似。NET 技术开始。

如果这是用在纯粹的。NET 环境中，我们甚至不必担心原型文件之类的东西，但是如果我们想要与其他技术的互操作性，最好创建服务定义文件，这样其他人就可以照常工作，尽管在这种情况下整个事情变得不那么有用了。

那么，先弄清楚，它准备好生产了吗？...不要！(好像现在还不明显)但是如果有用的话，我认为它可以实现，添加缺失的部分，改进 API，确保性能与我们照常使用核心库时得到的性能相当。

即使这个 PoC 没有更进一步，至少我有理由玩一些不同的东西，包括 IL 发射:)

如果你读了整本书，非常感谢！我希望它是有用的。Cyaz！

PS:原贴[此处](https://blog.codingmilitia.com/2018/04/15/dotnetifying-grpc/)
# 通过 Apache Thrift 迁移到 ASP.NET 核心服务器

> 原文：<https://dev.to/jeikabu/migrating-to-aspnet-core-w-apache-thrift-45f6>

我们的客户端 UI 大部分是 HTML5(通过 [CEF](https://bitbucket.org/chromiumembedded/cef) )，它使用 [Apache Thrift](https://thrift.apache.org/) 与 [Windows 服务](//./windows-service-in-c-1m79)(通过 HTTP 和 WebSockets)对话。作为我们迁徙的一部分。我们的目标是:

*   使用[节俭 0.11](https://github.com/apache/thrift/releases/tag/0.11.0) 中的新`netcore`发电机
*   用[ASP.NET 核心](https://docs.microsoft.com/en-us/aspnet/core/)上的`Thrift.Transports.Server.THttpServerTransport`处理 HTTP 请求，而不是用`System.Net.HttpListener`将请求交给`Thrift.Transport.THttpHandler`

## 之前- HttpListener

基于`System.Net.HttpListener`的最初实现类似于:

```
if (!System.Net.HttpListener.IsSupported)
{
    return;
}

// MultiProcessors.HttpProcessor is a Thrift.TMultiplexedProcessor
var thrift = new Thrift.Transport.THttpHandler(MultiProcessors.HttpProcessor, new Thrift.Protocol.TJSONProtocol.Factory());
var listener = new System.Net.HttpListener();
listener.Prefixes.Add("http://localhost:8282/");
listener.Start();

while (!cts.IsCancellationRequested)
{
    // Receive HTTP request
    var ctx = await listener.GetContextAsync();
    await Task.Run(() =>
    {
        try
        {
            // FIXME: implement CORS correctly
            ctx.Response.AppendHeader("Access-Control-Allow-Origin", "*");

            if (ctx.Request.HttpMethod == "OPTIONS")
            {
                ctx.Response.AddHeader("Access-Control-Allow-Headers", "Content-Type, Accept, X-Requested-With");
                ctx.Response.AddHeader("Access-Control-Allow-Methods", "Get, POST");
                ctx.Response.AddHeader("Access-Control-Max-Age", "1728000");
                ctx.Response.Close();
            }
            else
            {
                // Pass request to thrift services registered with multiplexed processor
                thrift.ProcessRequest(ctx);
            }
        }
        catch(Exception e)
        {
            Log(LogLevel.Warn, $"HttpListener Exception: {e.Message}");
        }
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  使用`TMultiplexedProcessor`实例和`TJSONProtocol`创建`THttpHandler`
    *   [客户端的 Javascript 不支持二进制或压缩协议](https://thrift.apache.org/docs/Languages)
2.  用 [`GetContextAsync()`](https://docs.microsoft.com/en-us/dotnet/api/system.net.httplistener.getcontextasync) 等待 HttpListenerContext
3.  递给 [`ProcessRequest()`](https://github.com/apache/thrift/blob/0.10.0/lib/csharp/src/Transport/THttpHandler.cs#L67)

CEF 需要 CORS 黑客直接从磁盘加载内容。

## 后——ASP.NET 核心

当我们开始研究 ASP.NET 核心时，可配置性和复杂程度令人望而生畏。MS 文档内容丰富，以下内容将帮助您入门:

*   [ASP.NET 核心网站主持人](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/web-host)
*   [ASP.NET 核心中的应用启动](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup)

目前还不清楚如何节俭地处理 HTTP 请求。Thrift 0.11.0 的特点是针对 [netcore](https://github.com/apache/thrift/tree/master/lib/netcore) 的新生成器。netcore 客户端库包含带有`Task Invoke(HttpContext)` 的 [THttpServerTransport，这似乎是 ASP.NET 核心](https://github.com/apache/thrift/blob/master/lib/netcore/Thrift/Transports/Server/THttpServerTransport.cs#L69)的[泄露特征。](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-2.1&tabs=aspnetcore2x)

以下是从许多来源拼凑而成的:

```
try
{
    webHostBuilder = WebHost.CreateDefaultBuilder()
    .UseKestrel(options =>
    {
        options.Listen(IPAddress.Loopback, 8282);
    })
    //.UseUrls("http://localhost:8282/")
    //.UseStartup<Startup>()
    .ConfigureServices(services =>
    {
        services.AddCors();
    })
    .Configure(app =>
    {
        app.UseCors(corsPolicyBuilder =>
        {
            corsPolicyBuilder.AllowAnyHeader();
            corsPolicyBuilder.AllowAnyOrigin();
            corsPolicyBuilder.AllowAnyMethod();
        });
        // Here's our thrift middleware
        app.UseMiddleware<Thrift.Transports.Server.THttpServerTransport>(MultiProcessors.HttpProcessor, new TJsonProtocol.Factory());
    })
    .Build();
    webHostBuilder.Start();
}
catch (Exception ex)
{
    log(LogLevel.Error, "HTTP server failed: " + ex.Message);
} 
```

Enter fullscreen mode Exit fullscreen mode

这使用了 [ConfigureServices()和 Configure()助手，而不是启动类](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup#convenience-methods)。

ASP.NET 核心可以配置有[“中间件”](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/index)来处理请求和响应，而不是等待例程返回 HTTP 请求，然后将其传递给处理程序。

我们最初试图通过[ASP.NET 核心依赖注入](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection) :
来添加节俭即服务

```
.ConfigureServices(services =>
{
    // Couldn't make variants of these work
    services.AddSingleton<ITAsyncProcessor>(MultiProcessors.HttpProcessor);
    services.AddSingleton<ITProtocolFactory>(new TJsonProtocol.Factory());
}) 
```

Enter fullscreen mode Exit fullscreen mode

但是出现了如下错误:

```
Unable to resolve service for type 'Thrift.ITAsyncProcessor' while attempting to activate 'Thrift.Transports.Server.THttpServerTransport'. 
```

Enter fullscreen mode Exit fullscreen mode

服务可能不是正确的机制，而中间件似乎是用于请求/响应处理的。

杂项注释:

*   `AddSingleton()`、`AddTransient()`等的区别。与服务的[生命周期相关。](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection#service-lifetimes)
*   确保关机时调用`webHostBuilder.StopAsync()`。否则，您将在 GC 终结器中得到一个本机异常。

## 结论

不幸的是，我们没有针对这部分代码的任何性能测试。因为我很想知道 ASP.NET 核心是什么野兽之后的结果。

我们已经有了第一个版本，但是我还没有完全理解它。相同的架构可以用来构建网络核心控制台应用程序，所以投入更多的时间是值得的。
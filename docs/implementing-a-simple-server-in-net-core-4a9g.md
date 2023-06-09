# 在中实现简单的服务器。网络核心

> 原文：<https://dev.to/hardkoded/implementing-a-simple-server-in-net-core-4a9g>

我们开始测试[木偶师夏普](https://github.com/kblok/puppeteer-sharp)需要的一件东西是一个运行测试网站的简单网络服务器。我知道理想的场景是这样的:运行`dotnet test`，测试将加载一个 web 服务器，运行所有测试并关闭服务器。我还知道 ASP.NET 核心能够在任何进程中运行，所以在单元测试进程中启动 web 服务器似乎很容易实现。

不是的。

我尝试异步执行 web 主机，但是没有成功。我不能同步执行它，因为它会锁定测试执行。我在使用。NET 框架(而不是核心)，所以我继续前进。我说:“不管怎样，让我们创建一个流程，调用测试服务器并继续编写木偶师代码”。

```
private async Task StartWebServerAsync()
{
    var taskWrapper = new TaskCompletionSource<bool>();
    const int timeout = 2000;

    var build = Directory.GetCurrentDirectory().Contains("Debug") ? "Debug" : "Release";
    var webServerPath = Path.Combine(Directory.GetCurrentDirectory(), "..", "..", "..", "..",
                                        "PuppeteerSharp.TestServer");

    _webServerProcess = new Process();
    _webServerProcess.StartInfo.UseShellExecute = false;
    _webServerProcess.StartInfo.FileName = "dotnet";
    _webServerProcess.StartInfo.WorkingDirectory = webServerPath;
    _webServerProcess.StartInfo.Arguments = $"./bin/{build}/netcoreapp2.0/PuppeteerSharp.TestServer.dll";

    _webServerProcess.StartInfo.RedirectStandardOutput = true;
    _webServerProcess.StartInfo.RedirectStandardError = true;

    _webServerProcess.OutputDataReceived += (sender, e) =>
    {
        Console.WriteLine(e.Data);
        if (e.Data != null &&
            taskWrapper.Task.Status != TaskStatus.RanToCompletion &&
            //Though this is not bulletproof for the purpose of local testing
            //We assume that if the address is already in use is because we have another
            //process hosting the site
            (e.Data.Contains("Now listening on") || e.Data.Contains("ADDRINUSE")))
        {
            taskWrapper.SetResult(true);
        }
    };

    _webServerProcess.Exited += (sender, e) =>
    {
        taskWrapper.SetException(new Exception("Unable to start web server"));
    };

    Timer timer = null;
    //We have to declare the timer before initializing it because if we don't, 
    //we can't dispose it in the action created in the constructor
    timer = new Timer((state) =>
    {
        if (taskWrapper.Task.Status != TaskStatus.RanToCompletion)
        {
            taskWrapper.SetException(
                new Exception($"Timed out after {timeout} ms while trying to connect to WebServer! "));
        }
        timer.Dispose();
    }, null, timeout, 0);

    _webServerProcess.Start();
    _webServerProcess.BeginOutputReadLine();

    await taskWrapper.Task;
} 
```

# 第一个内部过程

对我来说幸运的是， [Meir Blachman](https://www.twitter.com/MeirBlachman) 加入了这个项目，除了其他事情之外，他还能够[让 web 主机在测试项目](https://github.com/kblok/puppeteer-sharp/pull/101)中运行。这是伟大的一天。

现在我们只用这 4 行代码启动服务器:

```
var builder = Startup.GetWebHostBuilder();
builder.UseContentRoot(TestUtils.FindParentDirectory("PuppeteerSharp.TestServer"));
_host = builder.Build();
await _host.StartAsync(); 
```

Meir [在他的 PR 中写了一个简单而冷静的解释](https://github.com/kblok/puppeteer-sharp/pull/101#issuecomment-378902668)。

*   DllNotFoundException:无法加载 DLL“libuv”-尤其是此注释。查看 aspnet/KestrelHttpServer#1292(注释)，我意识到我只需要添加 RuntimeIdentifier 元素。
*   将 RuntimeIdentifier 添加到 PuppeteerSharp。请使用 Tests.csproj 而不是 PuppeteerSharp。TestServer.csproj
*   使用 netstandard 2.0 而不是 netcoreapp 2.0 *查找木偶师 Sharp。TestServer 目录，因为对于 net471 它是一个更深的目录
    *   net471 -木偶竖琴。tests \ bin \ Debug \ net 471 \ win 7-x64
    *   netcoreapp2.0 -木偶剧。测试\bin\Debug\netcoreapp2.0

# 我们需要一个简单服务器

我们可以继续研究木偶师夏普。我们不再有进程泄漏和端口锁定。但是 Puppeteer 有很多测试是在服务器端拦截请求的，比如 **server.setAuth** 。

```
server.setAuth('/empty.html', 'user', 'pass'); 
```

这很容易实现。我们创建了一个控制器，[实现了一个基本的 HTTP 认证](https://github.com/kblok/puppeteer-sharp/blob/8c5a9e531efcc0a6eaa406489eb3092bc1fc49a3/lib/PuppeteerSharp.TestServer/Controllers/AuthenticationTestController.cs)。

但后来，有些事情变得相当棘手。例如，我们需要在服务器端等待一个请求:

```
await server.waitForRequest('/one-style.css') 
```

我们必须将 Web 主机**集成到测试套件**中。我们需要能够在一些测试运行之前改变服务器行为。

# 让我们实现一个简单的服务器

同样，[梅尔·布莱克曼](https://www.twitter.com/MeirBlachman)说[“我们需要一个简单的服务器”](https://github.com/kblok/puppeteer-sharp/issues/116)。他最终实现了一个[非常酷的解决方案](https://github.com/Meir017/puppeteer-sharp/blob/a522f3062e53a019ed6a4c06e00c7545b610135e/lib/PuppeteerSharp.TestServer/SimpleServer.cs)

API 很简单

```
public class SimpleServer
{
    public static SimpleServer Create(int port, string contentRoot)
    public static SimpleServer CreateHttps(int port, string contentRoot)

    public void SetAuth(string path, string username, string password)
    public Task StartAsync()
    public async Task StopAsync()
    public void Reset()
    public void SetRoute(string path, RequestDelegate handler)
    public void SetRedirect(string from, string to)
    public async Task<T> WaitForRequest<T>(string path, Func<HttpRequest, T> selector)
    private static bool Authenticate(string username, string password, HttpContext context)
} 
```

中间件的实现只用了几行代码:

```
 .Configure(app => app.Use((context, next) =>
{
    if (_auths.TryGetValue(context.Request.Path, out var auth) && !Authenticate(auth.username, auth.password, context))
    {
        context.Response.Headers.Add("WWW-Authenticate", "Basic realm=\"Secure Area\"");
        context.Response.StatusCode = StatusCodes.Status401Unauthorized;
        return context.Response.WriteAsync("HTTP Error 401 Unauthorized: Access is denied");
    }
    if (_requestSubscribers.TryGetValue(context.Request.Path, out var subscriber))
    {
        subscriber(context.Request);
    }
    if (_routes.TryGetValue(context.Request.Path, out var handler))
    {
        return handler(context);
    }
    return next();
}) 
```

所以现在，我们可以这样做:

```
Server.SetAuth("/empty.html", "user", "pass");

var response = await Page.GoToAsync(TestConstants.EmptyPage);
Assert.Equal(HttpStatusCode.Unauthorized, response.Status);

await Page.AuthenticateAsync(new Credentials
{
    Username = "user",
    Password = "pass"
});

response = await Page.ReloadAsync();
Assert.Equal(HttpStatusCode.OK, response.Status); 
```

或者拦截一个请求:

```
await Page.SetExtraHttpHeadersAsync(new Dictionary<string, string>
{
    ["Foo"] = "Bar"
});

var headerTask = Server.WaitForRequest("/empty.html", request => request.Headers["Foo"]);
await Task.WhenAll(Page.GoToAsync(TestConstants.EmptyPage), headerTask);

Assert.Equal("Bar", headerTask.Result); 
```

# 最后的话

虽然 Simple Server 是为木偶师的需求而创建的解决方案，但它也展示了您可以使用它做什么。NET 核心，同时在您的应用程序中实现一个简单的服务器。

不要停止编码！

最初发布于[harkoded.com](https://www.hardkoded.com/blogs/implementing-simple-server)
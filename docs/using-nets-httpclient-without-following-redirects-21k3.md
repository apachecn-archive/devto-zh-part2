# 使用。不跟随重定向

> 原文：<https://dev.to/joaofbantunes/using-nets-httpclient-without-following-redirects-21k3>

我很确定(或者也许我错了)我们很少需要避免跟随重定向，但是我遇到过一两次这样的需要，所以我不妨写下来:)

附带源代码[此处](https://github.com/joaofbantunes/HttpClientNotFollowingRedirectsSample)。

## 场景

因此，对于一个具体的场景，我实际上需要`HttpClient`不跟随重定向。

在工作中，我们实现了一个依赖于遗留系统的新特性。该功能包括显示一些可以点击的横幅(类似于广告)。当一个横幅被点击时，它必须做很多事情:

*   打开一个模态窗口做一些事情
*   Ping 指示横幅被点击的跟踪 url
*   跟踪 url 可能会也可能不会进行重定向，在这种情况下，应该会打开一个新的选项卡

让我们忽略它的可用性...这些是要求。

所以对于上面的步骤，有问题的是第三步——我们集成的遗留系统负责横幅和跟踪 URL，所以我们必须让它与我们现有的系统一起工作。我想到的第一个想法是，嗯，总是打开一个新标签，如果它重定向，那么我们很好，如果不是...我们有一个打开的空白标签:)

空白标签不是很酷，所以我们认为我们可以做得更好。在一些或多或少复杂的想法之后，新的计划是创建一个间接的 <sup>1</sup> 来请求跟踪 url 而不遵循重定向——如果响应是 200，我们就完成了，如果是 302，这个中间人将返回目标 url，应用程序可以在一个新的选项卡中打开它。

## 解

做这件事。我们通常使用一个`HttpClient`，但是由于它的默认行为是跟随重定向，所以需要做一点配置。

```
var handler = new HttpClientHandler()
{
    AllowAutoRedirect = false
};
var httpClient = new HttpClient(handler);

var response = await _httpClient.GetAsync(trackingUrl, ct);

var targetUrl = response.StatusCode == HttpStatusCode.Redirect
        ? response.Headers.Location.OriginalString
        : null; 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！如果您阅读这篇文章只是为了了解所需的配置，那么它就是——这么多的单词，以大约十行代码结尾...抱歉:)

对于完整的上下文，我们在一个 ASP.NET 应用程序中这样做，现在人们应该知道我们不能到处实例化 http 客户端，否则我们会以端口耗尽而告终，所以我们最终创建了一个类来抽象它。

```
//Usings...

namespace CodingMilitia.HttpClientNotFollowingRedirectsSample.Web
{
    public class TrackingHttpClientWrapper
    {
        private HttpClient _httpClient;

        public TrackingHttpClientWrapper()
        {
            var handler = new HttpClientHandler()
            {
                AllowAutoRedirect = false
            };
            _httpClient = new HttpClient(handler);
        }

        public async Task<string> TrackAsync(string trackingUrl, CancellationToken ct)
        {
            var response = await _httpClient.GetAsync(trackingUrl, ct);

            return response.StatusCode == HttpStatusCode.Redirect
                 ? response.Headers.Location.OriginalString
                 : null;
        }

        //IDisposable stuff...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

并在 DI 容器中配置为 singleton。

```
//...
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    //old school version
    services.AddSingleton<TrackingHttpClientWrapper>();
    //not the best place to put this but... it'll suffice for the sample
    ServicePointManager.FindServicePoint(new Uri("http://httpstat.us/")).ConnectionLeaseTimeout = (int)TimeSpan.FromMinutes(1).TotalMilliseconds;
    ServicePointManager.DnsRefreshTimeout = (int)TimeSpan.FromMinutes(1).TotalMilliseconds;
}
//... 
```

Enter fullscreen mode Exit fullscreen mode

还做了`ServicePointManager`配置，以确保 DNS 刷新，如这里的[所示](https://github.com/dotnet/corefx/issues/11224)。

最后，控制器动作可以简单地做如下事情。

```
[Route("track")]
public async Task<IActionResult> TrackAsync(string trackingUrl, CancellationToken ct)
{
    var targetUrl = await _tracker.TrackAsync(trackingUrl, ct);
    if (string.IsNullOrWhiteSpace(targetUrl))
    {
        _logger.LogInformation("No target url -> it wasn't a redirect");
    }
    else
    {
        _logger.LogInformation("Target url: \"{targetUrl}\" -> it was a redirect", targetUrl);
    }
    return Ok(new {targetUrl});
} 
```

Enter fullscreen mode Exit fullscreen mode

## 奖励回合:相同解决方案 ASP.NET 核心 2.1 版本

在 ASP.NET 核心 2.1 中，有一些新的东西可以与`HttpClient`一起工作，并避免由于端口耗尽和 DNS 刷新而导致的所有这些恶作剧，所以我在示例中添加了一个使用新`HttpClientFactory`功能的 V2 解决方案。

```
//Usings...

namespace CodingMilitia.HttpClientNotFollowingRedirectsSample.Web
{
    public class TrackingHttpClientWrapperV2
    {
        private HttpClient _httpClient;

        public TrackingHttpClientWrapperV2(HttpClient httpClient)
        {
            _httpClient = httpClient;
        }

        public async Task<string> TrackAsync(string trackingUrl, CancellationToken ct)
        {
            var response = await _httpClient.GetAsync(trackingUrl, ct);

            return response.StatusCode == HttpStatusCode.Redirect
                 ? response.Headers.Location.OriginalString
                 : null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

并向 DI 容器注册:

```
//...
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    //ASP.NET Core 2.1 version
    services.AddScoped<TrackingHttpClientWrapperV2>();
    services.AddHttpClient<TrackingHttpClientWrapperV2>()
        .ConfigurePrimaryHttpMessageHandler(() =>
        {
            return new HttpClientHandler
            {
                AllowAutoRedirect = false
            };
        });
}
//... 
```

Enter fullscreen mode Exit fullscreen mode

## 其他阅读材料

关于 ASP.NET 核心 2.1 `HttpClient`相关特性的更多信息，史蒂夫·戈登有一系列的文章，第一篇[在这里](https://www.stevejgordon.co.uk/introduction-to-httpclientfactory-aspnetcore)。

## 包装完毕

好吧，这篇文章可能比它需要的要大，只是告诉如何配置一个 http 客户端不跟随重定向，但是因为我有一个真实世界的场景来证明它的有用性，我想我还是分享一下吧。

另外，`HttpClientHandler`除了我使用的`AllowAutoRedirect`还有一些其他选项，所以如果你需要做一些`HttpClient`没有直接做的事情，你可能想看看`HttpClientHandler`提供了什么。

感谢阅读！

* * *

1 - [软件工程基本定理](https://en.wikipedia.org/wiki/Fundamental_theorem_of_software_engineering)

PS:原贴[此处](https://blog.codingmilitia.com/2018/04/28/using-dotnet-httpclient-without-following-redirects)
# 跨来源请求被阻止？

> 原文：<https://dev.to/jonhilt/cross-origin-request-blocked-15ee>

您已经使用 ASP.NET 核心构建了一个全新的 API。

你可以通过网络浏览器(或者我最喜欢的工具之一，[失眠](https://insomnia.rest/))来调用它，一切都很完美。

[![](img/a18e34bdc2dd308101a9f96ff4fdc1a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WGtZmX6T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-5/2018-08-30-10-39-01.png)

然后你试着从你的前端调用它(反应，角度或什么的)然后 boom。

[![](img/a338c68edf47c4fa35155e9f20c90128.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JX3tBOnw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-5/2018-08-30-10-40-47.png)

您会得到一个关于跨源请求被阻塞的神秘错误，因为同一个源策略不允许读取某处或其他地方的远程资源...

这是什么？

所有现代浏览器都执行一种叫做“同源策略”的东西。

这是为了防止互联网上的一些随机网站能够向您的 API 发出请求。

## 为什么会存在同源策略？

举这个例子。

我在[https://jonhilton.io/api](https://jonhilton.io/api)建立了一个 API，在同一个域建立了一个前端(使用 React、Angular 之类的东西)。

到目前为止一切顺利，我启动了网站，每个人都开始使用 [https://jonhilton.io](https://jonhilton.io) ，它与我的 API 交互，一切都很好。

但是，灾难来了。我的一个用户被骗去了一个欺骗网站，这个网站看起来像是我的，但实际上是托管在 https://dodgygeezer.com 的。

位于[https://dodgygeezer.com](https://dodgygeezer.com)的页面包含调用我的 api 的 javascript。

没有同样的原产地政策，就没有什么能阻止 https://dodgygeezer.com 提出这些要求。

## 同源施救

同源策略(在浏览器中实现)做了很多事情来保护你的站点不被恶意使用。

当谈到 AJAX 请求时，我们感兴趣的主要方面是它能够防止在浏览器中执行的代码(例如，您的或其他人的 javascript)对不同来源的站点进行某些 AJAX 调用。

> Origin 指的是为 javascript 提供服务的页面的域。
> 
> 对于这个例子，来自我的站点的合法请求的来源是 [https://jonhilton.io](https://jonhilton.io)
> 
> 这个狡猾的冒名顶替网站的发源地应该是 https://dodgygeezer.com

如果客户端从服务器不信任的来源发出请求，同源策略会尝试限制客户端可以执行的操作。

默认情况下，您的 ASP.NET 核心 Web API 将允许来自同一来源站点的任何请求。所以任何为 [https://jonhilton.io](https://jonhilton.io) 提供的 javascript 都可以。

## 那么为什么当我调用我自己的 API 时会出现问题呢？

这一切都很好，但为什么当您自己的前端应用程序试图与您的 api 通信时，您会看到这个错误？

好吧，如果他们都来自同一个域，你会没事的。

前端: [https://jonhilton.io](https://jonhilton.io)
后端:[https://jonhilton.io/api](https://jonhilton.io/api)

两者都是从 [https://jonhilton.io](https://jonhilton.io) 提供的，因此具有相同的来源。

前端: [https://jonhilton.io](https://jonhilton.io)
后端: [https://api.jonhilton.io](https://api.jonhilton.io)

现在我们有问题了。这里的子域意味着这些被认为是不同的起源。

如果你在本地运行你的 api 和前端站点，由于使用了不同的端口，你会遇到同样的问题。

前端:[https://localhost:3000](https://localhost:3000)
后端: [https://localhost:5000](https://localhost:5000)

这里的不同港口意味着这些被认为是不同的来源。

## 好吧，那么，我如何允许我自己的前端绕过这个策略呢？

### 快捷方便

如果你想为自己的应用程序生成 CORS 代码(并获得生成代码的简便解释),请查看[CorsDot.net](https://corsdot.net)。

[![](img/dd2711c31185b81902110633ccc4f98f.png)T2】](https://corsdot.net)

这是我创建的一个小站点，用来解决配置 CORs 的问题。请查看并让我知道您的进展:-)

### CORs 配置的一个例子

让我们举这个例子。

前端: [https://jonhilton.io](https://jonhilton.io)
后端: [https://api.jonhilton.io](https://api.jonhilton.io)

如果我们希望 [https://jonhilton.io](https://jonhilton.io) 能够向 [https://api.jonhilton.io](https://api.jonhilton.io) 发出请求，我们需要通过实现 api 的 CORS 策略来配置我们的 API 以允许这些请求。

> CORS =跨来源资源共享

CORS 让你控制浏览器中运行的哪些资源(前端应用程序)会调用你的 API。

在 ASP.NET Core 2 . x 中，这只是一个简单的配置问题。

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ...

    app.UseCors(builder =>
        builder.WithOrigins("https://jonhilton.io"));

    app.UseHttpsRedirection();
    app.UseMvc();
} 
```

Enter fullscreen mode Exit fullscreen mode

**确保`app.UseCors`调用在`app.UseMVC`之前，否则它将对触发 MVC 管道的应用程序请求没有影响(这是所有的 MVC 和 API 请求！).**

仅此而已。如果您现在检查来自 API 的任何响应，您会注意到它们包括这个响应头。

[![](img/2b0278d3825492564d97ca7968397687.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lx-4vry_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-5/2018-08-30-16-51-08.png)

这个“Access-Control-Allow-Origin”头是确保您不会再看到可怕的 CORS 错误的魔法。

如果你想要一个以上的网址被允许，你可以简单地添加更多的列表。

```
app.UseCors(builder =>
    builder.WithOrigins(
        "https://jonhilton.io", 
        "https://localhost:3000")); 
```

Enter fullscreen mode Exit fullscreen mode

抓住你了:省略结尾的斜线！

注意一件事。当向允许的起源列表中添加一个起源时，很容易意外地包含一个结尾斜杠。

```
app.UseCors(builder =>
    builder.WithOrigins(
        "https://jonhilton.io/", 
        "https://localhost:3000/")); 
```

Enter fullscreen mode Exit fullscreen mode

但是如果你这样做，你会发现你又开始看到“同源策略”的错误。这是一个会让你因为“看起来正确”而抓狂的问题，但是后面的斜线会打断你的 CORS！
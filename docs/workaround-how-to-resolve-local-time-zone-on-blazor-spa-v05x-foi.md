# [解决方法]如何解析 Blazor SPA (v.0.5.x)上的本地时区？

> 原文：<https://dev.to/j_sakamoto/workaround-how-to-resolve-local-time-zone-on-blazor-spa-v05x-foi>

## 简介:“Blazor”是什么？

“Blazor”是一个单页面 Web 应用程序(也称为“SPA”)框架。

Blazor 允许你运行 C#代码和。NET Standard 2.0 本机程序集(。dll)放在现代 web 浏览器的顶部，没有任何。NET 服务器进程。

如果你是 Blazor 的新手，我推荐你看这个 YouTube 视频。

[NDC 奥斯陆 2017 -“网络应用不能真的做*那个*，对吧？——史蒂夫·桑德森”](https://www.youtube.com/watch?v=MiLAE6HMr10&feature=youtu.be&t=31m59s)

视频讲的是 Blazor 的原型，我写这篇帖子的时候目前的版本是 v.0.5.x，有一些突破性的变化。但那个视频对理解“Blazor”的概念和利弊是有用的。

## 问题:Blazor SPA 没有时区信息...！

我觉得 Blazor 对于 C#开发者来说是一个很棒的框架。

然而，Blazor 目前仍是一个实验项目。

最近我发现其中一个案例表明 Blazor 仍然是一个实验项目。

令人惊讶的是，Blazor 应用程序上的时区信息计数为“0”！

因此，`"TimeZoneInfo.Local"`总是返回 UTC 时区，`" DateTime.Now"`总是返回 UTC 时间，不管浏览器或操作系统的本地设置如何。

[![fig.1](img/19a3165f393e2cdbfeb2731611e5f109.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nhW7a03q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/jsakamoto/Toolbelt.Blazor.TimeZoneKit/blob/master/.assets/fig-2018-09-03-001.png%3Fraw%3Dtrue)

如果我们需要访问当地时间或任何时区的信息，我们该怎么办？

## 快速解决方法:使用“工具带。Blazor.TimeZoneKit" NuGet 包。

您可以通过安装["工具带来解决这个问题。Blazor . time zone kit " nu 获取 Blazor 应用程序项目上的包](https://www.nuget.org/packages/Toolbelt.Blazor.TimeZoneKit/),如下所示。

**step . 1**——安装 NuGet 包，如下。

```
> dotnet add package Toolbelt.Blazor.TimeZoneKit 
```

Enter fullscreen mode Exit fullscreen mode

**step . 2**——在启动类的`"Configure()"`方法中调用包提供的`"UseLocalTimeZone()"`扩展方法。

```
...
using Toolbelt.Blazor.TimeZoneKit;

public class Startup
{
    ...
    public void Configure(IBlazorApplicationBuilder app)
    {
        app.UseLocalTimeZone();
    ... 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！

[![fig.2](img/1caa930d25149efbc39173c1a5d0ee94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T7lvJUCk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/jsakamoto/Toolbelt.Blazor.TimeZoneKit/blob/master/.assets/fig-2018-09-03-002.png%3Fraw%3Dtrue)

## 如何设置系统时区信息？

没有魔法。

“工具带。只是包含了所有的。NET 系统时区信息。dll)文件。

(参见: ["TimeZoneKit。CreateSystemTimeZones.cs"](https://github.com/jsakamoto/Toolbelt.Blazor.TimeZoneKit/blob/master/Toolbelt.Blazor.TimeZoneKit/TimeZoneKit.CreateSystemTimeZones.cs#L16) )

`"UseLocalTimeZone()"`扩展方法通过反射技术覆盖`"TimeZoneInfo"`的私有字段，在应用运行时设置系统时区信息。

## 它是如何获取浏览器的当地时区的？

最近，大多数 web 浏览器都支持`"Intl.DateTimeFormat().resolvedOptions().timeZone"` JavaScript 代码。

该 JavaScript 代码返回本地时区名称。

Blazor 提供了一种 C#代码和 JavaScript 代码互操作的方式，因此`"UseLocalTimeZone()"`扩展方法可以调用 JavaScript 代码来获取本地时区名称。

[![fig.3](img/db63805c6b4fab6e484435a350554780.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DsvdWc_Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/jsakamoto/Toolbelt.Blazor.TimeZoneKit/blob/master/.assets/fig-2018-09-03-003.png%3Fraw%3Dtrue)

然而，从 C#到 JavaScript 的互操作代码是异步的。

如果`"UseLocalTimeZone()"`扩展方法在执行期间不能同步完成当地时区的设置，将会导致可怕的包。

[![fig.4](img/355eb4a000ab539a51632032fb4c1b47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ELAOXkn3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.TimeZoneKit/master/.assets/fig-2018-09-04-001.png)

但是，因为它会导致死锁，所以我们不能同步等待任务结果。

[![fig.5](img/c8e6b2b4ea6384d779065225f95467b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TosxWuok--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.TimeZoneKit/master/.assets/fig-2018-09-04-002.png)

为了避免这个问题，`"UseLocalTimeZone()"`扩展方法调用 JavaScript 代码回调 C#代码。

由于从 JavaScript 到 C#的 interope 代码是同步的，loca 时区设置过程会一直完成，直到`"UseLocalTimeZone()"`扩展方法返回；)

[![fig.6](img/c5b3cf4cfef92a179b0018bf34ba3717.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--taBHtcc7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/jsakamoto/Toolbelt.Blazor.TimeZoneKit/blob/master/.assets/fig-2018-09-04-003.png%3Fraw%3Dtrue)

## 如何将 IANA 时区名称转换为。网络时区 id？

JavaScript 代码`"Intl.DateTimeFormat().resolvedOptions().timeZone"`返回时区名称，但它不是。净时区 id。

JavaScript 的本地时区名称为“IANA 时区名”。

为了将 IANA 时区名称转换为。网络时区 id，”工具带。包含从 IANA 时区名称到。NET 时区 id，在它的程序集(.dll)文件。

(see: ["TimeZoneKit.IANAtoTZIdMap.cs"](https://github.com/jsakamoto/Toolbelt.Blazor.TimeZoneKit/blob/master/Toolbelt.Blazor.TimeZoneKit/TimeZoneKit.IANAtoTZIdMap.cs#L5) )

## 注

*   **警告** -库访问系统的私有成员。TimeZoneInfo 使用“反射”。NET API，所以将来可能会崩溃。NET 运行时(mono.wasm)版本。
*   在我的测试案例中，该库将 Blazor 浏览器应用程序内容的大小增加了 154 KB。(通过 20KB gzip 传输。)

## 结论

我认为 Blazor 的未来版本将支持当地时区。

另请参见:

# [![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) 日期时间始终显示为 UTC  #15776](https://github.com/dotnet/aspnetcore/issues/15776) 

[![RickStrahl avatar](img/464f1c2638769df84a40212f113cec82.png)](https://github.com/RickStrahl) **[RickStrahl](https://github.com/RickStrahl)** posted on [<time datetime="2018-07-22T07:42:15Z">Jul 22, 2018</time>](https://github.com/dotnet/aspnetcore/issues/15776)

DateTime 值总是显示为 UTC 时间，而不是本地时间，即使在显式强制`.ToLocalTime()`时也是如此。

这个:

```
Console.WriteLine(DateTime.Now.ToLocalTime().ToString("HH:mm"));
```

Enter fullscreen mode Exit fullscreen mode

显示 UTC 时间。

[View on GitHub](https://github.com/dotnet/aspnetcore/issues/15776)

“工具带。Blazor.TimeZoneKit" NuGet 包将帮助你直到 Blazor 支持时区。

让我们一起期待 Blazor 的新发布吧！

用 Blazor 快乐编码:)
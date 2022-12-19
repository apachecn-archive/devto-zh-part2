# 在 Blazor 中本地化日期时间

> 原文：<https://dev.to/rembou1/localizing-datetime-in-blazor-4p5c>

# 在 Blazor 中本地化日期时间(和数字)

当我们创建一个被许多国家的人使用的应用程序时，我们很快就会面临翻译它的挑战。另一个挑战是让每个人都能理解非字符串数据(数字、日期)。例如，“01/02/2018”对法国人来说意味着“2018 年 2 月 1 日”，但对英国人来说意味着“2018 年 1 月 2 日”。

幸运是，当我们称之为“日期时间”时，所有这些特定的格式都已经由微软设定好了。它查找当前区域性(存储在静态属性“CultureInfo”中。CurrentCulture”)并创建表示日期时间的好字符串。

这是 Blazor 的最佳论据:您可以使用所有现有的。net 库/ API。如果它符合两个条件，您就可以:

*   它在一个面向 netstandard 的库中
*   它所使用的标准部分是由 mono 团队在 web 组装实现( [repo github](https://github.com/mono/mono) )中实现的。

CultureInfo 和其他用于格式化的类是 netstandard 的一部分(我猜它是在这里定义的)并由 mono 团队实现(mono repo 对我来说太难浏览了，但我认为他们肯定已经实现它超过 10 年了)。

## 获取用户语言

第一步是获取用户语言。因为它是在浏览器中设置的，并在每个 HTTP 请求的 accept-language 头中发送，所以它应该在 Javascript 中可用。确实有“navigator.languages ”,它与每个浏览器的最新版本兼容。

互操作调用如下所示

```
navigatorLanguages = function () {
    return navigator.languages;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   我必须为此创建一个小函数，因为 js interop 只接受函数调用，不接受属性。

调用它的 CS 代码非常简单

```
public static async Task<string[]> Languages()
{
    return await JSRuntime.Current.InvokeAsync<string[]>("navigatorLanguages");
} 
```

Enter fullscreen mode Exit fullscreen mode

## 设置当前语言

CultureInfo。CurrentCulture 绑定到线程，所以我们不能像 Blazor / monowasm 那样使用它，因为它可能会创建多个线程(例如，使用 await 肯定会创建一个线程)。见[本期 github](https://github.com/aspnet/Blazor/issues/1056)我们努力寻找初始化语言的正确位置。我不知道他们如何使用 ASPNET Core 在所有当前请求线程中传递文化，但这里我们有机会设置为 statefull，因此我们可以使用属性“System”为整个应用程序设置它。将在线程创建时使用的。为了给整个应用程序设置一些东西，我们可以使用 Main()方法，下面是我设置它的代码:

```
JSRuntime.Current.InvokeAsync<string[]>("navigatorLanguages")
    .ContinueWith(t => CultureInfo.DefaultThreadCurrentCulture = t.Result.Select(c => CultureInfo.GetCultureInfo(c)).FirstOrDefault())
    .ContinueWith(t => new BrowserRenderer(serviceProvider).AddComponent<App>("app")) ; 
```

Enter fullscreen mode Exit fullscreen mode

*   我使用 ContinueWith 是因为 Main()不是异步的
*   应用程序渲染是在语言初始化之后添加的，否则我们会被贴上不良文化的标签
*   我很幸运这里有 JSInterop:)

## 结论

随着我继续使用 Blazor，我遇到的大多数问题都是由于生命周期和任务管理，但是解决这种问题让我对整个事情有了更好的理解，我希望你也一样(我甚至对 mono 做了一次无用的检查)。

您可以在我的 Toss 项目中找到这一点([链接](https://github.com/RemiBou/Toss.Blazor)和[相关提交](https://github.com/RemiBou/Toss.Blazor/commit/505e9a8c6fc3bc35d9fb7b0bfa59d50eca2de4f3))。
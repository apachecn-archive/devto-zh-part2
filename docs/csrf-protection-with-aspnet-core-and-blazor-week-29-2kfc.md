# 使用 Aspnet Core 和 Blazor 的 Csrf 保护第 29 周

> 原文：<https://dev.to/rembou1/csrf-protection-with-aspnet-core-and-blazor-week-29-2kfc>

# 用 ASPNET 核心和 Blazor 保护 CSRF

https://scotthelme.co.uk/csrf-is-dead/ CSRF 是一个众所周知的网络应用漏洞，你可以在这里找到更多关于它的信息。如题所见，这个漏洞是死的。但对于拥有最新浏览器的人来说，它已经死了。所以我们必须实施经典的保护措施，直到每个浏览器都实施了保护措施，每个人都更新了自己的浏览器。

在本文中，我将向您展示我是如何用我的 Blazor / ASPNET 核心应用 calles [TOSS](https://github.com/RemiBou/Toss.Blazor) 实现它的

## 服务器端(ASPNET Core 2.1.1)

通常 CSRF 保护是这样工作的:

*   浏览器在隐藏字段中呈现带有标记的表单
*   用户提交表单
*   服务器验证客户端请求上的字段，并对其进行验证

但是在 SPA 中，表单不是在服务器端创建的，所以我们需要另一种方法。我将使用的方法如下:

*   服务器发送一个带有验证令牌的非 http cookie(因此可以从 javascript 中读取)
*   客户端读取 cookie，并在它将要执行的每个 http 请求(ajax)的头中发送它的值

在服务器端，我需要一个 ASPNET 核心中间件来设置 cookie(如果不存在的话)

```
public class CsrfTokenCOokieMiddleware
    {
        private readonly IAntiforgery _antiforgery;
        private readonly RequestDelegate _next;

        public CsrfTokenCOokieMiddleware(IAntiforgery antiforgery, RequestDelegate next)
        {
            _antiforgery = antiforgery;
            _next = next;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if(context.Request.Cookies["CSRF-TOKEN"] == null)
            {
                var token = _antiforgery.GetTokens(context);
                context.Response.Cookies.Append("CSRF-TOKEN", token.RequestToken, new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
            }
            await _next(context);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

*   中间件不必实现特定的接口，只需声明这个方法“async Task invoke async(HttpContext context)”，不知道为什么。Net 核心团队并没有用接口来保护它
*   我使用内置的 IAntiforgery 服务来生成值，因为我不知道它应该如何构建。当我们谈论安全性时，我们必须尽可能避免内部代码。

然后我在 Startup.cs 的 Configure 方法中注册了这个中间件

```
app.UseMiddleware<CsrfTokenCOokieMiddleware>(); 
```

Enter fullscreen mode Exit fullscreen mode

并通过“ConfigureService”方法上的头启用 XSRF 保护

```
services.AddAntiforgery(options =>
            {
                options.HeaderName = "X-CSRF-TOKEN";
            }); 
```

Enter fullscreen mode Exit fullscreen mode

*   如果不添加此项，CSRF 保护将只在请求中查找字段

在我想要保护的所有方法中(主要是 POST，因为 GET 对系统没有影响),我添加了以下属性

```
[ValidateAntiForgeryToken] 
```

Enter fullscreen mode Exit fullscreen mode

*   可能有一种方法可以避免这种情况，但是我不知道。

## 客户端(Blazor 0.4)

在客户端，首先我需要读取 cookie 的值(两者托管在同一个域上，所以当我加载我的应用程序时，cookie 应该被设置)。为了读取 cookie 值，我必须使用 Js interop，因为 Blazor 中没有读取 cookie 的方法。

```
Blazor.registerFunction("getDocumentCookie", function () {
    return { content: document.cookie };
}); 
```

Enter fullscreen mode Exit fullscreen mode

*   我为我的结果使用了一个容器，因为 Blazor 中的字符串序列化/反序列化有问题

然后在 C#中，我读取这个值

```
public static string GetCookie()
        {
            StringHolder stringHolder = RegisteredFunction.Invoke<StringHolder>("getDocumentCookie");           
            return stringHolder.Content;
        } 
```

Enter fullscreen mode Exit fullscreen mode

我创建了一个服务来解析这个值

```
/// <summary>
    /// Service for reading the current cookie on the browser
    /// </summary>
    public class BrowserCookieService : IBrowserCookieService
    {
        /// <summary>
        /// returns the cookie value or null if not set
        /// </summary>
        /// <param name="cookieName"></param>
        /// <returns></returns>
        public string Get(Func<string,bool> filterCookie)
        {
            return JsInterop
                .GetCookie()
                .Split(';')
                .Select(v => v.TrimStart().Split('='))
                .Where(s => filterCookie(s[0]))
                .Select(s => s[1])
                .FirstOrDefault();
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

DI 设置是在我的客户端项目的 Program.cs 中设置的，就像这样

```
configure.Add(new ServiceDescriptor(
   typeof(IBrowserCookieService),
   typeof(BrowserCookieService),
   ServiceLifetime.Singleton)); 
```

Enter fullscreen mode Exit fullscreen mode

然后，我像这样在所有 http 调用上添加头(我使用包装器包装 HttpClient 以避免复制/粘贴)

```
private HttpRequestMessage PrepareMessage(HttpRequestMessage httpRequestMessage)
  {
      string csrfCookieValue = browserCookieService.Get(c => c.Equals("CSRF-TOKEN"));
      if (csrfCookieValue != null)
          httpRequestMessage.Headers.Add("X-CSRF-TOKEN", csrfCookieValue);
      return httpRequestMessage;
  } 
```

Enter fullscreen mode Exit fullscreen mode

*   我可以使用 HttpClient DefaultHeaders，但是我找不到一个好的地方来初始化它。
*   而且有效:)

我认为这种实现 CSRF 的方式对于 SPA 来说很常见，但是如果有安全问题，请告诉我。

## 引用

*   [https://scotthelme.co.uk/csrf-is-dead/](https://scotthelme.co.uk/csrf-is-dead/)
*   [https://github.com/aspnet/Antiforgery/](https://github.com/aspnet/Antiforgery/)
*   [https://docs . Microsoft . com/en-us/aspnet/core/security/反请求-伪造？view = aspnetcore-2.1 # JavaScript-Ajax-and-spas](https://docs.microsoft.com/en-us/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1#javascript-ajax-and-spas)
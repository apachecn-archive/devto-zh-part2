# Google Oauth 与 Blazor Aspnet 核心第 26 周

> 原文：<https://dev.to/rembou1/google-oauth-with-blazor-aspnet-core-week-26-iea>

# 用 Blazor (0.4)和 ASPNET Core 2.1.1 实现 Google OAuth

ASPNET 核心项目模板通过各种提供者为快速实现 OAuth 提供了一切。但是这些模板都是基于 ASPNET 核心 MVC 的。在这篇博文中，我将解释我是如何修改代码来在 Blazor 应用上实现 Google 认证的。

我的解决方案( [TOSS](https://github.com/RemiBou/Toss.Blazor) )有 3 个项目:

*   客户端:blazor 代码(librairy 针对 netstandard 2.0)
*   服务器:通过 HTTP 服务 RPC 的 ASPNET 核心应用程序(面向 netcoreapp2.1 的应用程序)
*   Shared:客户机和服务器之间的共享类(librairy 针对 netstandard 2.0)

## 服务器配置

首先需要在服务器应用程序上配置 Google OAuth。只需在 ConfigureServices
中添加以下代码

```
services.AddAuthentication()
        .AddGoogle(o =>
          {
              o.ClientId = Configuration["GoogleClientId"];
              o.ClientSecret = Configuration["GoogleClientSecret"];
          }); 
```

Enter fullscreen mode Exit fullscreen mode

*   要获取配置值，请访问[https://docs . Microsoft . com/en-us/aspnet/core/security/authentic ation/social/Google-logins？view = aspnetcore-2.1&tabs = aspnetcore 2x](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/google-logins?view=aspnetcore-2.1&tabs=aspnetcore2x)
*   第一行添加了 ASPNET Core 所需的所有服务(您不会直接需要这些服务，但是您可以在这里找到详细信息
*   第二行在这里添加 google 作为 OAuth 提供者[源代码](https://github.com/ume05rw/AspNetCore.WithFrameworkSource.All.2.0/blob/82ee05dd041aa93cfe4ba07b74dd4d8d1d68b1de/AspNetCore/Security/src/Microsoft.AspNetCore.Authentication.Google/GoogleExtensions.cs)

然后您需要在配置
中设置认证中间件

```
app.UseAuthentication(); 
```

Enter fullscreen mode Exit fullscreen mode

*   你必须把它放在“UseMvc()”之前

## 清单提供商

现在一切都设置好了，让我们从这个过程的第一步开始:列出用户可以使用的身份验证提供者。

在您的 AccountController 上添加此操作

```
[HttpGet, AllowAnonymous]
        public async Task<IActionResult> LoginProviders()
        {
          return Ok((await _signInManager.GetExternalAuthenticationSchemesAsync())
                .Select(s => new SigninProviderViewModel()
                {
                    Name = s.Name,
                    DisplayName = s.DisplayName
                }));
        } 
```

Enter fullscreen mode Exit fullscreen mode

*   需要在您的控制器(signinManager)中注入 _signinManager

这个动作将由客户端的 ajax 调用。你的 blazor 页面将看起来像这样

```
@page "/login"
<h1>Welcome</h1>
<div class="row">
    <div class="col-sm">
        <LoginExternal></LoginExternal>
    </div>
</div>
@inject HttpClient Http;
@inject IUriHelper UriHelper;
<h4>Use another service to log in.</h4>
@foreach (var provider in loginProviders)
{
    <form action="/api/account/externalLogin" method="post">
        <button type="submit"
                class="btn btn-default"
                name="provider"
                value="@provider.Name"
                title="@("Log in using your "+provider.DisplayName+" account")">
            @provider.Name
        </button>
    </form>
}
@functions{
    IEnumerable<SigninProviderViewModel> loginProviders = new SigninProviderViewModel[0];
    protected override async Task OnInitAsync()
    {
        var response = await Http.SendAsync(new HttpRequestMessage(HttpMethod.Get, "/api/account/loginProviders"));
        loginProviders = JsonUtil.Deserialize<List<SigninProviderViewModel>>(await response.Content.ReadAsStringAsync());
        StateHasChanged();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   这将调用我们刚刚创建的动作并将结果绑定到 loginProviders 本地字段，blazor 将负责与 html 的绑定
*   我不确定这里是否需要 StateHasChanged()
*   将显示每个提供商的按钮列表(此时只有谷歌)
*   这是应用程序中唯一的传统表单发布之一，因为下一步将很难在 ajax 中实现，从 UX 的角度来看，这不会改变任何事情(用户浏览器被重定向)

## 重定向

当用户点击一个按钮时，它将执行以下操作

```
[HttpPost, AllowAnonymous]
public async Task<IActionResult> ExternalLogin([FromForm] string provider)
{
    // Request a redirect to the external login provider.
    var redirectUrl = _urlHelper.Action("ExternalLoginCallback", "Account");
    var properties = _signInManager.ConfigureExternalAuthenticationProperties(request.ProviderName, redirectUrl);
    return Challenge(properties, provider);
} 
```

Enter fullscreen mode Exit fullscreen mode

*   第一行是链接，用户将通过其令牌被重定向到该链接(它将被删除，并将给我用户的电子邮件，以便我可以对他进行身份验证)。
*   质询结果将向用户浏览器返回一个 401，并带有登录页面 url (google 的)。

## 登陆页面

在这一点上，用户在谷歌登录页面，他输入他的凭证，并接受给你在谷歌控制台声明的应用程序一些信息。目前，我们只要求您提供电子邮件和姓名，但是您也可以要求您的用户提供其他信息。

对于批准后的登录页面(仅询问一次),用户将登陆以下操作

```
[HttpGet, AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(string returnUrl = null, string remoteError = null)
{
    if (remoteError != null)
    {
        return Redirect("/login");
    }
     var info = await _signInManager.GetExternalLoginInfoAsync();
    if (info == null)
    {
        return null;
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager.ExternalLoginSignInAsync(info.LoginProvider, info.ProviderKey, isPersistent: false, bypassTwoFactor: true);
    if (result == null || result.IsNotAllowed)
    {
        return Redirect("/login");
    }

    if (result.Succeeded)
    {
        return RedirectToLocal(returnUrl);
    }
    // If the user does not have an account, then ask the user to create an account.
    return Redirect("/account/externalLogin");
} 
```

Enter fullscreen mode Exit fullscreen mode

*   令牌不是操作的参数，因为可能有许多提供者，GET 参数将由 httpcontext 中的 signInManager 直接读取。
*   我们仍然需要为这个用户创建一个本地帐户，所以我们将他重定向到“/account/externalLogin”。我们的数据库中的用户帐户将在此时创建。
*   如果用户登录，我们可以将他重定向到主页，我们的控制器中的属性用户将被填充他的信息。

## 账户创建

这一步是用一个像这样的 razor 页面构建的

```
@page "/account/externalLogin"
@inject IHttpClient HttpClient;
@inject IUriHelper UriHelper
<h4>Associate your @model.Provider account.</h4>
<hr />

<p class="text-info">
    You've successfully authenticated with <strong>@model.Provider</strong>.
    Please enter an email address and a login for this site below and click the Register button to finish
    logging in.
</p>

<div class="row">
    <div class="col-md-4">
        <form method="post" >
            <div class="form-group">
                <label for="Email">Email</label>
                <input id="Email" bind="@model.Email" class="form-control" />
            </div>           
            <button type="button" onclick="@ExternalLoginConfirm" class="btn btn-default">Register</button>
        </form>
    </div>
</div>

@functions {

    ExternalLoginConfirmationCommand model = new ExternalLoginConfirmationCommand();
    protected override async Task OnInitAsync()
    {
        var response = await Http.SendAsync(new HttpRequestMessage(HttpMethod.Get, "/api/account/externalLoginDetails"));
        model = JsonUtil.Deserialize<ExternalLoginConfirmationCommand>(await response.Content.ReadAsStringAsync());
        StateHasChanged();
    }
    async Task ExternalLoginConfirm()
    {
        var requestJson = JsonUtil.Serialize(model);
        await _httpClient.SendAsync(new HttpRequestMessage(HttpMethod.Post, "/api/account/externalLoginConfirmation")
        {
            Content = new StringContent(requestJson, System.Text.Encoding.UTF8, "application/json")
        });
        UriHelper.NavigateTo("/");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   我用 get externalLoginDetails 调用，用用户的电子邮件地址预填充表单
*   然后我把表格的内容贴出来。我不处理这里的错误，如果你想看我如何管理验证，请到我的回购。
*   UriHelper。导航到("/")将用户发送到主页。

下面是动作 externalLoginConfirmation
的内容

```
[HttpPost, AllowAnonymous]
public async Task<IActionResult> ExternalLoginConfirmation(ExternalLoginConfirmationCommand command)
{
     // Get the information about the user from the external login provider
    var info = await _signInManager.GetExternalLoginInfoAsync();
    if (info == null)
    {
        throw new ApplicationException("Error loading external login information during confirmation.");
    }
    var user = new ApplicationUser { UserName = command.Email, Email = command.Email, EmailConfirmed = true };
    var result = await _userManager.CreateAsync(user);
    if (result.Succeeded)
    {
        result = await _userManager.AddLoginAsync(user, info);
        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            return Ok();
        }
    }
    return BadRequest();
} 
```

Enter fullscreen mode Exit fullscreen mode

*   我强制 EmailConfirmed 为 true，因为我信任 google，也许我应该使用“info”而不是命令
*   这创建了用户并为他添加了登录信息，你可以为一个用户提供多个外部提供者，但我不会在这里使用它，因为我认为这是一个非常极端的情况。

## 来源

*   [https://Andrew lock . net/introduction-to-authorization-in-ASP-net-core/](https://andrewlock.net/introduction-to-authorisation-in-asp-net-core/)
*   [https://github.com/aspnet/Security](https://github.com/aspnet/Security)
*   [https://blazor.net](https://blazor.net)
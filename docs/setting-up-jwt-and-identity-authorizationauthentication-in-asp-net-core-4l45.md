# 在 ASP 中设置 JWT 和身份授权/认证。网络核心

> 原文：<https://dev.to/coolgoose/setting-up-jwt-and-identity-authorizationauthentication-in-asp-net-core-4l45>

继续我对 ASP 的探索。NET Core，并确保我在我的舒适区之外得到[，我进入了这样一种情况，我希望能够在我的 API 请求中轻松地访问登录的用户信息。](https://dev.to/coolgoose/comment/2kh3)

网上有好几个版本，不幸的是有些已经过时了(主要是因为 ASP。NET Core 2.0 相对较新，在 1.0 和 2.0 之间的特性发生了显著变化)。

我偶然发现的第一个版本推荐扩展 IHttpContextAccessor。如果你不知道，在 C#中你可以使用一个[自定义扩展方法](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/how-to-implement-and-call-a-custom-extension-method)来实现你自己对核心库的扩展。

我出生时的代码看起来像:

```
public static class IHttpContextAccessorExtension
{
    public static string CurrentUser(
        this IHttpContextAccessor httpContextAccessor
    ) {
        return httpContextAccessor.HttpContext.User.FindFirst(JwtRegisteredClaimNames.Sub).Value;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

从技术上来说，这是对用户的回报。因为这是我的声明定义所指定的。(*忽略我只有一个[【重要】令牌](https://github.com/dwyl/learn-json-web-tokens)* )

```
 var claims = new[] {
    new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
    new Claim(JwtRegisteredClaimNames.Sub, user.Id),
}; 
```

Enter fullscreen mode Exit fullscreen mode

[因为](https://github.com/aspnet/Security/issues/1043) [的](https://github.com/aspnet/Security/issues/1703)[事实](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/issues/415)[微软](https://stackoverflow.com/questions/38453895/getting-claims-with-identityserver3)最清楚(是的，所有单独的链接，我保证我会做一个文档拉取请求)我们需要在我们的`Startup.cs` / `ConfigureServices`中添加`System.IdentityModel.Tokens.Jwt.JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Clear();`(最简单的方法是在顶部直接转储)以避免子类型自动转换为`ClaimTypes.NameIdentifier`。

出于某种原因，这在 2.0 中没有得到改进，即使这是一个很好的机会，因为它打破了向后兼容性(希望在 2.1 中有这一点，因为最近的博客帖子表明 2.1 不会遵循 SemVer，可能会引入突破性的变化)。

改变后，您可以重新定义`UserIdClaimType`来使用 Jwt 声明。

```
services.AddIdentity<User, IdentityRole>(options =>
{
    ...
    options.ClaimsIdentity.UserIdClaimType = JwtRegisteredClaimNames.Sub;
}) 
```

Enter fullscreen mode Exit fullscreen mode

老实说，我理解为什么这个会被忽略，因为大多数时候，你的 CurrentUser 方法看起来很可能是:

```
public static class IHttpContextAccessorExtension
{
    public static async Task<User> CurrentUser(
        this IHttpContextAccessor httpContextAccessor,
        UsersService users
        )
    {
        return await users.UserManager.GetUserAsync(httpContextAccessor.HttpContext.User);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

即使您不重置索赔处理，这也是有效的，因为它在幕后为您完成了到`ClaimTypes.NameIdentifier`的转换。我不是一个大粉丝，因为即使约定优于配置是很好的，这对我来说有点太神奇了，因为它也做声明类型转换。

现在，作为一种替代方法，你可以直接在控制器内部调用你的`UserManager`上的`GetUserAsync`方法，因为你可以直接访问`HttpContext.User`(或者`this.User`，我喜欢它更明确一点)。

```
public async Task<IActionResult> Index()
{
    // _users is my injected service that contains the UserManager
    var currentUser = _users.UserManager.GetUserAsync(HttpContext.User);
} 
```

Enter fullscreen mode Exit fullscreen mode

你们是怎么处理的？作为最佳实践，我应该记住什么？
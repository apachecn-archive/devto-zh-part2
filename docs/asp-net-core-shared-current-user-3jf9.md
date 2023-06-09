# ASP。网络核心共享当前用户

> 原文：<https://dev.to/coolgoose/asp-net-core-shared-current-user-3jf9>

作为对解释如何设置 [JWT 和身份](https://dev.to/coolgoose/setting-up-jwt-and-identity-authorizationauthentication-in-asp-net-core-4l45)的前一篇文章的快速更新，我已经更新了`currentUser`方法以使用依赖注入。

```
namespace Craidd.Extensions {
    public static class IHttpContextAccessorExtension
    {
        public static async Task<User> CurrentUser(this IHttpContextAccessor httpContextAccessor)
        {
            IUsersService users = httpContextAccessor.HttpContext.RequestServices.GetService(typeof(IUsersService)) as IUsersService;
            return await users.UserManager.GetUserAsync(httpContextAccessor.HttpContext.User);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，IUsersService 是一个包含 dbContext、UserManager 和 SignInManager 的包装器。

```
namespace Craidd.Services
{
    public class UsersService: IUsersService
    {
        private readonly AppDbContext _dbContext;
        private readonly UserManager<User> _userManager;
        private readonly SignInManager<User> _signInManager;

        public UsersService(
            AppDbContext context,
            UserManager<User> userManager,
            SignInManager<User> signInManager
        )
        {
            _dbContext = context;
            _userManager = userManager;
            _signInManager = signInManager;
        }

        public UserManager<User> UserManager => _userManager;
        public SignInManager<User> SignInManager => _signInManager;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，在您的控制器中，您可以通过注入 httpContext 轻松地访问该方法。
我目前正在用它快速返回 [Nuxt.js 认证模块](https://auth.nuxtjs.org/)T3 的登录用户数据

```
[HttpGet]
public IActionResult Index()
{
    return Json(new { user = _httpContext.CurrentUser() });
} 
```

Enter fullscreen mode Exit fullscreen mode
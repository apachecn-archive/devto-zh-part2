# 在 Aspnet Core 2.1 上使用 Mediatr 第 25 周

> 原文：<https://dev.to/rembou1/using-mediatr-on-aspnet-core-21-week-25-1pe>

# 在 ASPNET Core 2.1 上使用 Mediatr

## CQRS

CQRS 的意思是命令和查询责任分离。这是一种设计模式或开发实践，在这种模式或实践中，您将领域分为两个部分:写模型和读模型。

### 写模式(命令)

这是所有更新/插入/删除发生的地方。命令对系统有持久的影响，它改变系统状态。大多数情况下，这部分是最复杂的，因为你有最多的业务逻辑和最复杂的类。当你发出一个命令，你不期待任何回报。为什么？因为之前使用的读取模型(用于显示表单)禁止用户发出不成功的命令。

在 web 应用程序/ rpc via http api / rest api 中，命令由 POST 启动，即使它删除数据库中的行。因为在 CQRS，不像在现实世界中那样键入命令:用户不想删除预订记录，他想取消预订(这意味着删除记录，但也发送邮件、退款、提醒管理人员……)

### 读取模型(查询)

这是所有选择发生的地方。查询是向用户显示系统状态的一种方式。如果你只是发出查询，系统状态不会改变。这一部分是所有性能工作完成的地方，因为它是系统中使用最多的部分:在预订一次住宿之前，您会查看多少家酒店？

在通过 http api / rest api 的 web 应用程序/ rpc 中，命令由 GET 启动。唯一的例外是技术原因(请求太大)。这是一个 GET，所以用户可以在任何时候获得这个查询结果。

### 为什么这样分割:

*   解耦:通过将你领域一分为二，你可以使它们更加独立。读取模型将始终与写入模型相耦合，至少当您更新它时是这样，但是写入模型不会关心您如何读取数据，它只是试图尽可能地保持一致，并遵循业务专家的观点(CQRS 与 DDD 紧密相关)。域事件的引发将使写模型完全忽略读模型。
*   领域跟踪:拥有一个尽可能接近领域专家想法的代码库是非常重要的。领域专家不考虑数据库行(CRUD)，他们考虑动作(命令)、终端用户屏幕(查询)和事件/触发器。
*   性能:如果您将两个域都存储在一个为其优化数据存储中，您将能够获得更好的性能。例如，您将写模型存储在 RDMS 上，这样您就可以确保系统状态是一致的，并且您可以将读模型存储在 NoSQL 数据库(如 Redis)上，因为当您想要向用户显示一些数据时，您不想执行 150 个连接。

## mediator

如果你想实现 CQRS，你必须做大量的样板代码来连接消息和处理它们，或者你可以使用一个包来完成所有这些工作。

Mediart 是由[吉米·博加德](https://twitter.com/jbogard)创建的. Net 开源项目( [GitHub](https://github.com/jbogard/MediatR) ， [Nuget](https://www.nuget.org/packages/MediatR/) )。它是中介模式的一个实现，这个模式的目的是将消息从处理中分离出来。

## 用法

### 安装

Mediatr 可在 nuget 上获得。net standard 2.0 项目(它也可用于。net 框架项目)。您只需在软件包管理器控制台上输入以下命令:

> 安装包中介器

### 与 Ioc 容器的配线

为了链接消息和处理，MediatR 需要一个 IoC 容器。如果你认为 Asp.Net 核心 2.1 中包含的已经足够了，你必须为这个容器安装这个包:

> 安装包中介器。扩展. Microsoft.DependencyInjection

这个包是第一个包的附属品，所以只有这个包就够了。并且在启动时像这样配置 MediatR。配置服务

```
services.AddMediatR(typeof(Startup)); 
```

Enter fullscreen mode Exit fullscreen mode

我使用 Startup 类型，因此 MediatR 将扫描我的所有 aspnet 核心项目，以实现所需的接口。

可以在这里查看我的代码: [Startup.cs](https://github.com/RemiBou/Toss.Blazor/blob/master/Toss/Toss.Server/Startup.cs) 。我还添加了一个对我的客户机(blazor app)和服务器之间的共享程序集的引用，在那里我得到了我的命令和查询的定义。

### 发布命令/查询

一旦所有东西都被引导，你就可以创建你的第一条消息了。我为登录用户创建了一个。这种情况很奇怪，因为我不更新数据库，但在我看来，它仍然是系统的全局状态，无论如何，在 Mediatr 中，命令和查询之间没有太大区别。这是我的命令，非常简单

```
public class LoginCommand : IRequest<LoginCommandResult>
    {
        [Required]
        public string UserName { get; set; }

        [Required]
        [DataType(DataType.Password)]
        public string Password { get; set; }

        [Display(Name = "Remember me?")]
        public bool RememberMe { get; set; }
    } 
```

Enter fullscreen mode Exit fullscreen mode

*   这是一个 C#对象，实现了一个通用的“标记接口”IRequest(没有实现的方法)
*   我有 DataAnnotations 验证属性来验证这个消息
*   IRequest 的泛型参数是处理程序返回的结果的类型。这里是一个有结果的命令，只是因为用户可能输入了错误的登录名或密码。我可以设法没有结果，因为这是一个命令，但这样做更容易。

要处理这个命令，你需要两样东西。首先是中介实例 imediat:

```
private readonly IMediator _mediator;
public AccountController(IMediator mediator)
{
    _mediator = mediator;
} 
```

Enter fullscreen mode Exit fullscreen mode

处理是这样定义的

```
public class LoginCommandHandler : IRequestHandler<LoginCommand, LoginCommandResult>
{
    private readonly SignInManager<ApplicationUser> _signInManager;
    private readonly ILogger _logger;

    public LoginCommandHandler(SignInManager<ApplicationUser> signInManager, ILogger logger)
    {
        _signInManager = signInManager;
        _logger = logger;
    }

    public async Task<LoginCommandResult> Handle(LoginCommand request, CancellationToken cancellationToken)
    {
        var result = await _signInManager.PasswordSignInAsync(request.UserName, request.Password, request.RememberMe, lockoutOnFailure: false);
        if (result.Succeeded)
        {
            _logger.LogInformation("User logged in.");
            return new LoginCommandResult() { IsSuccess = true };
        }
        if (result.RequiresTwoFactor)
        {
            return new LoginCommandResult() { Need2FA = true };
        }
        if (result.IsLockedOut)
        {
            _logger.LogWarning("User account locked out.");
            return new LoginCommandResult() { IsLockout = true };
        }
        else
        {
            return new LoginCommandResult() { IsSuccess = false };
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码非常简单。我只需要实现“IRequestHandler <logincommand logincommandresult="">”。</logincommand>

那你这样叫它

```
[HttpPost]
[AllowAnonymous]
public async Task<IActionResult> Login(LoginCommand command)
{
    var result = await _mediator.Send(command);
    if (!result.IsSuccess)
    {
        if (result.IsLockout)
            return Redirect("/lockout");
        ModelState.AddModelError("UserName", "Invalid login attempt.");
        return BadRequest(ModelState);

    }
    //if (result.Need2FA)
    //{
    // return RedirectToAction("/loginWith2fa");
    //}
    return Ok();
} 
```

Enter fullscreen mode Exit fullscreen mode

*   第一行调用中介器并异步获得结果(在这一行下可以有 IO，所以最好异步完成所有事情)
*   然后我解析结果，大部分代码来自 ASP.NET 的原始安全模板。我评论了 2FA 部分，因为我还没有实现它。
*   我重用 ModelState 来发送错误，因为这是在出现错误请求(400)时客户端(rpc via http)所期望的格式。这是我的控制器操作中最复杂的代码，大多数情况下是这样的:

```
var res = await mediator.Send(command);
if (res.IsSucess)
    return new OkResult();
return new BadRequestObjectResult(res.Errors); 
```

Enter fullscreen mode Exit fullscreen mode

那么为什么要有一个控制器呢？主要是因为它有一些事情要做:路由/ http 动词/重定向，在某些情况下，比如登录部分，一些更复杂的事情。

### 事件

我使用 Azure Table 进行数据存储。这项服务不提供开箱即用的索引，所以你必须自己做。有了 event，我可以安全地分离写模型(插入新元素)和读模型(基于某些标准查询元素)。一个事件很像一个命令，但是实现通知，这里没有结果，因为调用者不关心接下来会发生什么:

```
public class TossPosted : INotification {
    public string TossId{get;set;}
} 
```

Enter fullscreen mode Exit fullscreen mode

*   一次投掷是我的申请上的一个信息(像一个帖子或什么的)
*   我只是把 tossId，我可以把整个折腾的内容，但我选择这样做

然后，为了处理事件并创建索引，您要像这样实现 INotificationHandler】

```
public class TossTagIndexHandler : INotificationHandler<TossPosted> {
    ///missing code : dependency injection, azure table init ...
    public Task Handle(TossPosted notification, CancellationToken cancellationToken) {       
        await mainTable.CreateIfNotExistsAsync();

        var toss = await _mediator.Send(new TossContentQuery(notification.TossId);

        var tasks = HashTagIndex
            .CreateHashTagIndexes(toss)//this create an entry for each hashtag entered into a toss
            .Select(h => mainTable.ExecuteAsync(TableOperation.Insert(h))
            .ToList();
        await Task.WhenAll(tasks);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   有了它，我可以添加任意多的索引策略。不过，在部署它时，我必须创建一些批处理。
*   我可以在另一个服务器或云服务上使用其他实现来处理这个问题。
*   我也可以将它解耦更多，事件处理将只是推送新的 CreateTossTagIndexCommand，但这样做代码太多，没有任何好处。###验证

您在这里没有看到任何命令验证，这很正常，我使用 aspnet core 2.1 的新 ApiController，如果我的一个属性没有被考虑，它会处理返回一个带有模型状态的 400 错误请求。这对我来说已经足够了。

## 结论

Mediatr 确实帮助我分离了所有的命令/查询/事件处理。这也有助于保持我的控制器很小，因为在我看来，这是 mvc 应用程序中最大的问题之一。

## 来源

*   [https://lostechies . com/jimmybogard/2015/05/05/cqrs-with-mediator-and-auto mapper/](https://lostechies.com/jimmybogard/2015/05/05/cqrs-with-mediatr-and-automapper/)
*   [https://github.com/jbogard/MediatR/wiki](https://github.com/jbogard/MediatR/wiki)
*   [https://docs . Microsoft . com/en-us/aspnet/core/release-notes/aspnetcore-2.1？view=aspnetcore-2.1](https://docs.microsoft.com/en-us/aspnet/core/release-notes/aspnetcore-2.1?view=aspnetcore-2.1)
*   [https://martinfowler . com/bliki/cqrs . html](https://martinfowler.com/bliki/CQRS.html)
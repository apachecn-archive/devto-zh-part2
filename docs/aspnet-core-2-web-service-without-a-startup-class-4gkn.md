# 没有启动类的 ASP.NET 核心 2 web 服务

> 原文：<https://dev.to/kspeakman/aspnet-core-2-web-service-without-a-startup-class-4gkn>

# 更新 2018-07-06

这篇文章的内容因为 ASP.NET 核心不支持多次运行`Configure(...)`而有所减少。相反，将只运行最后一个配置功能。

然而，我稍微调整了一下我的助手，以便单独跟踪所需的配置函数，并且只在构建时应用它们。下面是 JWT OAuth 的更新示例。

```
type AppBuilder = IApplicationBuilder -> IApplicationBuilder

let setAuth0Auth audKey authKey (host : IWebHostBuilder, configs : AppBuilder list) =
    ( host
        .ConfigureServices(fun (context : WebHostBuilderContext) (services : IServiceCollection) ->
            let config = context.Configuration
            services
                .AddAuthentication(fun options ->
                    options.DefaultAuthenticateScheme <- JwtBearerDefaults.AuthenticationScheme
                    options.DefaultChallengeScheme <- JwtBearerDefaults.AuthenticationScheme
                )
                .AddJwtBearer(fun options ->
                    options.Audience <- config.[audKey]
                    options.Authority <- config.[authKey]
                )
            |> ignore
        )
    , configs @ [ fun app -> app.UseAuthentication() ]
    )

let run (host : IWebHostBuilder, configs : AppBuilder list) =
    host
        .Configure(fun (app : IApplicationBuilder) ->
            configs
            |> List.fold (fun x f -> f x) app
            |> ignore
        )
        .Build()
        .Run() 
```

Enter fullscreen mode Exit fullscreen mode

否则下面的内容还是可以用的。

* * *

几天来，我一直在敲打键盘，试图正确配置 ASP.NET Core 2 web 服务，使其在不使用启动类的情况下运行。我终于让它工作了。

## 为什么？

启动类是各种关注点、不必要的抽象和不必要的记忆惯例的一个非常惊人的混合体。不仅如此，使用不同的方法实际上可以更容易地创建和维护服务配置。

## 如何？

我将从最终结果开始，逆向工作。这是我服役时的样子。

```
[<EntryPoint>]
let main args =
    let basePath = Directory.GetCurrentDirectory()

    new WebHostBuilder()
    |> setConfig basePath args
    |> setSerilogLogger
    |> removeServerHeader // bye X-Powered-By
    |> setAuth0Auth Keys.Audience Keys.Authority
    |> setCorsPolicy
    |> setRouteHandler MyApi.routes
    |> run

    0 // exit code 
```

Enter fullscreen mode Exit fullscreen mode

看着这段代码，我要配置的东西(希望)是显而易见的。当然，这些是我必须自己创建的辅助函数。但是因为这些函数是独立的，所以它们可以在其他项目中重用。它们还允许我通过添加或删除一行来选择加入或退出各种功能。例如，我可以用`setLogaryLogger`代替`setSerilogLogger`。

诚然，构建器已经有了流畅的方法——比如`Configure`和`ConfigureServices`。你也可以为他们做同样的声明。但是，有些功能不是仅通过一种 fluent 方法配置的。例如，`setAuth0Auth`函数必须在内部使用一些方法来正确配置认证:

```
let setAuth0Auth audienceKey authorityKey (host : IWebHostBuilder) =
    host
        .ConfigureServices(fun (context : WebHostBuilderContext) (services : IServiceCollection) ->
            let config = context.Configuration
            services
                .AddAuthentication(fun options ->
                    options.DefaultAuthenticateScheme <- JwtBearerDefaults.AuthenticationScheme
                    options.DefaultChallengeScheme <- JwtBearerDefaults.AuthenticationScheme
                )
                .AddJwtBearer(fun options ->
                    options.Authority <- config.[authorityKey]
                    options.Audience <- config.[audienceKey]
                )
                |> ignore
        )
        .Configure(fun builder -> builder.UseAuthentication() |> ignore) 
```

Enter fullscreen mode Exit fullscreen mode

将它组织成自己的函数准确地封装了设置 auth 需要做的事情，并且只有 auth。因为它只使用传递给它的内容，所以这个函数对于其他服务来说是非常可重用的。(有点遗憾的是，构建器使用了突变，但这不太可能是个问题，因为这段代码只在启动时运行。)

弄清楚如何创建无启动服务的棘手部分是访问您可能需要的东西，比如`IConfiguration`和`IHostingEnvironment`状态。我找到的每一条建议都是:“把它们注入一个创业班”🙃。但事实证明，用 [`ConfigureService`过载](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder.configureservices?view=aspnetcore-2.1)中的一个很容易找到它们。带`WebHostBuilderContext`的那个是钥匙。它将配置和宿主对象都作为属性。你可以在上面的例子中看到，我用它来获取`IConfiguration`对象。

从`Configure`中获取这些项目更具挑战性，因为它没有将这些作为直接属性的重载。但是，你仍然可以在提供的`IApplicationBuilder`中找到它们(和其他东西)。

```
webHostBuilder
    .Configure(fun (builder : IApplicationBuilder) ->
        let services = builder.ApplicationServices
        let env = services.GetService<IHostingEnvironment>()
        let config = services.GetService<IConfiguration>()
        ...
    ) 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

几个助手函数对降低配置服务的复杂性大有帮助。当它们可重用来创建其他服务时，那就更好了！
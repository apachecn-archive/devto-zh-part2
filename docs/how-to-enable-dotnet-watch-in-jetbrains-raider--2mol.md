# 如何在 Jetbrains 中启用 dotnet watch

> 原文：<https://dev.to/coolgoose/how-to-enable-dotnet-watch-in-jetbrains-raider--2mol>

嗨，伙计们，在用 C#和。我意识到我很怀念让调试器“监视”文件变化的日子。

标准的做法是。网芯 2.0 就是设置 [dotnet-watch](https://github.com/aspnet/DotNetTools/tree/rel/2.0.0/src/Microsoft.DotNet.Watcher.Tools) 。好像是从。NET Core 2.1 不需要这一步，所以少了一步。
在那之前，你可以按照[官方教程](https://docs.microsoft.com/en-us/aspnet/core/tutorials/dotnet-watch)来设置 dotnet-watch。

为了让它在莱德工作，你需要做一些简单的步骤。

1.  在运行/调试配置下，点击*加号*并勾选**运行外部工具**
    [![Run/Debug Configurations](../Images/d1b7a50bf0b0154f591f27f76f9b70fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wrqP6-Xb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6pyxfzo3itpf7gnj4n2o.PNG)

2.  设置 dotnet-watch，并确保设置正确的*工作目录*宏`$ProjectFileDir$`
    [![External tool](../Images/0925ad7a0dade53872ee9ed2559447eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xxhnMMkj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i5l5v67nsk743bbjdnzu.PNG)

**更新**
由于在运行命令之前，没有在 Rider 中设置 ASPNETCORE_ENVIRONMENT 的隐式方法，这里有一个快速的方法，向您的程序添加一个参数来启用这个开关。

修改*program . cs*T2】

```
 public static IWebHost BuildWebHost(string[] args)
        {
            var config = new ConfigurationBuilder().AddCommandLine(args).Build();
            var enviroment = config["environment"] ?? "Development";

            return WebHost.CreateDefaultBuilder(args)
                          .UseEnvironment(enviroment)
                          .UseStartup<Startup>()
                          .Build();
        } 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以运行

`dotnet watch run --environment="Development"`

(或任何其他环境变量)，并根据您的运行配置进行特定的环境设置。
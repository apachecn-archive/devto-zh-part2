# 开始使用。网络核心

> 原文：<https://dev.to/dotnet/getting-started-with-net-core-2bf9>

。NET 是一个免费的开源开发平台，用于构建各种应用程序。从网络到移动应用，任何东西都可以在 Mac、Linux 和 Windows 上构建。

稍微讨论一下那个声明...

*   开源——看看[https://github.com/dotNET](https://github.com/dotnet)中所有与. net 相关的库。每一个都代表了平台的一部分。一些重点回购包括:

    *   在 https://docs.microsoft.com/dotnet[可以看到的](https://docs.microsoft.com/dotnet)[单据](https://github.com/dotnet/docs)
    *   -。NET 命令行工具
    *   [CoreCLR](https://github.com/dotnet/coreclr) -。NET 核心运行时，基本库，包含垃圾收集器和基本类型。
    *   [ASP.NET](https://github.com/aspnet)-ASP.NET 和实体框架项目。[文件](https://github.com/aspnet/Docs)可在[https://docs.microsoft.com/aspnetcore](https://docs.microsoft.com/aspnetcore)、[信号员](https://github.com/aspnet/SignalR)、 [Mvc](https://github.com/aspnet/Mvc) 、[实体框架](https://github.com/aspnet/EntityFrameworkCore)、[红隼](https://github.com/aspnet/KestrelHttpServer)
*   跨平台——不管你喜欢在什么操作系统上开发；的。NET Core SDK 和工具可以按照你喜欢的方式安装开发。如果是使用 CLI 体验，文本编辑器如 [Visual Studio Code](https://code.visualstudio.com/?WT.mc_id=devto-twitter-shboyer) 或 Windows 上带有 [Visual Studio](https://www.visualstudio.com/?WT.mc_id=devto-twitter-shboyer) 的全 IDE 或 MacOS 上带有[Visual Studio for Mac](https://www.visualstudio.com/vs/mac/?WT.mc_id=devto-twitter-shboyer)；。NET Core 在这些情况下都是可用的。

### 如果只是想尝试一下呢？

不安装任何东西...

浏览浏览器教程中的

[![Try .NET In Browser](../Images/c208c001cf2dee41a08cd81d80b269d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5s4_km66--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/Dbo6zhQVAAAPeI4.jpg)

### 获取比特

前往[https://dot.net](https://dot.net/?WT.mc_id=devto-twitter-shboyer)并点击[下载](https://www.microsoft.com/net/download/?WT.mc_id=devto-twitter-shboyer)链接以获得与您的操作系统相关的 SDK。

另一个选择是安装 Visual Studio 2017，。NET Core 是作为[ASP.NET 和 web 开发工作量](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/?WT.mc_id=devto-twitter-shboyer)的一部分安装的。

[![Visual Studio Workloads](../Images/2795bdd9ae644ba8c853cf1356ff2865.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iqK2ZXd3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.visualstudio.com/wp-content/uploads/2017/06/ASP.NETandWebDevelopment.png)

### 使用 CLI 创建快速应用程序

打开您喜欢的命令提示符(bash、zsh、powershell 等)并键入`dotnet new`来查看可用的项目模板。

[![dotnet new templates](../Images/19e2d7f1e31ea251c0374b5bd6fbe29e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O9-M_aix--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DbpGht6V0AAGt4m.jpg)

创建一个名为`devto`
的新 web 应用程序

```
dotnet new web --name devto 
```

Enter fullscreen mode Exit fullscreen mode

输出显示应用程序已创建，依赖包已恢复。

```
The template "ASP.NET Core Empty" was created successfully.
This template contains technologies from parties other than Microsoft, see https://aka.ms/template-3pn for details.

Processing post-creation actions...
Running 'dotnet restore' on devto/devto.csproj...
  Restoring packages for /mnt/c/Users/shboyer/play/devto/devto.csproj...
  Generating MSBuild file /mnt/c/Users/shboyer/play/devto/obj/devto.csproj.nuget.g.props.
  Generating MSBuild file /mnt/c/Users/shboyer/play/devto/obj/devto.csproj.nuget.g.targets.
  Restore completed in 3.26 sec for /mnt/c/Users/shboyer/play/devto/devto.csproj.

Restore succeeded. 
```

Enter fullscreen mode Exit fullscreen mode

现在将 cd 放入文件夹并运行 web 应用程序。

```
cd devto 
dotnet run 
```

Enter fullscreen mode Exit fullscreen mode

在您的机器上浏览到 [http://localhost:5000](http://localhost:5000) ，现在您已经创建了您的第一个。NET 核心网络应用程序只使用命令行。不需要在你选择的任何操作系统上安装大量的 IDE。

### 资源

*   查看所有关于[ASP.NET 核心](https://docs.microsoft.com/aspnet/?WT.mc_id=devto-twitter-shboyer)和[的开源文档。https://docs.microsoft.com/](https://docs.microsoft.com/dotnet/core/?WT.mc_id=devto-twitter-shboyer)[的网芯](https://docs.microsoft.com/?WT.mc_id=devto-twitter-shboyer)。

*   [https://dot.net](https://dot.net/?WT.mc_id=devto-twitter-shboyer)

*   如有任何问题，请通过 twitter [@spboyer](https://twitter.com/spboyer) 联系我
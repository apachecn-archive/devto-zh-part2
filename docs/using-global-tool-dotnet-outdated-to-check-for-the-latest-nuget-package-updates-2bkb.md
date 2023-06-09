# 使用全局工具“dotnet outdated”检查最新的 NuGet 包更新

> 原文：<https://dev.to/dotnet/using-global-tool-dotnet-outdated-to-check-for-the-latest-nuget-package-updates-2bkb>

[![Using global tool](img/b616ea80c9f466a20d1ecf202c57e522.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P4jiJqU7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/conteimg/2018/07/Screen-Shot-2018-07-02-at-5.27.43-PM.png)

就在不久前的[。NET Core June 年 6 月更新](https://blogs.msdn.microsoft.com/dotnet/2018/06/22/net-core-2-1-june-update/?WT.mc_id=none-devto-shboyer)掉线，我刚为 CI/CD build 设立的[ASP.NET 工坊需要更新到](https://tattoocoder.com/learning-devops-and-building-the-asp-net-core-workshop-up-to-date/) [2.1.1(发布说明)](https://github.com/dotnet/core/blob/master/release-notes/2.1/2.1.1.md)。

*   在 http://dot.net/从[下载页面获取最新的](https://www.microsoft.com/net/download/all?WT.mc_id=none-devto-shboyer) [2.1.1 运行时](https://www.microsoft.com/net/download/all?WT.mc_id=none-devto-shboyer) | [2.1.301 SDK](https://www.microsoft.com/net/download/all?WT.mc_id=none-devto-shboyer)

浏览所有的`save-points`，当然还有主要的`/src`，挑战在于知道哪些 NuGet 包发生了变化，哪些版本是最新的等等。，待更新。

这是一个伟大的。NET 全球工具， [`dotnet-outdated`](https://github.com/jerriep/dotnet-outdated) 进场。

*   在文档中阅读更多关于[点网全球工具的信息](https://docs.microsoft.com/dotnet/core/tools/global-tools?WT.mc_id=none-devto-shboyer)

使用安装该工具。NET CLI - `dotnet tool install --global dotnet-outdated`，那么我可以在解决方案(。sln)级别或项目(。csproj)级别和类型`dotnet outdated`。

[![Using global tool](img/cda8cdbec8e540785d934f97e3735b94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SRYL2Kcp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/conteimg/2018/07/Screen-Shot-2018-07-02-at-5.27.43-PM-1.png)

输出显示了每个 NuGet 包、当前使用的版本和最新版本。如果你也想寻找预发布版本，你可以选择通过`-pr`。

另一个不错的选项是`-vl`或版本锁定选项，它允许在检查过时的包时锁定主要或次要版本。

使用默认值更新每个项目非常快，对*进行更改。csproj 文件，然后让 CI/CD 进程在提交时施展魔法。

这是另一个很好的例子。NET 全球工具可以让体验更好。我希望看到这个工具给出一个自动更新的选项。它在[https://github.com/jerriep/dotnet-outdated](https://github.com/jerriep/dotnet-outdated)开源。
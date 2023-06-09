# 使用 F#和 ASP.NET 核心构建 web 服务

> 原文：<https://dev.to/samueleresca/build-web-service-using-f-and-aspnet-core-52l8>

*原贴于[https://samueleresca.net](https://samueleresca.net)T3】*

下面这篇文章的目的是使用 F#和 ASP.NET 核心构建 web 服务，使用[长颈鹿库](https://github.com/dustinmoris/Giraffe)。因此，我们将构建一些 JSON APIs，从数据源获取数据，并通过 HTTP 公开它们。

在开始之前，我邀请您阅读这篇关于用 F#进行 Web 开发的文章。

你可以在 github 上找到以下演示:[https://github.com/samueleresca/Blog.FSharpOnWeb](https://github.com/samueleresca/Blog.FSharpOnWeb)

## 设置项目

设置一个长颈鹿项目非常简单。首先，让我们从添加长颈鹿项目模板开始:
`dotnet new -i "giraffe-template::*"`

前面的命令安装了一些项目模板，以便构建 Giraffe web 应用程序。之后，您可以通过运行`dotnet new giraffe`创建一个新的 Giraffe 应用程序。

该命令将创建一个包含一些文件的项目。最重要的是`Program.fs`文件，它包含主机、路由引擎和服务的设置:
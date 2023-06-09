# 带 Wercker 的 CI/CD

> 原文：<https://dev.to/rafalpienkowski/cicd-with-wercker--51k>

## 简介

如果您正在开始一个新项目，您需要执行的步骤之一是设置持续集成(CI)和持续部署(CD)流程。你很有可能熟悉像[詹金斯](https://jenkins.io/)、 [Visual Studio Team Services](https://www.visualstudio.com/team-services/) 或 [Octopus](https://octopus.com/) 这样的 CI 工具。我想向你介绍一些新的东西。这个工具被命名为 [Wercker](http://www.wercker.com/) 。

在我看来，Wercker 可能会让那些正在使用或考虑开始一个基于 Docker 的新项目的开发人员感兴趣。它将自动创建包含您的应用程序的 Docker 映像，执行单元和集成测试，并在最后将新创建的映像发布到 [DockerHub](https://hub.docker.com/) 。我认为 Wercker 最显著的优势在于它是免费的(至少在写这篇文章的时候是这样)。

顺便说一句，如果你不熟悉 DockerHub，简而言之，它是 Docker 图片的仓库。通过与 GitHub 的类比，您可以将您的图像拉入和推送至一个专用的存储库。你可以在由[杂志](https://dev.to/ech0server)创建的[这篇文章](https://dev.to/ech0server/creating-a-docker-image-and-upload-it-to-docker-hub)中阅读更多关于手动将图像拖到 DockerHub 的信息。

在本文中，我将向您展示如何为用 ASP.Net Core 2 编写的应用程序创建 CI/CD 流程。我们将基于提供的脚手架创建一个简单的 Web API 应用程序。CI/CD 流程将包括以下步骤:

*   Docker 图像创建
*   应用程序构建
*   单元测试
*   集成测试
*   将新创建的图像推送到我们的 DockerHub 存储库

在我们开始之前，您需要设置以下内容:

*   。你机器上安装的 Net Core 2 SDK
*   DockerHub 帐户
*   GitHub 账户

完成这些零步骤后，我们就可以开始设置 CI/CD 流程了。

## Github 资源库

首先，我们将在 GitHub 上为我们的项目建立一个存储库。如果你周围没有任何项目，你可以把我创建的克隆到你的本地机器:

```
git clone https://github.com/rafalpienkowski/wercker-ci-cd.git 
```

Enter fullscreen mode Exit fullscreen mode

我想在下一节稍微描述一下我的应用程序。

## 申请

我创建了一个新的。基于 web API 模板的. Net 核心 Web 应用程序。为此，我们只需运行 dotnet 命令。我们将把应用程序命名为“SimpleApi”。

```
dotnet new webapi --name SimpleApi 
```

Enter fullscreen mode Exit fullscreen mode

我对脚手架生成的代码做了一些修改。我用一个简单的“内存中”实现添加了 IValueRepository 接口。我添加了简单的单元和集成测试，以便在构建过程中进一步使用。

单元测试检查在调用 ValuesController 到 Get 方法的过程中，我们是否从 IValueRepository 调用 GetAll()方法，我们是否对控制器值返回的执行断言。测试如下所列:
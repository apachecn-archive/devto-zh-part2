# 使用 Docker 部署 ASP.NET 核心 2 应用

> 原文：<https://dev.to/kingsor/deploy-aspnet-core-2-apps-withdocker-598n>

*最初发布于[https://kingsor.github.io/](https://kingsor.github.io/2018/04/30/deploy-asp-net-2-apps-with-docker/)2018 年 4 月 30 日*

这篇文章是写给 self 的，关于我学习使用 Docker 的第一步。我开始在一个容器中部署 ASP.NET 核心 web api。

## 先决条件

*   坞站( [Windows](https://docs.docker.com/docker-for-windows/install/) 、 [Mac](https://docs.docker.com/docker-for-mac/install/) 、[坞站工具箱](https://docs.docker.com/toolbox/overview/)
*   ASP.NET 酷睿 2.0 SDK ( [此处](https://www.microsoft.com/net/download/windows))

## 用 ASP.NET 2.0 构建 web api

我按照这个教程创建了一个简单的待办事项 Web API:[用 ASP.NET 核心和 Visual Studio for Windows 创建一个 Web API](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-2.1)。

为了能够在没有任何特定客户端的情况下测试 web api，我使用 [NSwag](https://github.com/RSuter/NSwag) 包为项目添加了 [Swagger](https://swagger.io/) 支持。

我按照这个教程完成了这个任务:[开始使用 NSwag 和 ASP.NET 核心](https://docs.microsoft.com/en-us/aspnet/core/tutorials/getting-started-with-nswag?view=aspnetcore-2.1)。

NSwag 项目提供了从现有的 ASP.NET Web API 控制器生成 Swagger 规范的工具，并提供了一个嵌入式的 [Swagger UI](https://swagger.io/swagger-ui/) 来与 Web API 进行交互。

最终的项目是托管在 GitHub 上的 [TodoCoreWebApi](https://github.com/kingsor/TodoCoreWebApi) 。

## 添加 Dockerfile

下面是用于构建 docker 映像的 docker 文件。

```
 FROM microsoft/dotnet:2.0-sdk AS build-env
WORKDIR /app

# copy csproj and restore as distinct layers
COPY ./TodoCoreWebApi/*.csproj ./
RUN dotnet restore

# copy everything else and build
COPY ./TodoCoreWebApi/. ./
RUN dotnet publish -c Release -o out /p:PublishWithAspNetCoreTargetManifest="false"

# build runtime image
FROM microsoft/dotnet:2.0-runtime
WORKDIR /app
COPY --from=build-env /app/out .
# not valid for Heroku
# ENTRYPOINT ["dotnet", "TodoCoreWebApi.dll"]
# this is working
CMD ASPNETCORE_URLS="http://*:$PORT" dotnet TodoCoreWebApi.dll 
```

Enter fullscreen mode Exit fullscreen mode

为了理解构建 Docker 映像的来龙去脉，我遵循了本教程。网络核心应用。我使用了[上可用的](https://github.com/dotnet/dotnet-docker/tree/master/samples) [aspnetsample](https://github.com/dotnet/dotnet-docker/tree/master/samples/aspnetapp) 项目中的 [Dockerfile](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/Dockerfile) 。GitHub 上的 NET Core Docker 示例库。

## 构建 Docker 图像

在项目的根目录(Dockerfile 所在的位置)运行这个命令:

```
 docker build -t todo-core-webapi . 
```

Enter fullscreen mode Exit fullscreen mode

构建过程完成后，您可以使用以下命令运行映像:

```
 docker run -it --rm -p 8080:80 --name TodoCoreWebApi todo-core-webapi 
```

Enter fullscreen mode Exit fullscreen mode

并在`http://localhost:8080`测试 web api

## 将图像推送到 DockerHub

首先，你需要[注册一个 Docker ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) 。

之后，您可以[在 DockerHub](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) 上创建新的存储库。

我的 Docker ID 是 [neetpiq](https://hub.docker.com/u/neetpiq/) ，我生成的图像的存储库是 [neetpiq/todo-core-webapi](https://hub.docker.com/r/neetpiq/todo-core-webapi/) 。

现在，我需要标记我的图像。

```
docker tag todo-core-webapi neetpiq/todo-core-webapi:1.2 
```

Enter fullscreen mode Exit fullscreen mode

```
docker tag todo-core-webapi neetpiq/todo-core-webapi:latest 
```

Enter fullscreen mode Exit fullscreen mode

终于可以推送新版的我的形象了。

```
docker push neetpiq/todo-core-webapi:1.2 
```

Enter fullscreen mode Exit fullscreen mode

```
docker push neetpiq/todo-core-webapi:latest 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以运行以下命令来测试这个 web api 应用程序映像(最新的):

```
 docker run -d -p 8080:80 --name TodoCoreWebApi neetpiq/todo-core-webapi 
```

Enter fullscreen mode Exit fullscreen mode

或者如果你想要一个特定的版本:

```
 docker run -d -p 8080:80 --name TodoCoreWebApi neetpiq/todo-core-webapi:1.2 
```

Enter fullscreen mode Exit fullscreen mode

## 备注

### Windows 用户

如果您在 Windows 上使用 boot 2 Docker([Docker Toolbox](https://docs.docker.com/toolbox/overview/))，请注意以下几点:

boot2docker VirtualBox 中的 Linux VM 将 VM 实例中的 c/Users 目录映射到 Windows 中的 C:\Users 文件夹。因此，确保您的 worker 源代码位于 C:\Users 下的一个文件夹中，然后 cd 到 VM 上下文中的那个文件夹(在 Boot2Docker 终端中)并从那里运行它。

## 资源

*   [构建 Docker 图像。网络核心应用](https://docs.microsoft.com/en-us/dotnet/core/docker/building-net-docker-images)
*   [向坞站中心推坞站图像](https://github.com/dotnet/dotnet-docker/blob/master/samples/dotnetapp/push-image-to-dockerhub.md)
*   [ASP.NET Core Docker 生产样品](https://github.com/dotnet/dotnet-docker/tree/master/samples/aspnetapp)
*   [在 Heroku 上部署 ASP.NET 核心 2.0 应用](https://blog.devcenter.co/deploy-asp-net-core-2-0-apps-on-heroku-eea8efd918b6)
*   [将存储库映像推送到 Docker Hub](https://docs.docker.com/docker-hub/repos/#pushing-a-repository-image-to-docker-hub)
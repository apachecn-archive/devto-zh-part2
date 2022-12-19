# 。网核在码头上的树莓码头

> 原文：<https://dev.to/dustinsoftware/net-core-in-docker-on-a-raspberry-pi-4ha6>

### 2018 年 5 月 20 日更新:本帖现已作废，截止日期。网芯 2.1！详情请看[这篇](https://blogs.msdn.microsoft.com/dotnet/2018/05/07/announcing-net-core-2-1-rc-1/)博文。

* * *

### 过期信息如下图:

默认情况下，会给你一个在 x86/x64 架构上运行良好的项目。然而，RPI 运行在 arm32v7 上。我们需要做一些小的改动来支持 Docker 中的这个架构。

### ARM 有 ASP.NET 内核支持，那为什么不直接在 Docker 中工作呢？

默认情况下，新项目包括微软的元包[。AspNetCore.All](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage) ，它对哪些库已经在运行时目标上做了一些假设。为了与 Docker 一起工作，这意味着运行时映像需要是[微软/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) 。但是，这就产生了一个问题！

没有用于 microsoft/aspnetcore Docker 映像的 ARM 包。 [dockerfile](https://github.com/aspnet/aspnet-docker/blob/3fb758ced1e1735d604b30dfc2ff554d2b7a2f5d/2.0/jessie/runtime/Dockerfile#L5) 表明这个映像的预制二进制文件是从 CDN 中提取的，但是 armv7 似乎没有任何预制二进制文件。

### 使用不同的基础图像

幸运的是， [ARMv7 已经有了一个 docker 图像。NET Core runtime](https://github.com/dotnet/dotnet-docker/blob/master/2.0/runtime/stretch/arm32v7/Dockerfile) ，我们可以用它作为基础映像！现在我们只需显式声明 AspNetCore 依赖项。

### 入门

这个项目的一个工作实例在[这个 GitHub repo](https://github.com/dustinsoftware/RPI-Docker-ASPNetCore)

*   用`dotnet new react`创建一个新项目
*   移除项目文件中的`Microsoft.AspNetCore.All`引用，并替换为以下引用:

```
 <PackageReference Include="Microsoft.AspNetCore" Version="2.0.0" />
    <PackageReference Include="Microsoft.AspNetCore.Mvc" Version="2.0.0" />
    <PackageReference Include="Microsoft.AspNetCore.SpaServices" Version="2.0.0" />
    <PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" /> 
```

Enter fullscreen mode Exit fullscreen mode

*   验证应用程序在使用`dotnet run`时仍然有效
*   创建一个 docker 文件，如下所示:

```
FROM microsoft/aspnetcore-build:2 AS build-env
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

# Build runtime image
FROM microsoft/dotnet:2.0-runtime
WORKDIR /app
COPY --from=build-env /app/out ./
ENTRYPOINT ["dotnet", "DpmWebsite.dll"]
EXPOSE 5000 
```

Enter fullscreen mode Exit fullscreen mode

然后，将这个 dockerfile 文件复制到一个名为`Dockerfile.arm32`的新文件中。这应该是相同的，除了我们要将运行时映像改为:

```
FROM microsoft/dotnet:2.0.0-runtime-stretch-arm32v7 
```

Enter fullscreen mode Exit fullscreen mode

这允许应用程序从 ARM 版本的。NET SDK。

现在，确保本地构建工作:

```
docker build -t netcore-local .
docker run -d -p 5000:5000 netcore-local 
```

Enter fullscreen mode Exit fullscreen mode

一旦确认工作正常，构建一个特定于 ARM 的映像:

```
docker build -t yourdockerid/rpi-net-core -f Dockerfile.arm32 .
docker push yourdockerid/rpi-net-core 
```

Enter fullscreen mode Exit fullscreen mode

最后，提取图像并在您的树莓派上运行它！

```
docker pull yourdockerid/rpi-net-core
docker run --restart=always -d -p 5000:5000 yourdockerid/rpi-net-core 
```

Enter fullscreen mode Exit fullscreen mode

如果有问题，获取图像的 ID，然后使用`docker logs (containerid)`

### 感谢阅读！

这是一个有趣的练习。看到……真是令人兴奋。NET 平台变得更容易访问，为整个团队让这成为现实而脱帽致敬！
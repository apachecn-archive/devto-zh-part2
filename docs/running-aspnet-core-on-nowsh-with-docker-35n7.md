# 用 Docker 在 now.sh 上运行 ASP.NET 核心

> 原文：<https://dev.to/jonstodle/running-aspnet-core-on-nowsh-with-docker-35n7>

最近喜欢上了【T2 now . sh。这是一个超级好的托管服务，让我们部署静态网站，节点驱动的网站或 Docker 容器使用一个单一的命令在您的终端:`now`。你只需在终端上写下`now`，你的网站就会在几秒钟内上线:

[![Demo of deploying to now.sh](../Images/2fcf7138b1fc7548a5feda05cb84beee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hwsDvCpt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.jonstodle.com/content/images/2018/09/now-sh-demo-2.gif)

原视频片段不到 1 分 45 秒！

我对现在的最大的兴趣是他们的码头工人支持。这意味着我将能够轻松运行 ASP.NET 的核心应用，最重要的是，成本低廉。

我开始时充满希望和快乐。我有一个`Dockerfile`，它有一个构建步骤和一个只包含最终编译位的最终容器。我将编译好的位打包在官方的 ASP.NET 核心 Docker 镜像中:

```
FROM microsoft/dotnet:2.1-sdk AS build

WORKDIR /source

COPY . .

RUN dotnet restore

RUN dotnet publish -c Release -o /output

FROM microsoft/dotnet:2.1-aspnetcore-runtime

EXPOSE 80

WORKDIR /app

COPY --from=build /output .

CMD ["dotnet", "NowShDemo.dll"] 
```

但这让我碰到了一个不可预见的障碍。最终 Docker 图像的大小不能大于 100 兆字节。哎哟！

[![The Docker image is too large](../Images/ea7101b2e65e421438e0028358456856.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uGa_Bjj5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/content/images/2018/09/docker-image-too-large-1.png)

幸运的是， *now.sh* 在他们那端做了一些“压缩”(即*组装图像*的步骤)，这意味着他们看到的 Docker 图像实际上比从`Dockerfile`本地构建时产生的“原始”图像要小一些。他们报告的图像是 116.7 兆字节，而我的磁盘上是 258 兆字节。

代码只是默认的 ASP.NET 核心 MVC 模板的内容，我已经超过了大小限制。怎么办？

第一个技巧是使用较小的 Linux 基础映像。通过向下滚动官方 [Docker 注册表](https://store.docker.com/community/images/microsoft/dotnet)的`microsoft/dotnet`页面，你会发现有基于 Alpine 的运行时(和 SDK)映像。Alpine 以其非常小的图像尺寸而闻名，并且很受 Docker 的欢迎。阿尔卑斯的形象是 **4** 兆！这就是整个操作系统！

我将我最终的 Docker 图像改为使用 Alpine 变体:

```
FROM microsoft/dotnet:2.1-sdk AS build

WORKDIR /source

COPY . .

RUN dotnet restore

RUN dotnet publish -c Release -o /output

FROM microsoft/dotnet:2.1-aspnetcore-runtime-alpine

EXPOSE 80

WORKDIR /app

COPY --from=build /output .

CMD ["dotnet", "NowShDemo.dll"] 
```

这将我磁盘上的图像大小更改为 167 兆字节。那就减少了 91 兆字节。当我将这个映像部署到 *now.sh* 时，部署没有问题。 *now.sh* 报告图像大小为 76.8 兆字节，远低于限制。

快乐时光！

酪我能做得更好吗？我能设法把图像变得更小吗？我打赌我能！

从的 2.1 版开始。NET 核心，Alpine 是一个完全支持的运行时目标。这意味着您将能够将您的应用程序编译成一个*自包含部署*，这将为 Alpine、您的代码和。NET 核心运行时。

通过在我的 *csproj* 文件中的`TargetFramework`标签下添加`<RuntimeIdentifier>alpine-x64</RuntimeIdentifier>`，我能够编译我的代码在 64 位 Alpine 上运行。我还需要说明，当我运行`dotnet publish`时，我正在为 alpine 构建。

。NET Core 需要在 Alpine 上安装一些工具和/或应用程序才能正常运行。您可以设置映像来安装这些依赖项，但幸运的是，微软提供了一个安装了依赖项的 Docker 映像。这个 Docker 映像是在构建我之前使用的 Alpine 运行时映像时使用的。

我的 docker 文件现在看起来像这样:

```
FROM microsoft/dotnet:2.1-sdk AS build

WORKDIR /source

COPY . .

RUN dotnet restore

RUN dotnet publish -c Release -o /output -r alpine-x64

FROM microsoft/dotnet:2.1-runtime-deps-alpine

EXPOSE 80

WORKDIR /app

COPY --from=build /output .

CMD ["./NowShDemo"] 
```

您还会注意到，底部的`CMD`命令改为调用当前目录中的可执行文件，而不是通过`dotnet`。

这张新图片在我的磁盘上有 115 兆字节。又省了 52 兆！now.sh 报告其大小为 51.5 兆字节。这很好！

我能更进一步吗？我当然可以！

当您从默认模板创建一个 ASP.NET 核心 MVC 项目时，它包含一个名为`Microsoft.AspNetCore.All`的包。这是一个所谓的*元包*，它只包含对其他包的引用。在这种情况下，那些其他的包就是组成 ASP.NET 核心的*所有*包。

如果我去掉那个包罗万象的包，只添加我所用的部分，我可能会多删除几兆字节。

经过一些尝试和错误之后，我能够把我的 *csproj* 从这个:

```
<ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App"/>
</ItemGroup> 
```

对此:

```
<ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore" Version="2.1.3" />
    <PackageReference Include="Microsoft.AspNetCore.Hosting" Version="2.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.HttpsPolicy" Version="2.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.Mvc" Version="2.1.2" />
</ItemGroup> 
```

我没有把 ASP.NET 核心的所有 T2 都拉进来，而是只使用了我的代码运行明确需要的部分。通过拆分套餐，你可能会节省更多，但我对这个结果很满意。

新的 Docker 镜像在磁盘上只有 105 兆字节，这又少了 10 兆字节。在将该映像部署到 *now.sh* 之后，它报告大小为 47.4 兆字节。新纪录！

在这个实验的过程中，我能够从 258 兆字节的 Docker 图像到 105 兆字节的图像。这还不到原始大小的一半，而且没有删除我的任何代码！部署到 *now.sh* 的映像从 116.7 兆字节缩减到 47.4 兆字节！

请注意，这不是我运行的真实网站；这只是默认的 ASP.NET 核心 MVC 模板的代码。但是它可能会给你一些如何缩小 Docker 图片的灵感。

如果您有任何问题或意见，请随时在 [Twitter](https://twitter.com/jonstodle) 上联系我。
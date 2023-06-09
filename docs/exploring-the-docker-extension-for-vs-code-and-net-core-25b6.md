# 探索 VS 代码的 Docker 扩展。网络核心

> 原文：<https://dev.to/azure/exploring-the-docker-extension-for-vs-code-and-net-core-25b6>

[![Exploring the Docker Extension for VS Code and .NET Core](img/129b8579b0297d8ad2535bb7a2a210e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nM45S0eH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/conteimg/2018/10/Screen-Shot-2018-10-04-at-11.56.04-AM.png)

VS 代码的 [Docker 扩展最近被更新以更好地支持。NET 核心应用程序，体验超级好！](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker&WT.mc_id=devto-blog-shboyer)

该扩展已经为构建你的容器、将图像推送到 Docker Hub、 [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/?WT.mc_id=devto-blog-shboyer) 以及运行特定图像或标记图像等任务提供了强大的支持。然而，将 Dockerfile 添加到 ASP.NET 核心应用程序并未更新以支持[微软/dotnet](https://hub.docker.com/r/microsoft/dotnet/) 基本映像。

Docker 扩展可以单独下载，也可以作为 VS 代码的 [Azure 扩展包的一部分，该扩展包提供了许多其他扩展，可以在不离开编辑器的情况下使用许多云服务。](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack&WT.mc_id=devto-blog-shboyer)

首先，我将使用创建一个快速 Razor Pages 应用程序。NET CLI，转到输出目录并在 VS 代码中打开。

```
> dotnet new razor -o dotnet-docker-vscode
> cd dotnet-docker-vscode
> code . 
```

Enter fullscreen mode Exit fullscreen mode

使用该扩展，我现在可以使用命令 Pallete、CMD + Shift + P (MacOS)或 Ctrl + Shift + P (Win)添加所需的 Dockerfile，键入 **Docker** 并显示选项。

[![Exploring the Docker Extension for VS Code and .NET Core](img/4d3f439df78998ea2b7d05550145f6d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tn35GT_w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0szn2homzw85mddr4deg.gif)

该扩展为 ASP.NET 核心 2.1 添加了适当的 Dockerfile】

```
FROM microsoft/dotnet:2.1-aspnetcore-runtime AS base  
WORKDIR /app  
EXPOSE 80

FROM microsoft/dotnet:2.1-sdk AS build  
WORKDIR /src  
COPY ["dotnet-docker-vscode.csproj", "./"]  
RUN dotnet restore "./dotnet-docker-vscode.csproj"  
COPY . .  
WORKDIR "/src/."  
RUN dotnet build "dotnet-docker-vscode.csproj" -c Release -o /app

FROM build AS publish  
RUN dotnet publish "dotnet-docker-vscode.csproj" -c Release -o /app

FROM base AS final  
WORKDIR /app  
COPY --from=publish /app .  
ENTRYPOINT ["dotnet", "dotnet-docker-vscode.dll"] 
```

Enter fullscreen mode Exit fullscreen mode

再次使用扩展，我们可以建立图像。当这样做的时候，它确实需要一个标签，在这种情况下，我将在它前面加上我的 Azure 容器注册表名称，以便以后推送。

[![Exploring the Docker Extension for VS Code and .NET Core](img/0090f5970de6ce3d540f0fde86dac0a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6HfBZswP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aclnm7l517w4zlmq7yxy.gif)

展开**图像**部分，我们可以看到现在可用的【shayne.azurecr.io/dotnet-docker-vscode T2】图像。右键单击条目显示您期望的选项；与 Docker CLI 中最常用的命令保持一致。

[![Exploring the Docker Extension for VS Code and .NET Core](img/c79f8d1e7d1101c41d03f7d95d1027b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G_uRNBNY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/conteimg/2018/10/Screen-Shot-2018-10-04-at-11.26.40-AM.png)

我在这里可以选择的选项是将图片推送到我的 Azure 容器注册中心。

> 如果推送图像时出现错误，您可能需要使用`az acr login --name {name of your registry}`在终端中向您的 ACR 进行认证。

现在我的私有注册表中有了这个映像，我可以使用 App Service 扩展在 Linux 上使用 [App Service 创建一个应用程序实例，并推送我的容器。](https://docs.microsoft.com/en-us/azure/app-service/containers/)

[![Exploring the Docker Extension for VS Code and .NET Core](img/4eb7b3dce54dd811b43e9dd601d4f6ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h4xvkYCD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tbk3ttfq5ywt3bncng59.gif)

所有这些任务我们都可以在不离开 Visual Studio 代码的情况下完成。有很多很棒的扩展，可以用来开发你喜欢的任何语言或栈。因为。NET Core，Azure 和 Docker 开发下面是我最喜欢的一些。

*   [Azure 扩展包](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack&WT.mc_id=devto-blog-shboyer)
*   [VS 代码的 Docker 扩展](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker&WT.mc_id=devto-blog-shboyer)
*   [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens&WT.mc_id=devto-blog-shboyer)
*   [mssql](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql&WT.mc_id=devto-blog-shboyer)
*   [Kubernetes](https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools&WT.mc_id=devto-blog-shboyer)

### 挑战

对你来说这是一个挑战...去下载扩展。做点什么，发表出来，让我知道。想听听大家对延伸和体验的反馈。
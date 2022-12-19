# 用 C#构建一个简单的带有 Sidecars 的 PaaS(来自《设计分布式系统》一书)

> 原文：<https://dev.to/elemarjr/building-a-simple-paas-with-sidecars-from-designing-distributed-systems-book-in-c-3gnl>

激发这篇文章的例子来自[布兰登·伯恩斯](https://twitter.com/brendandburns)(Kubernetes 项目的联合创始人)的优秀著作**设计分布式系统**。顺便说一下，这本书在[这里](https://azure.microsoft.com/en-us/resources/designing-distributed-systems/en-us/?wt.mc_id=AID627576_QSG_SCL_224576)是免费的。

在书中，布伦丹·伯恩斯分享了一个令人兴奋的[边车模式](https://docs.microsoft.com/en-us/azure/architecture/patterns/sidecar)的用法。

> sidecar 模式不仅可用于适应和监控。它还可以作为一种手段，以简化、模块化的方式实现应用程序的完整逻辑。例如，想象围绕 git 工作流构建一个简单的平台即服务(PaaS)。一旦部署了这个 PaaS，只需将新代码推送到 Git 存储库，就会导致代码被部署到正在运行的服务器上。我们将看到 sidecar 模式如何使构建这个 PaaS 变得非常简单。
> 
> [..]在 sidecar 模式中有两个容器:主应用程序容器和 sidecar。在我们简单的 PaaS 应用程序中，主容器是实现 web 服务器的 Node.js 服务器。Node.js 服务器会进行检测，以便在更新新文件时自动重新加载服务器。这是通过 nodemon 工具完成的。
> 
> sidecar 容器与主应用程序容器共享一个文件系统，并运行一个简单的循环，将文件系统与现有的 Git 存储库同步。

这是本书中可用的图示。

[![](../Images/788eb96a9cca2d1933c3bbcc00805674.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qsCX6hmZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/02/sidecar.png)

我的目标是使用 ASP.net 核心创建这个例子。这个源代码可以在我的 GitHub 上找到。

[![](../Images/df4cf3598317fc06ce4a10a37cc261cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MRUgoKmK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/02/dotnet_sidecar.png)

## 创建一个“当文件更新时自动重新加载”的服务器

我没有努力创造一个复杂的例子。它只是一个基于“空 web app”模板的新项目。

为了让它运行起来，在 docker 上，我遵循了 Nate McMaster 的指示。

这里是应用程序的[docker 文件。](https://github.com/ElemarJR/sidecar-paas-dotnetcore/blob/master/application/Dockerfile)

```
FROM microsoft/aspnetcore-build:2.0 ENV DOTNET\_USE\_POLLING\_FILE\_WATCHER 1 WORKDIR /code/app EXPOSE 80 COPY Application.csproj . COPY Directory.Build.props . COPY NuGet.config . RUN dotnet restore ENTRYPOINT dotnet watch run 
```

*/code/app* 目录将被映射到主机的卷。

## 与 Git 边车的同步

与 Git 同步的逻辑与 Brendan Burns 提出的相同。

```
#!/bin/bash while true; do git pull origin master sleep 10 done 
```

边车的 [*Dockerfile* 也很简单。](https://github.com/ElemarJR/sidecar-paas-dotnetcore/blob/master/sidecar/Dockerfile)

```
FROM microsoft/aspnetcore-build:2.0 WORKDIR /repo RUN apt-get update && apt-get install -y dos2unix COPY bash-paas.sh /entrypoint.sh RUN dos2unix /entrypoint.sh && apt-get --purge remove -y dos2unix && rm -rf /var/lib/apt/lists/\* ENTRYPOINT /entrypoint.sh 
```

同样， */repo* 目录将被映射到主机的卷(包含应用程序容器使用的存储库)。

实用程序 dos2unix 用于修复行尾。

## 作曲

最后，为了运行这个例子，我创建了一个基本的 [Docker 编写配置。](https://github.com/ElemarJR/sidecar-paas-dotnetcore/blob/master/docker-compose.yml)

```
version: '3' services: application: image: application build: context: application dockerfile: Dockerfile volumes: - ./application:/code/app ports: - "5000:80" sidecar: image: sidecar build: context: sidecar dockerfile: Dockerfile volumes: - .:/repo 
```

## 如何在你的机器上测试

要让它在您的机器上运行(假设您已经安装了 docker):

1.  叉我的回购
2.  在你的电脑上克隆分叉回购
3.  构建它( *docker-compose build)*
4.  Run ( *坞站-复合* up)
5.  导航到*本地主机:5000*

现在，当您更改分叉回购时，sidecar 将拉取更新，应用程序容器将自动重启 web 应用程序。

[![](../Images/c3234d8e90944a7c76f0947e48fa9360.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XlwsOx_w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/02/running.png)

## 最后一句话

我不是“集装箱专家”所以，你可能会发现很多改进的机会。请随时给我您的反馈。对吗？

在 C# 中用 Sidecars 构建一个简单的 PaaS 的帖子[首先出现在](http://www.elemarjr.com/en/2018/02/building-a-simple-paas-with-sidecars-from-designing-distributed-systems-book-in-c/) [Elemar JR](http://www.elemarjr.com/en) 上。
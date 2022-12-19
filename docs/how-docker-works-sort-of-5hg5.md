# Docker 是如何工作的…算是吧

> 原文：<https://dev.to/dotnetcoreblog/how-docker-works-sort-of-5hg5>

在我们开始之前提醒一下，这是我在我的博客上写的一篇文章的转贴。NET Core 博客-可以在[dotnetcore.gaprogman.com](https://dotnetcore.gaprogman.com)找到

* * *

最近写了一点关于[入门 docker](https://dotnetcore.gaprogman.com/2018/05/17/getting-started-with-docker-an-introduction/) 的内容。上周，我写了一篇关于如何着手[dockering a . NET 核心应用](https://dotnetcore.gaprogman.com/2018/05/24/how-to-dockerise-net-core-apps/)的文章。本周，我想我应该谈谈它是如何工作的。首先，这不会是一个关于 docker 如何做它的事情的深入分析

*如果你真的想，你可以[通过源代码](https://github.com/docker)找到所有的内容*

但是对于那些开始使用 docker 和容器的人来说，这已经足够了。

另外，如果你想幽默地看待 docker，没有比这款 XKCDT3[更好的了](https://xkcd.com/1988/)

### 什么是容器？

容器是应用程序运行所依赖的一切。它是运行时的完整描述，以及应用程序的所有依赖项

*外部 dll、包等。*

容器的伟大之处在于，至少在 docker 世界中，你的应用程序需要的整个堆栈可以在你的服务器上作为一个单独的进程运行。我在本系列的第一部分提到过，容器不是一项新技术。事实上，他们的历史可以追溯到 20 世纪 70 年代

*[这里有一个链接](https://blog.aquasec.com/a-brief-history-of-containers-from-1970s-chroot-to-docker-2016)链接到一篇讲述他们历史的精彩博文*

##### 崩溃恢复

为什么这是一件大事？好吧，假设您有一个安装了 IIS 的 Windows 服务器，并且您的大量应用程序正在该服务器上运行。如果操作系统崩溃或由于某种原因变得无响应，那么在该服务器上运行的所有应用程序都会被关闭。其中，如果容器中的某些东西崩溃或变得没有响应，那么您丢失的唯一东西就是该容器中的应用程序。

##### 开机时间

另一个好处是启动时间。在我们前面的例子中(窗口服务器、IIS、AppPools 等。)，我们必须等待 Windows 服务器重新启动，IIS 提供端口等，应用程序池启动，然后您的应用程序启动。现在，有了足够强大的服务器，应该不到 10-15 秒。但是用集装箱，几乎是瞬间的。这是因为当您创建容器所基于的 docker 映像时，准备运行时的困难部分已经完成。说到这个

### 集装箱？图像？什么？

我做的有点落后了。我应该先讨论什么是容器和图像。哦，好吧，我想没有比现在更好的时机了

*除了早些时候，我猜*

##### 图像

首先是图像，它很好。docker 图像是一个基于纯文本文件(称为 dockerfile)的文件，它描述了 docker 将如何构建您的图像。这可以是应用程序的构建或运行时堆栈。在本系列博文的[前一部分，我们为](https://dotnetcore.gaprogman.com/2018/05/24/how-to-dockerise-net-core-apps/) [dwCheckApi](https://github.com/GaProgMan/dwCheckApi) 构建了一个图像。other 文件描述了您的映像所基于的其他映像，它还描述了从哪里获取应用程序和依赖项的二进制文件。让我们再次看看我们为 dwCheckApi 创建的 dockerfile:

```
FROM microsoft/dotnet:2.1-sdk-alpine AS build
# Set the working directory witin the container
WORKDIR /src

# Copy all of the source files
COPY *

# Restore all packages
RUN dotnet restore ./dwCheckApi/dwCheckApi.csproj

# Build the source code
RUN dotnet build ./dwCheckApi/dwCheckApi.csproj

# Ensure that we generate and migrate the database 
WORKDIR ./dwCheckApi.Persistence
RUN dotnet ef database update

# Publish application
WORKDIR ..
RUN dotnet publish ./dwCheckApi/dwCheckApi.csproj --output "../../dist"

# Copy the created database
RUN cp ./dwCheckApi.Persistence/dwDatabase.db ./dist/dwDatabase.db

# Build runtime image
FROM microsoft/dotnet:2.1-aspnetcore-runtime-alpine AS app
WORKDIR /app
COPY --from=build /dist .
ENV ASPNETCORE_URLS http://+:5000

ENTRYPOINT ["dotnet", "dwCheckApi.dll"] 
```

除了被称为“划痕”的特殊图像之外

*如《从零开始》*

所有的图像都建立在其他图像的基础上。dwCheckApi 的 docker 映像基于微软提供的一个("[Microsoft/dot net:2.1-SDK-alpine](https://github.com/dotnet/dotnet-docker/blob/master/2.1/sdk/alpine3.7/amd64/Dockerfile)")，它本身基于微软提供的另一个("[Microsoft/dot net:2.1-runtime-deps-alpine 3.7](https://github.com/dotnet/dotnet-docker/blob/master/2.1/runtime-deps/alpine3.7/amd64/Dockerfile)")；它基于 docker 提供的另一个图像(“ [alpine:3.7](https://github.com/gliderlabs/docker-alpine/blob/61c3181ad3127c5bedd098271ac05f49119c9915/versions/library-3.7/x86_64/Dockerfile) ”)，后者基于另一个名为“scratch”的图像(本身与 docker 捆绑在一起)。在这一句话中有很多文本，所以这里是相同的信息，但以图形方式呈现:

[![dwCheckApi image make up](../Images/e11ffc227059f4e682a496f000e5c7cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Her0M5a6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aurmn2b0m4pk51x2vtlx.jpg)

*我们不惜代价*

当我们告诉 docker 用下面的命令构建我们的映像时:

```
docker build . 
```

我们告诉它(通过 dockerfile)在构建之前获取构建我们基础映像的所有“层”(一直到“alpine:3.7”)。如果您观察控制台输出，就可以看到这种情况

[![Download Step](../Images/247b90f707414707ea82e1ee25d2c2e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gClw9vQM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hvf5muuizu01cwkzktd4.jpg)

在上面的屏幕截图中，docker 正在下载 dwCheckApi 的图像所依赖的所有图像。这些图像中的每一个都将作为单独的层添加到 docker 图像缓存中。

### 层？

(几乎)你在 dockerfile 中所做的一切都被存储为一个单独的层，然后这些层被组合起来创建一个图像。每个 docker 命令的结果将创建一个层，并且每个层将由校验和在内部标记，该校验和表示该命令直接导致的变化。例如(暂时回到 dwCheckApi)，下面的 docker 命令:

```
# Restore all packages
RUN dotnet restore ./dwCheckApi/dwCheckApi.csproj 
```

告诉 docker 运行一个. NET 核心恢复操作。此操作完成后，docker 将:

*   看看发生的变化
*   创建一个代表这些变化的校验和
*   将这些变化存储在我们的新图像中
*   将校验和作为标签应用于它们

*这比那要复杂一点，但现在你只需要知道这些*

如果您告诉 docker 构建您的映像，您应该得到类似于下面的输出:

```
Step 11/29 : RUN dotnet restore ./dwCheckApi/dwCheckApi.csproj --force --no-cache
 ---> Running in 56ec006e6f2f
  Restoring packages for /build/dwCheckApi.DAL/dwCheckApi.DAL.csproj...
  Restoring packages for /build/dwCheckApi.Common/dwCheckApi.Common.csproj...
  Installing System.Data.Common 4.3.0.
  Installing SQLitePCLRaw.core 1.1.5.
  Installing Microsoft.EntityFrameworkCore.Relational 2.0.0-preview1-final.
  Installing Microsoft.Data.Sqlite.Core 2.0.0-preview1-final.
...
  Installing Microsoft.Build 15.7.0-preview-000011-1378327.
  Installing Microsoft.NETCore.DotNetHostPolicy 2.0.0.
  Restore completed in 29.21 sec for /build/dwCheckApi/dwCheckApi.csproj.
Removing intermediate container 56ec006e6f2f
 ---> 3dcfa76ef35d 
```

我已经剪掉了大部分。净恢复箔条

但是，如果您再次运行相同的命令，输出将类似于以下内容:

```
 Step 11/29 : RUN dotnet restore ./dwCheckApi/dwCheckApi.csproj --force --no-cache
 ---> Using cache
 ---> 3dcfa76ef35d 
```

*不要被包含的- _no-cache* 所迷惑，因为那是. NET 核心交换机，与 docker 没有任何关系

我们可以看到，docker 已经检测到恢复操作的输出将与上一次运行的输出相同，所以它没有执行它，而是加载缓存的输出。利用这一点对于优化你的 docker 图片非常有用

*我们将在本系列的下一部分中讨论的一些内容*

但是如果这就是图像，那么容器是什么呢？

### 集装箱？

容器是图像的运行实例。你想要多少容器都可以

*只要你有可用的内存*

它们可以运行相同或不同的映像。当您准备在容器中运行您的映像时，您首先需要确保它在构建期间已经被标记。

##### 标签？

我们已经见过标签了。在 dwCheckApi dockerfile 文件的最顶端，我们说:

```
FROM microsoft/dotnet:2.1-sdk-alpine AS build 
```

它告诉 docker 从标记为“2.1-sdk-alpine”的“microsoft/dotnet”存储库中获取 docker 映像。标签就像一个标签，如果你想在不同的环境下提供不同的 docker 图像，它们会很有用。例如，如果您看一看。网络核心

*结束于[微软回购互联网图片](https://hub.docker.com/r/microsoft/dotnet/)T3】*

您将会看到其中一些被标记为用于 Windows，一些被标记为用于不同风格的 Linux，以及不同版本的 SDK 和运行时。例如，我可以看到以下组合:

*在撰写本文时，该列表是正确的*

*   带 SDK 2.1 版的 Debian Stretch
*   Debian Jessie 与 SDK 2.1 版
*   Debian Jessie 与 SDK 版本 1.19
*   带有 SDK 版本 2.1 的 Windows Nano 服务器
*   带有 SDK 2.0 的 Windows Server

*这还不是全部*

这些映像的构建方式不同，安装的软件也不同，具有不同的功能和要求。根据您如何设置您的服务器，以及您的本地构建管道是什么，您将希望选择带有匹配标记的图像。但是为什么我刚才说:

> 以及您的本地构建管道是什么

好吧，如果你将使用 docker 和 containerisation 作为你的 DevOps 计划的一部分，那么你将需要使用它在本地构建和测试。否则，你怎么能绝对肯定你创建的容器实际上工作。

* * *

### 回集装箱...有点儿

构建一个图像并为其提供一个标签非常简单。以 dwCheckApi 为例，我们可以在构建图像时使用以下命令来标记图像:

```
docker build . -t dwcheckapi:latest 
```

这个命令告诉 docker 构建我们的 dockerfile 并用“dwcheckapi:latest”标记它，

*我们只能在 docker 图像的标签中使用小写字符*

我们可以通过发出以下命令来检查我们构建的映像:

```
docker image ls 
```

这可能会返回类似下面的内容:

```
 REPOSITORY          TAG                             IMAGE ID            CREATED             SIZE
dwcheckapi          latest                          a879054aefb1        About an hour ago   168MB
microsoft/dotnet    2.1-sdk-alpine                  c751b3a7f4de        2 weeks ago         1.46GB
microsoft/dotnet    2.1-aspnetcore-runtime-alpine   4933ffee8f5b        2 weeks ago         163MB 
```

*第一行包含 dwCheckApi* 的容器细节

在上面的列表中有三个图像:

*   dwcheckapi:最新
*   微软/点网:2.1-sdk-alpine
*   Microsoft/dot net:2.1-aspnetcore-runtime-alpine

第一个映像将托管和运行 dwCheckApi，但其他映像是什么呢？它们是构建映像(“Microsoft/dot net:2.1-SDK-alpine”)和运行时映像(“Microsoft/dot net:2.1-aspnetcore-runtime-alpine”)。其中的第一个(“Microsoft/dot net:2.1-aspnetcore-runtime-alpine”)仅用于构建我们的应用程序。dockerfile 文件中的相关行是:

```
FROM microsoft/dotnet:2.1-sdk-alpine AS build
# Set the working directory witin the container
WORKDIR /src

# Copy all of the source files
COPY *

# Restore all packages
RUN dotnet restore ./dwCheckApi/dwCheckApi.csproj

# Build the source code
RUN dotnet build ./dwCheckApi/dwCheckApi.csproj

# Ensure that we generate and migrate the database 
WORKDIR ./dwCheckApi.Persistence
RUN dotnet ef database update

# Publish application
WORKDIR ..
RUN dotnet publish ./dwCheckApi/dwCheckApi.csproj --output "../../dist"

# Copy the created database
RUN cp ./dwCheckApi.Persistence/dwDatabase.db ./dist/dwDatabase.db 
```

这些行是那些使用“Microsoft/dot net:2.1-SDK-alpine”图像的行。此后，就不再需要它了，因为我们随后为应用程序定义了运行时，并将应用程序的已发布版本复制到其中:

```
# Build runtime image
FROM microsoft/dotnet:2.1-aspnetcore-runtime-alpine AS app
WORKDIR /app
COPY --from=build /dist .
ENV ASPNETCORE_URLS http://+:5000

ENTRYPOINT ["dotnet", "dwCheckApi.dll"] 
```

因为最终图像的“入口点”是在我们定义为“app”的部分中定义的；“Microsoft/dot net:2.1-aspnetcore-runtime-alpine”和我们从“build”部分复制的文件是唯一包含在结果映像中的文件。“Microsoft/dot net:2.1-aspnetcore-runtime-alpine”重 163MB，dwCheckApi 的已发布应用程序、包和数据库占用了另外 5MB 的空间。因此得到的映像是 168MB，尽管我们不得不使用 1.46GB 的映像来构建它。这意味着运行我们的应用程序的容器需要 168MB 的内存来托管和运行我们的应用程序。

### 回到容器(适当)

可以使用以下命令在容器中运行标记图像:

```
docker run --rm -p 5000:5000 dwcheckapi:latest --name running-dwcheckapi-instance 
```

这告诉 docker 创建一个容器

*你会记得，这只是你机器上的一个进程*

并在其中运行图像。作为设置的一部分，docker 可以在 docker 应用空间内创建一个单独的虚拟网络，并在那里托管您的容器。最棒的是，你的容器不会和你系统上的其他容器出现在同一个网络上——就容器而言，它们可能运行在完全不同的机器上。

##### 举个例子

假设您有两个应用程序:

*   用户界面
*   一个 API

为了简单起见，我们还假设 API 不需要访问外部世界。将它们装入容器并在 docker 中运行后，您可能会得到如下结果:

[![docker network example](../Images/23228ea670808eb1252e52df8392085d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WIr2MXFk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zbbwrloq5g8d19lrkoxs.jpg)

从右到左，我们有:

*   更广泛的网络(局域网/广域网/互联网
*   防火墙
*   服务器(显示猫的图像)
*   docker 网络(托管在带有 cat 映像的服务器上)
*   用户界面容器
*   API 容器

在这种情况下，我们有一个只有一个入口点(由 UI 容器提供)的 docker 网络。这意味着进入服务器的消息只能通过 UI 容器到达 API 容器——有效地将它与外界隔离开来

*有些情况下你可以访问 API 容器——但是它们非常特殊*

您仍然需要在服务器上使用反向代理，但是有一些应用程序(其中大部分已经被 dockerised)也可以为您处理所有这些。

* * *

### 结论

这些信息应该足够让你开始更好地理解 docker。我已经介绍了 docker 文件、图像和容器。我甚至很少接触 docker 网络。关于 docker 如何工作的更多信息，我想看看下面的链接:

*   史蒂夫·戈登的码头工人。NET 开发者幻灯片
*   [MSDEVShow 插曲- Docker 和 Kubernetes 与丽塔·张](https://msdevshow.com/2018/04/docker-and-kubernetes-with-rita-zhang/)
*   [基础知识–Docker、容器、虚拟机管理程序、CoreOS](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/)
*   [码头工人课程](https://docker-curriculum.com/)
*   [Docker 文档](https://docs.docker.com/)
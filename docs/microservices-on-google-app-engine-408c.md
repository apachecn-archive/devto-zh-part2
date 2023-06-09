# 谷歌应用引擎上的微服务

> 原文：<https://dev.to/drbearhands/microservices-on-google-app-engine-408c>

这篇文章既是一篇咆哮，也是一篇指南。当我依赖谷歌的文档时，事情似乎要花 10 倍的时间。因此，虽然我将要描述的一切听起来太简单，不值得一个指南，但我还是决定做一个。希望这能为其他人节省很多时间。

所以在这里，如何部署微服务，每个都有自己的子域，使用 Google App Engine。

本指南假设您已经可以在 Google 云平台上部署应用程序。

请注意，如果您灵活使用 App Engine，您将不得不单独支付资源以保持每个服务的运行。

### 简介&动机

我正在创建一个[网站](https://jobsort.eu),收集 IT 工作，对其进行分析，并使结果可搜索。它有一个用 Elm 编写的 SPA(编译成 js)和一个用 Go 编写的 API/backend，提供来自数据库的作业结果。

有一段时间，我基本上将前端编译成后端的一个`/public`子目录。使用后端作为 API 和静态文件服务器。这个设置导致了许多问题，主要是我的构建/部署管道。虽然这些问题可以单独解决，但我更愿意解决潜在的问题。更重要的是，我计划的一些未来功能也将受益于这一变化。

所以我决定把我的后端和前端分成(微)服务。

### 创建服务

在我之前关于在谷歌计算引擎上[低成本周期性任务的帖子中，我已经简单地提到了创建服务。
服务就像任何其他应用程序一样，你可以用同样的方式创建它。其实一个 app 已经是一个服务了，*默认*服务。要将您的应用程序部署为不同的服务，只需在 app.yaml 文件中添加一行`service: <service name>`。](https://dev.to/drbearhands/scaling-down-periodic-tasks-on-google-compute-engine-4jh2)

添加这一行后，gcloud 会将您的应用程序部署为非默认服务。每个项目的服务名称都是唯一的。

对于您的前端，您可能只有静态文件(我有)。在这种情况下，可以使用类似的 app.yaml 文件:

```
runtime: python27
api_version: 1
threadsafe: true

handlers:
    - url: /
      static_files: build/index.html
      upload: build/index.html

    - url: /*
      static_dir: build 
```

这将提供来自`./build`目录的静态文件，相对于您的 app.yaml 文件。

请注意，运行时很重要，如果您指定`go`作为运行时，您的应用程序将无法运行，因为它将无法找到要运行的 go 程序。`python27`允许您*而非*运行应用程序。

### 自定义(子)域

我会假设你想使用自己的域名，而不是依赖谷歌提供的标准 appspot.com 域名。对于基本领域，这实际上在谷歌自己的文档中得到很好的解释。

文档还提到了注册通配符`*`子域的可能性，以及请求如何自动转发到同名的服务。遗憾的是，通配符域不能拥有自动管理的 ssl 证书。这是一个主要的缺点，所以我们将使用显式子域。

首先，按照[文档](https://cloud.google.com/appengine/docs/standard/python/mapping-custom-domains)中的说明注册它们。这里有一个警告，那就是 DNS 更改传播。即使您在 DNS 记录中所做的更改可能已经传播到足以让您访问该网站的程度，但它们可能还没有传播到足以让 google 生成 ssl 证书的程度。这意味着您可以通过 http 而不是 https 连接到您的网站。如果是这样的话，就去做别的事情，第二天左右再来。

当这个完成后，访问你的任何一个注册的子域名应该会让你登陆到你的项目的默认服务。

接下来，我们将把域映射到特定的服务。这实际上很简单，但是文档似乎只提到了通配符子域。只需创建一个 [`dispatch.yaml`](https://cloud.google.com/appengine/docs/standard/python/config/dispatchref) 文件。这是一个为整个项目定义 URL 和域的任何非默认路由的文件。比如我的`dispatch.yaml`长这样:

```
dispatch:
  - url: "jobs.jobsort.io/*"
    service: jobs 
```

如您所见，我没有列出默认服务。这是不必要的，因为所有注册的域都被路由到默认服务，除非另有说明。

您可以使用终端`gcloud app deploy dispatch.yaml`部署`dispatch.yaml`文件。

### 结论性意见

该指南仍然缺少如何在本地运行您的项目，该项目现在由微服务组成。这有点超出了这篇文章的范围，坦白地说，我还没有找到一个我 100%喜欢的系统。我将努力在将来写一个关于这方面的指南。
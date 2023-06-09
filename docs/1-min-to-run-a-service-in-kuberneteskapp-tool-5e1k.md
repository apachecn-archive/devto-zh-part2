# 1 分钟*在 Kubernetes-kapp 工具中运行一项服务

> 原文：<https://dev.to/peterj/1-min-to-run-a-service-in-kuberneteskapp-tool-5e1k>

> *也许更多，也许更少，但标题中有一些数字(最好是小一些)是很流行的。我记得科技/编程书籍的标题是
> “10 天学会 X”。过了一会儿，标题中的那些日子变成了
> 小时，现在我们谈论的是分钟……秒，接下来是微秒、纳秒？

最近，我写了一篇名为[的文章，从 Makefile 到 Kubernetes 上的 Go 语义版本服务](https://hackernoon.com/from-makefile-to-go-semantic-versioning-service-on-kubernetes-3fae678db87c)，其中我谈到了在 Go 中创建一个简单的服务，它运行在 Kubernetes 上并简单地返回给你下一个语义版本。例如:您向服务发送类似于`minor/0.1.0`的内容，服务将使用`0.1.1`进行响应。

在那篇文章的结论中，我提到我的大部分时间都花在了解决 Kubernetes 部署文件(Helm)和 Makefile 上，实际上我并没有在代码上花太多时间。

由于我做过一些 react 开发，我记得有一个工具叫做 create-react-app，它可以帮助你创建一个基本的 React 应用程序，你可以在几秒钟内构建并在浏览器中运行它。我认为，如果在 Kubernetes 中有类似的应用程序/服务可以快速启动并运行，这将是非常有用的。

kapp 工具诞生了——一个帮助你创建 Go 服务并让它在 Kubernetes 上运行的工具！

你可以从[这里](https://github.com/peterj/kapp/releases)下载该应用的第一版。完整的源代码可以在 [GitHub repo](https://github.com/peterj/kapp) 上获得。

# kapp 快速概述

安装 kapp 后，您可以像这样创建您的第一个 Go 服务:

```
cd $GOPATH/src/github.com/peterj
kapp create helloworld --package github.com/peterj/helloworld 
```

Enter fullscreen mode Exit fullscreen mode

这将在当前文件夹下创建一个名为 helloworld 的文件夹，其中包含以下文件:

```
helloworld
├── .gitignore
├── Dockerfile
├── Makefile
├── VERSION.txt
├── docker.mk
├── helm
│   └── helloworld
│       ├── Chart.yaml
│       ├── templates
│       │   ├── _helpers.tpl
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
├── main.go
└── version
    └── version.go 
```

Enter fullscreen mode Exit fullscreen mode

您可以为您的服务获得一个 docker 文件、安装它的 Helm chart 以及 Makefile 和 docker.mk 文件中的许多有用的 Makefile 目标。

看看下面的 gif，它向你展示了如何创建一个新的应用程序，初始化它，构建它，并在本地运行它。

[![kapp demo](img/f651de13956507cab0d54b5ba4c84fbb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i8OmWYtQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/peterj/kapp/raw/master/img/kapp-create.gif)

# 部署到 Kubernetes

现在您已经创建了应用程序，是时候在 Kubernetes 中部署和运行它了。从本地运行的应用程序(作为二进制文件)到在 Kubernetes 上的容器中运行的应用程序需要几个步骤:

1.  创建 Dockerfile 文件
2.  构建并标记 Docker 图像
3.  将图像推送到注册表
4.  创建 Kubernetes 部署文件
5.  创建 Kubernetes 服务文件
6.  部署 Kubernetes 部署和服务

当您运行`kapp`时，您已经为您的应用程序获得了一个`Dockerfile`和 Helm 图表(Kubernetes 部署、服务文件)。使用`Makefile`——您还可以运行任务来执行各种步骤。

让我们先从设置 Docker 注册表开始:

```
$ docker login # you can skip this if you're already logged in
$ export DOCKER_REGISTRY=[registry_username] 
```

Enter fullscreen mode Exit fullscreen mode

有了 Docker 注册表集，我们就可以构建镜像:

```
$ make build.image 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以把它推送到注册表:

```
$ make push.image 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以将我们的应用程序部署到 Kubernetes:

```
$ make install.app 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您的应用未在 Kubernetes 中部署和运行。此时，您可以为集群创建一个代理(例如`kubectl proxy`)或者让一个终端在集群中运行并访问您的服务。

# 快速迭代

完成服务的初始部署后，您就可以快速迭代应用程序了。

你可以修改你的代码，也可以选择升级应用版本并运行:

```
make upgrade 
```

Enter fullscreen mode Exit fullscreen mode

这将升级已经在 Kubernetes 中运行的现有应用程序。

# 结论

这是一个有趣的兼职项目，我可以用它来引导任何新的(Go)服务开发。该项目的源代码可以在 [GitHub](https://github.com/peterj/kapp) 上获得。所有公关和问题都非常受欢迎！
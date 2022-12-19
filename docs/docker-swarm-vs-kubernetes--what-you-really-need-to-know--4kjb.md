# docker Swarm vs . Kubernetes——你真正需要知道的

> 原文：<https://dev.to/totalcloudio/docker-swarm-vs-kubernetes--what-you-really-need-to-know--4kjb>

随着使用虚拟的、基于 Linux 的容器来构建应用程序的趋势继续增长，“Docker Swarm Vs. Kubernetes”是我们越来越经常听到的短语。虽然这两种技术都处理容器，但仔细看，它们实际上并不是竞争对手。

> Kubernetes 和 Docker 在堆栈的不同层次上运行——你实际上可以一起使用它们来使你的应用程序更好。

让我们向您介绍何时以及为什么您应该选择 Docker 和/或 Kubernetes。

## **码头工人**T3】

Docker 作为一个平台，革新了软件打包的方式。它提供了一个操作系统的沙盒视图，为构建和运行应用程序增加了许多优势。有了 Docker，应用程序可以快速构建、快速部署、快速扩展，并以最少的系统资源随时随地运行。

作为开源软件，Docker 迅速成为打包和分发容器化应用的标准。然而，随着它的发展，新的问题出现了。这些高度专业化的包装或俗称的“集装箱”必须进行协调、沟通、排序、储存处理等。这就是库伯内特斯出现的地方。

 **Kubernetes 的核心是一个容器编排平台。它有助于在不同的机器上运行不同的容器，根据需要扩大/缩小和添加/删除新的容器。它还在集装箱之间分配负载。

随着构建微服务的应用程序开发新浪潮的出现，容器编排和管理已经成为其中必不可少的一部分。

> Docker 很快意识到了这一点，并发布了自己的容器管理服务 Docker Swarm。

## **Docker Swarm vs . Kubernetes**T3】

Docker 和 Kubernetes 其实是可以合作的。就管理平台而言，您可以使用 Kubernetes 或 Docker Swarm 作为您的 Docker 引擎。Kubernetes 目前拥有最大的市场份额，几乎是标准平台。它围绕 pods 的概念工作，pods 是 Kubernetes 生态系统中的调度单元(可以包含一个或多个容器),它们分布在节点之间以提供高可用性。

它的优势在于利用了谷歌多年的研究成果，从而利用了专业知识。由于是开源的，它有一个充满活力的、不断发展的社区，提供了大量有用的资源和指导给任何人。它既可以在公共云服务上运行，也可以在本地运行，并且易于学习和实施。

Docker Swarm 的优势是紧密集成到 Docker 生态系统中，并且使用自己的 API。它的过滤和调度系统支持在集群中选择最佳节点来部署容器。

由于是由 Docker 自己开发的，Docker Swarm 消除了许多兼容性和其他差异，可以顺利集成。Kubernetes 通常不是一个完整的解决方案，需要定制插件来设置。如果你使用 Docker swarm，所有这些依赖关系都会在生态系统中得到很好的处理，使得安装和设置非常顺利。

然而，Docker Swarm 并没有被广泛用作 Kubernetes。因此，它周围的社区和支持并不广阔和方便。如今，大多数云提供商都将 Kubernetes 作为服务提供。

### 综上所述，

[![Docker Swarm Vs. Kubernetes](../Images/5393ea15b017da1f83a6fab1dbe59610.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z2AlO9Tw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dclhdhonururot55noak.png)

## **一个用例:Docker Swarm vs . Kubernetes**T3】

[Bugsnag](https://www.bugsnag.com/) ，一个错误报告服务，想要容器化他们的事件处理管道。他们的事件管道每天处理 2.2 亿个应用程序错误， [大约每分钟 150k](https://blog.bugsnag.com/container-orchestration-with-docker-swarm-mode/)。他们选择了 Docker Swarm，而不是 Kubernetes，因为后者“在一群节点上运行 polling Node.js 应用程序过于复杂，部署和操作起来也是大材小用”。T13】

## **最终论证**

Kubernetes 提供了更多的定制选项和广泛的用途，适合希望完全自己设置参数的人，而 Docker Swarm 最适合希望快速设置一个容器化的应用程序并使其运行的人。

* * *

### 你认为哪个平台最适合你的需求——Docker 还是 Kubernetes？在 [@totalcloudio 上发布您的想法。](https://twitter.com/totalcloudio) 在 [Twitter](https://twitter.com/totalcloudio) 和 [Medium](https://medium.com/@Totalcloudio) 上关注我们。

* * *

> 如果你想查看所有连接到你的 AWS 库存的亚马逊弹性容器服务(Amazon ECS)，试试 [TotaCloud.io](https://totalcloud.io/user-signup.html) ，这是一个游戏化的 AWS 云管理平台，为今天的 DevOps 工程师&经理提供完整的云可见性。在单一平台上，用户可以实时了解成本、监控&安全资源和执行操作。 [从这里开始](https://totalcloud.io/user-signup.html)。

[原文发表于 blog.totalcloud.io.](http://blog.totalcloud.io/docker-vs-kubernetes-really-need-know/)

注:最初发布于 2018 年 4 月 9 日 [blog.totalcloud.io](https://blog.totalcloud.io/docker-vs-kubernetes-really-need-know/) 。**
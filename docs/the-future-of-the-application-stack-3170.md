# 应用程序堆栈的未来

> 原文：<https://dev.to/scalegrid/the-future-of-the-application-stack-3170>

[![The Future Of The Application Stack: Kubernetes, PaaS and DBaaS - ScaleGrid Blog](img/9a1198943ce5ec613fde6846e4018f87.png)T2】](https://scalegrid.io/blog/the-future-of-the-application-stack/)

集装箱正在吞噬世界。如果您在过去几年中已经在生产环境中构建和部署了一个应用程序，那么您很可能已经在容器中部署了您的代码。您可能已经创建并部署了单独的容器( [Docker](https://www.docker.com/ "Docker Containers") 、 [Linux LXC](https://linuxcontainers.org/ "Linux Containers") 等)。)开始时直接使用，但当您需要协调多节点部署和高可用性(HA)时，可以快速切换到容器编排技术，如 [Kubernetes](https://kubernetes.io/ "Kubernetes Container Orchestration") (K8s)或 [Swarm](https://docs.docker.com/swarm/ "Docker Swarm") 。在这个容器驱动的世界里，应用程序栈的[未来会是什么样子？让我们从我们需要从这个“未来”应用程序堆栈中得到什么开始。](https://scalegrid.io/blog/the-future-of-the-application-stack/)

## 我们需要从这个未来的应用程序堆栈中获得什么？

1.  #### 云不可知

    我们希望不依赖云，能够部署到我们选择的任何云。理想情况下，我们甚至可以在单个部署中混合不同的提供商。
2.  #### 内部部署

    我们需要能够使用我们自己的定制硬件、私有云以及内部管理的数据中心在内部运行我们的应用堆栈。
3.  #### 语言不可知

    这几乎是老生常谈，但为了完整起见，我还是补充一下。未来的开放堆栈需要支持所有流行的编程语言。

## 未来的应用堆栈

未来的应用程序堆栈将由三种技术组成:K8s、平台即服务(PaaS)和数据库即服务(DBaaS) **:**

<center>[![The Future Of The Application Stack: K8s, PaaS and DBaaS](img/440a72fce3e95712ecb8ff65122df534.png)](https://scalegrid.io/blog/wp-content/uploads/2018/09/Application-Stack-Diagram-DBaaS-PaaS-K8.png)</center>

### K8s

Kubernetes 是一个可移植的、可扩展的开源平台，用于管理容器化的工作负载和服务，您可以将您的应用程序直接部署到 K8s 容器。对于拥有现有应用程序的客户来说，将您现有的应用程序直接打包并部署到 K8s 是非常明智的。

所有公共云供应商都为 K8s 提供强大的本地支持，您也可以在内部运行自己的 K8s 集群。Docker 现在也加入了 K8s 的行列——所以你有充分的灵活性。毫无疑问，K8s 是当今的王者。几年后，它可能会被另一个解决方案击败，但是容器编排技术会一直存在。

### PaaS 解决方案

如果您正在从头开始创建一个新的应用程序，像 [Cloud Foundry](https://www.cloudfoundry.org/ "Cloud Foundry PaaS Solutions") 和 [OpenShift](https://www.openshift.com/ "OpenShift PaaS Solutions") 这样的平台即服务解决方案提供了引人注目的优势，您可以利用这些优势来加速您的应用程序开发生命周期。PaaS 是必须的吗？肯定不是，但我认为如果你正在创建一个新的应用程序，这是值得考虑的。

在某些情况下，PaaS 解决方案可能运行在 K8s 上，或者位于 K8s 旁边——从应用程序的角度来看，这并不重要。如果您的 IT 组织正在部署 PaaS 解决方案，他们可能会喜欢在现有 K8s 集群上运行。上面提到的 PaaS 解决方案也可以在所有公共云上使用。同样，您可以从您的平台获得所需的全部灵活性。

### DBaaS 解决方案

运行和管理一个生产数据库系统不适合胆小的人。如果您认为您将在三个容器上安装您的生产数据库，并且它将从此愉快地运行，那么您还有另外一件事要做。通过使用数据库即服务解决方案，他们将处理您的数据库管理的所有操作方面，以便您为任何和所有意外做好准备。

您的 DBaaS 可能会也可能不会在 K8s 上运行。也许 DBaaS 的某些部分可以在 K8s 上运行，但很可能不会。这是为什么呢？

1.  除了流行的公共云之外，还没有达到 EC2 弹性块存储(EBS)质量的优秀存储/卷解决方案。像 PortWorx T1 和 T2 open EBS T3 这样的供应商正在开发它，但是它还没有出现。如果没有好的存储解决方案，几乎不可能将数据放入容器。
2.  如果您正在运行一个大型的、几 TB 的生产数据库服务器，那么在容器中运行它是没有意义的——您将拥有大型的专用机器，这些机器配有花哨的 SSD。
3.  太多的动力——是的，你可以拥有太多的好东西。有时，当事情失败时，你希望它们一直失败下去，这样你就可以看看发生了什么。Kubernetes 中的 StatefulSets 是朝着解决这个问题的正确方向迈出的一大步。

在 ScaleGrid，我们的愿景是将 DBaaS 作为未来应用堆栈的一部分。我们现在是多云计算，也可以在内部或您自己的私有数据中心运行。此外，我们的平台是一个多语言系统，支持多种数据库，包括 [MongoDB](https://scalegrid.io/mongodb.html "MongoDB Hosting at ScaleGrid") 、 [Redis](https://scalegrid.io/redis.html "Redis Hosting at ScaleGrid") 、 [MySQL](https://scalegrid.io/mysql.html "MySQL Hosting at ScaleGrid") 和 [PostgreSQL](https://scalegrid.io/postgresql.html "PostgreSQL Hosting at ScaleGrid") 。

为了简单起见，我排除了应用程序堆栈的一些其他部分，如对象存储、文件系统存储等。原则上，我希望这些组件与 DBaaS 组件相似。这篇博客的灵感来自于几周前我参加的一个硅谷 PostgreSQL meetup ，并向 RedisLabs 的 Dave Nielsen([@Dave Nielsen](https://twitter.com/davenielsen))大喊，开始了这个话题的讨论。
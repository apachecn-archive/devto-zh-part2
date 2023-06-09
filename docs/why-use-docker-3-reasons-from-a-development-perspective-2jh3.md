# 为什么要用 docker？从发展的角度看，有三个原因

> 原文：<https://dev.to/geshan/why-use-docker-3-reasons-from-a-development-perspective-2jh3>

Docker 在过去 5 年中在技术领域的流行是一个保守的说法。在过去的五年里，它的使用和受欢迎程度都出现了爆炸式的增长。集装箱给我们带来了风暴。没有容器化的新应用程序感觉缺少了什么。Docker 已经脱颖而出，成为集装箱领域的王者。这篇文章详细介绍了在你的开发环境中使用 Docker 的原因。

[![Reasons to use Docker in dev](img/6edd79da753625b6b3a793a56c423224.png "Reasons to use Docker in dev")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QMazWI-L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geshan.com.img/why-docker/whale.jpg)

## TLDR；

> Docker 对于开发环境非常有用，因为如果它在你的机器上运行，它可以在任何地方运行。它在你朋友的机器上运行，在准备阶段也在生产阶段运行。当一名新团队成员开始工作时，他/她运行 3 条命令，应用程序也在运行。新的团队成员从第一天起就可以高效工作。终于，PHP 或者 MYSQL 的新版本来了。您可以使用 Docker 轻松测试您的应用程序，并让它在旧版本中运行，没有任何问题。

## 码头工人码头工人

你可以[阅读](https://runnable.com/docker/why-use-docker)所有你想知道的关于虚拟机和容器的信息。在[现实世界](https://www.airpair.com/docker/posts/8-proven-real-world-ways-to-use-docker)中使用 Docker 的理由有很多。这篇文章讨论了为什么使用 Docker 进行开发。如果你想亲自阅读关于在你的开发环境中使用 [docker 和 docker compose](https://geshan.com.np/blog/2017/05/how-to-use-docker-compose-with-virtual-hosts-and-services-like-db-for-dev-environment/) 的文章。

Docker 有很多优点，但最重要的是容器类比。你不仅要交付你的代码，还要以标准的方式交付整个操作系统和所有相关层。它是一个完整的包，包含正确的操作系统版本、所需的语言版本、应用程序的任何外部依赖项以及应用程序代码。这也是一种轻量级的、孤立的和资源可控的方式。这是 docker 吹走虚拟机的地方。

[![Reasons to use Docker in dev](img/10f1ffd63c5bc3693d30cb8dd5a42cbd.png "Reasons to use Docker in dev")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JpimxEZY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geshan.com.img/why-docker/ship.jpg)

## 开发环境使用 Docker 的理由

如果您已经读到这里，现在让我们深入了解一下您希望在您的开发环境中使用 Docker 的原因:

### 1。在我的机器上运行=在任何地方运行

如果你已经正确地对你的应用程序进行了分类，并且它在你的机器上运行没有问题，那么 99%的情况下它在任何地方都能顺利运行。这里的“任何地方”是指在您朋友的机器上，也指在登台环境和生产环境中。假设他们都安装了 docker 并正确配置，你的应用程序将运行。使用 docker 也使得应用程序代码云提供者不可知。你的应用可以毫无问题的运行在 AWS 或者 GCP 或者 Azure 上。

### 2。新的团队成员从第一天起就能高效工作

想象一下，一个新的团队成员加入，然后他/她花了一天多的时间来为机器安装正确的操作系统。设置公司使用的语言，并在此基础上添加数据库。仅仅是正确设置环境就浪费了 2-3 天时间。输入 docker + docker-compose，新加入者设置 OS。安装 docker，然后运行 3-5 个命令，喝点咖啡，然后变魔术:你的应用程序正在运行。新加入者可以在第一天贡献工作代码。想想一个公司用这种方法可以节省多少成本。简化的 docker 实现使之成为现实。

### 3。测试应用程序与新版本语言/数据库的兼容性

想象一下，你使用的语言的新版本刚刚发布。就像你使用 PHP 5.6 和 7.0 已经出来了。您不知道要使您的应用程序与新版本的语言兼容需要做多少工作。在这里使用 docker，你只需要运行两个不同的 docker 容器，一个运行当前版本，另一个运行新版本。你甚至可以并排测试应用程序来衡量性能。这可以适用于任何编程语言。

同样的技术也可以用来测试 MYSQL 和 8.0 版本。你可能认为有像 NVM(节点版本管理器)或 RVM (Ruby 版本管理器)这样的工具，但并不是每种语言都有这样的工具。此外，你可以只使用 docker 比超载你的本地机器。

## 结论

Docker 对软件工程师和 DevOps 工程师来说都是福音。这使得发布软件变得更加容易，因为整个堆栈不仅仅是代码。尽管如此，要将你的产品转移到 Docker，还需要考虑很多事情。像容器构建器(例如[谷歌云构建](https://cloud.google.com/cloud-build/docs/))，容器编排(例如 [Kubernetes](https://kubernetes.io/) )。因此，在进入容器阶段/生产阶段之前，要确信它对本地开发环境的好处。我希望你利用 Docker 来从中获取最大的利益。

* * *

最初发表于[Geshan.com.np](https://geshan.com.np)，你可以在那里读到更多的东西。
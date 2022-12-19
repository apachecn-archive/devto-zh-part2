# 我们如何使用 Docker Swarm 构建堆栈

> 原文：<https://dev.to/playtomic/how-we-built-our-stack-with-docker-swarm-3md5>

当我们开始考虑我们想要为 Playtomic 建造什么样的建筑时，我们知道我们不想最终得到什么:一个巨大的庞然大物。无论我们称之为微服务还是简单的老服务，我们都想要一个分散的系统，如果一个或一些部分出现故障，它能让我们生存下来。

我们还希望能够独立构建和测试系统的不同部分，以便不同的团队可以专注于解决对我们来说很重要的问题，而不必太担心理解整个系统的每个小细节。能够通过创建和重用服务来组合功能是按照我们当前需要的速度发展的关键。

因此，我们开始考虑我们需要的不同组件，以便组装这样一个系统。让我们看看其中的一些。

### 我们要如何构建服务？

我们决定开始使用 Spring Boot 来构建我们的服务。我们同时使用 Java 和 Kotlin。团队中的大多数人以前都有过用它构建单片应用程序的经验，我们认为这将是一个很好的起点。Spring boot 已经证明了自己是构建服务的好工具，因为它提供了数百种现成的框架和工具(安全、监控、日志、测试，...).我们发现的主要问题是启动时间和内存占用。我们目前正在尝试不同的方法来减少这两者。

### 我们要去哪里运行我们的东西？

我们需要能够让我们独立部署服务的东西，保证我们一切都将持续运行，没有太多的痛苦。

我们也不希望在每次推出新服务时都不得不过度依赖手动供应(至少，尽可能将其最小化)。Docker 和 [Docker Stacks](https://docs.docker.com/get-started/part5/) 对我们来说似乎是一个很好的解决方案。

我们研究了 Docker Swarm，发现它设置简单，易于理解，扩展性也很好。我个人在过去有过一些使用 Docker Swarm 的经验，虽然我在稳定性和服务质量方面有一些严重的问题，但这些问题主要是由于当时系统不够成熟，所以我们决定再试一次。

我们建立了一个 Docker 群，由 3 个管理节点和 4 个工作节点组成，部署在我们自己的服务器上。到目前为止，它一直工作顺利，没有任何问题。所以我们对自己的决定很满意。

[![Docker Swarm](../Images/1b5f082003dcbf3bf2b39ae205b3cffc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h-Ir0ZBf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9csnvs432iaq5tgsly61.png)

对我们来说， [Kubernetes](https://kubernetes.io/) 对于我们所需要的来说似乎太复杂了。我们很高兴 Docker Swarm 现在支持 Kubernetes，所以如果我们需要的话，我们可以在将来利用它。

我们还使用 [Docker 流代理](https://proxy.dockerflow.com/)将流量从不同的应用程序和 API 路由到群中相应的服务。

### 如何才能确定一切都正常工作？

在所有系统中，特别是在分布式系统中，能够看到整个画面以了解系统的行为是非常重要的。我们建立了一个基于普罗米修斯的监控系统来收集实时指标，基于 T2 的 Grafana 来可视化服务以及它们是如何协同工作的。

[![Monitoring system](../Images/b39d2c3e7a8d208d049bae1697d60797.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4NvM_x8j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rdva7ybg9jv527k6qna7.png)

日志对于快速找到问题所在也非常重要。我们开始使用自己的 [ELK stack](https://www.elastic.co/elk-stack) ，但最近我们决定转移到 [logz.io](https://logz.io/) 。建立我们自己的弹性搜索集群并让它在生产中运行对我们来说太难了。我们认为我们花了太多的时间去做我们觉得不应该做的事情(不是我们的重点)，所以我们把那一块移走了。

Logz.io 还有一个很棒的洞察功能。日志由他们的人工智能进行分析，将它们与不同网站上的数千篇文章进行比较，从而为您的系统提供有价值的见解。我们已经能够使用这个特性发现产品中的 bug，否则这些 bug 很可能会被我们忽略。

[![Logz.io insight feature](../Images/8020b38d0e728c3b8aa29f160cee7c5f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r0eiLVpj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rj3b226cqlaom8krmd0u.png)

我们知道我们仍有许多事情要探索和改进，但我们对目前的设置非常满意，我们希望与您分享。您正在使用哪种基础架构？你有什么改进我们的想法？让我们在下面的讨论中知道。
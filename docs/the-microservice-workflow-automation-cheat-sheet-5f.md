# 微服务工作流自动化备忘单

> 原文：<https://dev.to/berndruecker/the-microservice-workflow-automation-cheat-sheet-5f>

### 《微服务工作流自动化备忘单》

你的公司可能想要一个微服务架构**和**应用工作流自动化(我不会在这篇博客文章中探究动机，但是你可能想要阅读关于 [*5 个你可能没有考虑过的工作流自动化用例*](https://thenewstack.io/5-workflow-automation-use-cases-you-might-not-have-considered/) 或[*BizDevOps——工作流引擎*](https://blog.bernd-ruecker.com/bizdevops-the-true-value-proposition-of-workflow-engines-f342509ba8bb) 的真正价值主张)。)这会让你和很多我们的客户在一起。通常，您会有以下问题:

*   **范围和界限**(“您希望自动化什么样的工作流，以及如何将其映射到您环境中的多个微服务或[有界上下文](https://martinfowler.com/bliki/BoundedContext.html))。为了限制这篇文章的范围，我今天省略了这个话题，但是你可能想读读《T4》，在使用有界上下文或[现实生活中的 BPMN](https://www.amazon.com/Real-Life-BPMN-introductions-CMMN-DMN/dp/1541163443/) 时避免“BPM 大块头”。
*   **栈和工具**(“我可以使用什么样的工作流引擎？”)
*   **架构**(“我是集中运行工作流引擎还是分散运行？”)
*   **治理**(“谁拥有工作流模型，它是如何部署的？”)
*   **运营**(“我如何保持控制？”)

在这篇博文中，我对你必须做出的核心架构决策给出了一些指导。我将给出简单的答案来帮助你开始，并对这个复杂的话题有一些初步的了解。

由于所有的理论都是灰色的，我想用一个具体的商业例子来讨论某些方面，这个例子很容易理解和遵循。利用这一点，我依次检查了以下几个方面:

*   **跟踪或管理** —编排或编排？
*   **3 种通信选择**:使用命令和事件的异步通信，类似 RPC 的点对点通信，使用工作流引擎的工作分配
*   **集中式或分散式**工作流引擎
*   工作流模型的所有权

### 业务示例

我在本文中使用的例子是一个简单的订单执行应用程序，作为 GitHub 上的源代码的[流动零售示例应用程序](https://github.com/berndruecker/flowing-retail)提供。流动零售的酷之处在于它实现了不同的架构选择，并提供了不同编程语言的示例。所有的例子都使用工作流引擎，要么是 [Camunda BPM](https://camunda.com/) 要么是 [Zeebe](https://zeebe.io/) 。但是您可以将学到的知识转移到其他工具上——我只是最了解我自己公司的工具，并且有很多现成的代码示例。

让我们假设您想要通过解耦的服务来实现一些业务功能(例如，当按下一个类似于 [Amazon 的破折号按钮](https://en.wikipedia.org/wiki/Amazon_Dash)时的订单履行):

[![](../Images/465d49489f39c2d54285102422e87a96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_-JDxydt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/846/1%2AsUSskBeL09CQM1MRWRwl1A.png)

### 跟踪还是管理？编舞还是配器？

第一个问题通常与编排或编排有关，后者通常被视为更好的选择(基于 Martin Fowler 的微服务文章)。这通常与[事件驱动架构](https://en.wikipedia.org/wiki/Event-driven_architecture)相结合。

在这样一个**精心设计的架构**中，你发出所谓的领域事件，每个感兴趣的人都可以对这些事件采取行动。这是一个广播。这个想法是，你可以简单地添加新的微服务来监听事件，而不改变其他任何东西。这样的工作流是不明确的，但随着一系列事件的传递而发展。危险在于你[忽略了更大规模的流程](https://martinfowler.com/articles/201701-event-driven.html)，在我们的例子中是订单执行。理解流程、改变流程或操作流程变得异常困难。甚至回答“有没有订单逾期了？”或者“有什么卡住了需要干预的吗？”是一个挑战。我在我的演讲[中讨论了分布式系统中的复杂事件流](https://www.slideshare.net/BerndRuecker/complex-event-flows-in-distributed-systems) ( [记录，例如在 QCon New York](https://www.infoq.com/presentations/event-flow-distributed-systems) 或 [DevConf Krakow](https://www.youtube.com/watch?v=EegrVoPTRbQ) )。

你可以在这里找到一个纯编排的工作示例:[https://github . com/berndruecker/flow-retail/tree/master/Kafka/Java/choreography-alternative](https://github.com/berndruecker/flowing-retail/tree/master/kafka/java/choreography-alternative)

[![](../Images/e8e02049057bcd65bf74dab318079b4c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--grcS11MH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/503/1%2A36HoNe4sJUyBY3IpDLS5bg.png)

#### 追踪

一个简单的解决方法是至少跟踪事件的流程。根据具体的技术架构(见下文)，您可能只需添加一个工作流引擎来读取所有事件，并检查它们是否可以与跟踪流相关联。我在与 Kafka 和 Zeebe ( [录制自 Kafka Summit San Francisco](https://www.confluent.io/kafka-summit-sf18/the_big_picture) )的谈话[中讨论了这一点。](https://www.slideshare.net/BerndRuecker/kafka-summit-2018-monitoring-and-orchestration-of-your-microservices-landscape-with-zeebe)

[![](../Images/898f2e4813cc2353d7db0d2e2d60b579.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PpTCIzET--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/818/1%2Ag9oPOv8FamNm9-nnQTbYOw.png)

flow-retail 展示了一个使用 Kafka 和 Kafka-Connect 的实现示例:[https://github . com/berndruecker/flow-retail/tree/master/Kafka/Java/choreography-alternative/zee be-track](https://github.com/berndruecker/flowing-retail/tree/master/kafka/java/choreography-alternative/zeebe-track)和【https://github.com/berndruecker/kafka-connect-zeebe】。

#### 管理之旅

这是非侵入性的，因为你不需要改变你的架构。但它能让你开始做事，例如在订单延迟的情况下:

[![](../Images/826b4d4defd67d1962adc732f8671ceb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4sTFn3FQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/593/1%2AbEACTSoRLqJNIyLYeLY_pQ.png)

通常，这将导致从简单的跟踪流程**到真正的****管理** it 的**旅程:**

[![](../Images/c97301842604f41d574889120b80255a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oWxHYdga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/753/1%2Ap0fn0HCSWkKBC9aozTX9yA.png)

#### 混合编排和编排

一个好的架构通常是编排和编排的混合。平心而论，如果没有一些经验，平衡这两种力量并不容易。但是我们看到很多证据表明这是正确的方向，所以投入时间绝对是值得的。否则，你的编舞，在白板上是独立专业人士的优雅舞蹈，通常以更像混乱的 pogo 结束:

[![](../Images/9ffac9d8e4ddc4a8c00a1cfd33070a1d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8T7eB-Rg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/966/1%2AjWVswQO5pT6q1qQCTLnn3A.png)

在流动零售的例子中，这也意味着您应该为最重要的业务能力:客户订单拥有一个单独的微服务！

[![](../Images/6dfb1ef2f8fa95edf7bd973dab956e3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7uv-bkDP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/503/1%2A21hvhiGD0P0DoguP8fCCWw.png)

### 工作流引擎的作用——三种架构选择

但是如何使用工作流引擎建立一个架构来达到这种平衡呢？让我们简化一下，假设我们有一片绿地，只有三种架构可供选择(所以没有混合架构或遗留)。

*   通过命令和事件进行异步通信(通常使用消息或事件总线)
*   通过请求/响应进行点对点通信(通常是 REST)
*   工作流引擎的工作分配

[![](../Images/213081b736cea3db697eb0b25fa6fec4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OGFoo9Yd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/882/1%2AF_JateXoPZ6Qo9YjD0mUqQ.png)

我们还没有考虑是集中运行还是分散运行工作流引擎，这是一个单独的问题，以后再解决。

#### 通过命令和事件进行异步通信

这种架构依靠中央总线进行异步通信。不同的微服务连接到这个总线。编排逻辑和相应的编排流归微服务所有。工作流可以向总线发送新的命令(“嘿付款，请为我取回一些钱”)或等待事件发生(“无论谁感兴趣，我取回了 O42 的付款”)。

[![](../Images/beffc3e40e4ade0d60e0446d19546958.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aBhf7mcZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/822/1%2ACrVNp7NanVltYv7TBqRI4g.png)

*   *典型工具*:卡夫卡、RabbitMQ (AMQP)、JMS。
*   *工作流引擎做什么*:超时处理，管理活动链/流，支持有状态的[企业集成模式](https://www.enterpriseintegrationpatterns.com/)，如[聚合器](https://www.enterpriseintegrationpatterns.com/patterns/messaging/Aggregator.html)或[重排序器](https://www.enterpriseintegrationpatterns.com/patterns/messaging/Resequencer.html)，一致性和补偿处理又名[传奇模式](https://blog.bernd-ruecker.com/saga-how-to-implement-complex-business-transactions-without-two-phase-commit-e00aa41a1b1b)，如我的演讲中所讨论的[事务丢失](https://www.slideshare.net/BerndRuecker/2018-lost-in-transaction/)(例如在 JavaZone Oslo 中记录的[)。](https://2018.javazone.no/program/45df84d4-e819-4fc9-9e3b-931972891441)
*   *实现示例*:[https://github . com/berndruecker/flow-retail/tree/master/Kafka/Java](https://github.com/berndruecker/flowing-retail/tree/master/kafka/java)
*   *Pro* :微服务的时间解耦；事件驱动架构应用正确可以减少耦合；许多失败场景(比如响应消息丢失)对开发人员来说是透明的，所以他会适当地考虑这些情况。
*   *Con* :需要消息或事件总线作为中心组件，不易操作。缺少这些组件的操作工具导致努力进入本土的“消息医院”大多数开发人员不太熟悉异步通信。

#### 通过请求/响应进行点对点通信

在这种架构中，您只需主动调用其他微服务，通常是以同步、阻塞的方式。最突出的方法就是休息。端点通常从注册表中检索。工作流引擎可以协调 REST 调用，还可以帮助应对远程通信的挑战——这是我在我的 [3 微服务集成的陷阱文章](https://www.infoworld.com/article/3254777/application-development/3-common-pitfalls-of-microservices-integrationand-how-to-avoid-them.html)中详细讨论的主题(也可以作为演讲、[在 QCon London](https://youtu.be/O2-NHptllKQ) 等场合录制的[)。](https://www.slideshare.net/BerndRuecker/3-common-pitfalls-in-microservice-integration)

[![](../Images/75ded1888fdfc45e37a2d8e444cc9ed4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vt51tDeK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/744/1%2AcJWRsT-c_0-PxcyrmF9ECw.png)

*   *典型工具* : REST、SOAP、gRPC 也可以通过使用[请求/回复队列(例如在 RabbitMQ](https://www.rabbitmq.com/tutorials/tutorial-six-java.html) 中)的阻塞消息来实现。
*   *工作流引擎做什么* : [有状态弹性模式(如有状态重试)](https://blog.bernd-ruecker.com/fail-fast-is-not-enough-84645d6864d3)、超时处理、管理活动链/流、一致性和补偿处理又名[传奇模式](https://blog.bernd-ruecker.com/saga-how-to-implement-complex-business-transactions-without-two-phase-commit-e00aa41a1b1b)如我的演讲中所讨论的[在事务中丢失](https://www.slideshare.net/BerndRuecker/2018-lost-in-transaction/)(例如在 JavaZone Oslo 中记录的[)。](https://2018.javazone.no/program/45df84d4-e819-4fc9-9e3b-931972891441)
*   *实现示例*:[https://github . com/berndruecker/flow-retail/tree/master/rest](https://github.com/berndruecker/flowing-retail/tree/master/rest)
*   *Pro* :易设置，易被大多数开发者理解；可用的良好工具。
*   反对:调用看起来像是本地的，所以开发人员经常忘记分布式系统的复杂性；要求应用弹性模式(例如，[断路器](https://martinfowler.com/bliki/CircuitBreaker.html)，状态重试等等)。

#### 工作流引擎的工作分配

在这种架构中，工作流在微服务之间分配工作，这意味着它本身成为某种总线。微服务可以订阅工作流的某些工作，并通过某种队列获得任务。

[![](../Images/de1d6f6e569c89a788bec9cb991979dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d0ZwtTU_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/583/1%2A_3tmyFDu5MFEWb5_tIfLAg.png)

*   *典型工具* : [外部任务](https://docs.camunda.org/manual/latest/user-guide/process-engine/external-tasks/)(卡蒙达 BPM)或[工人](https://docs.zeebe.io/basics/job-workers.html)(齐贝)。
*   *工作流引擎做什么*:通信通道、超时处理、管理活动链/流、一致性和补偿处理又名[传奇模式](https://blog.bernd-ruecker.com/saga-how-to-implement-complex-business-transactions-without-two-phase-commit-e00aa41a1b1b)如我的演讲中所讨论的[事务中的丢失](https://www.slideshare.net/BerndRuecker/2018-lost-in-transaction/)(例如在 JavaZone Oslo 中记录的[)。](https://2018.javazone.no/program/45df84d4-e819-4fc9-9e3b-931972891441)
*   *实现示例*:[https://github . com/berndruecker/flow-retail/tree/master/zeebe](https://github.com/berndruecker/flowing-retail/tree/master/zeebe)
*   *Pro* :易于设置；良好的操作工具。
*   工作流引擎成为架构的核心部分，需要适当的操作；微服务之间的通信只能通过工作流进行，或者需要建立第二种通信方式(例如 REST 或消息传递)。

#### 感想和推荐

像往常一样，很难给出一个明确的建议。通常情况下，我会试图弄清楚公司目前已经建立了什么，并根据直觉判断在这种环境下什么会成功。

例如，如果客户对 Kafka 或消息传递没有任何经验，那么很难在旅途中建立这种关系。因此，他们可能更好地使用基于 REST 的架构，特别是如果，例如，他们深入 Spring Boot，使得一些挑战相对容易解决。然而，如果他们在战略上想要走向更多的异步，我个人支持这一点，但我仍然想确保他们真的能够处理它。如果客户已经接受了[领域驱动设计(DDD)](https://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215) 和事件，或者甚至利用了像 Akka 或 Axon 这样的框架，那么包含工作流引擎的事件驱动方法可能是最好的选择。

所以，有各种各样的可能性，我认为所有的选择都是完全有效的。问问自己什么适合你的组织，你已经有什么工具，你在追求什么目标。不要忘记你的开发人员，他们必须做所有的实质性的艰苦工作，并且需要真正理解他们在做什么。不要忘记操作，以确保您在上线时真的很开心。

### 集中式还是分散式工作流引擎？

如果您想使用工作流引擎进行工作分配，它必须是集中式的。在其他选择中，您有两个半选项:

*   **分散引擎**，意味着每个微服务运行一个引擎
*   **一个中央引擎**，服务多个微服务
*   像分散引擎一样使用的中央引擎。

一个很好的背景阅读可能是 [*架构选项运行一个工作流引擎*](https://blog.bernd-ruecker.com/architecture-options-to-run-a-workflow-engine-6c2419902d91) 。

[![](../Images/137c485458668e9699c5e3f132eeba74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bgkTfA1U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/868/1%2AuGOJ-PtvpuTJKZD2m3_VTA.png)

#### 分散引擎

有了微服务，默认就是给团队很大的自主权，尽可能的相互隔离。从这个意义上说，这也是默认的分散引擎，每个需要的微服务一个工作流引擎。每个团队甚至可以决定他们想要使用哪个实际的引擎(产品)。

*   *实现示例*:[https://github . com/berndruecker/flow-retail/tree/master/Kafka/Java/order-cam unda](https://github.com/berndruecker/flowing-retail/tree/master/kafka/java/order-camunda)
*   *Pro* :自主性；孤立。
*   *Con* :每个团队都要操作自己的引擎(包括打补丁等)；还没有现成的中央监控。

典型的**监控**在这个架构中被讨论的最多:“我们如何保持对正在发生的事情的概述”？

拥有分散的操作工具通常是一种优势。由于微服务架构中的团队经常为他们运行的服务进行开发，他们负责修复工作流中的错误。因此，他们有一个集中的视图，只看到他们负责的事情，这很酷。

#### 用分散引擎进行集中监控

但是通常您仍然希望有一个总体的概述，至少是跨越微服务边界的端到端流。目前，客户通常构建自己的集中式监控，最常见的是基于例如 Elastic。现在，您可以将分散引擎中最重要的事件(例如，工作流实例开始、达到里程碑、工作流实例失败或结束)发送给 it 部门。中央监控仅显示更高层次的概览，并链接回分散操作工具以获取详细信息。换句话说，分散的工作流引擎处理所有的重试和失败处理逻辑，而中央监控只是提供对整个流程的可见性。

[![](../Images/380dd3814f28814e5b7e0c15799e290d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Cuj3qxBi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/910/1%2AMvPNnzzduN0xOYjqfHqfkw.png)

在我们自己的堆栈中，我们开始允许某些工具从分散的引擎中收集数据，例如 [Camunda Optimize](https://camunda.com/products/optimize/) 或 Zeebe Operate。

*   *实现示例*:简化示例包含在[https://github . com/berndruecker/flow-retail/tree/master/Kafka/Java/monitor](https://github.com/berndruecker/flowing-retail/tree/master/kafka/java/monitor)中

#### 一台中央发动机

为了简化操作，您还可以运行中心引擎。这是微服务可以连接的远程资源，以便部署和执行工作流。从技术上讲，这可能是通过 REST (Camunda BPM)或 gRPC (Zeebe)实现的。

[![](../Images/f2651abca26db51a57a7cc1712ce51e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L6krVqV2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/258/1%2AgQ5LYar6E4jjV-HDM65QAg.png)

*   *实现示例*:[https://github . com/berndruecker/flow-retail/tree/master/Kafka/Java/order-zee be](https://github.com/berndruecker/flowing-retail/tree/master/kafka/java/order-zeebe)
*   *Pro* :操作简便；开箱即用的中央监控
*   就运行时数据和产品版本而言，微服务之间的隔离不太严格；就可用性要求而言，中央组件更为关键。

如果你想使用工作流引擎作为工作分配，你当然需要集中运行它。

#### **中央发动机，即像分散式发动机一样使用**

这种方法通常需要一些解释。在 Camunda 中，您可以在不同的微服务中以分散的方式运行工作流引擎作为库(例如，使用[Spring Boot 启动器](https://docs.camunda.org/manual/latest/user-guide/spring-boot-integration/))。但是，然后你把所有这些引擎连接到一个中央数据库，它们在那里会合。这让你有免费的中央监控。

[![](../Images/8d364732a13496667f78886a6f7d3a28.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--msEKw1kl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/260/1%2AIKko3bIeAct-ZCi9vTOmFQ.png)

*   *Pro* :开箱即用的中央监控。
*   *缺点*:在运行时数据和产品版本方面，微服务之间的隔离更少，但实际上是通过像[滚动升级](https://docs.camunda.org/manual/latest/update/rolling-update/)和[部署感知流程引擎](https://docs.camunda.org/manual/latest/user-guide/process-engine/the-job-executor/#job-execution-in-heterogeneous-clusters)这样的特性来调节的。

#### 感想和推荐

总的来说，我个人倾向于支持分散的方法。它只是与微服务值同步。

但我对出于正当理由运行中央引擎没意见。对于小公司来说尤其如此，因为运营开销比清晰的沟通边界更重要。在这些情况下，协调工作流引擎的维护窗口也不是什么问题。因此，根据经验法则:公司越大，你就越应该倾向于分权。除了组织原因之外，一个引擎上的负载也可以使决策变得清晰——因为多个引擎也意味着分布负载。

在 Camunda 中，混合共享数据库是一个巧妙的技巧，但不应该过度使用。我也将它限制在你仍然可以监督工作流引擎的所有用例，并且很容易与使用它的每个团队交谈的场景中。

当然也可以混搭。例如，您可以在有限数量的微服务之间共享一个数据库，这些微服务在某种程度上是相关的，但是其他团队使用完全独立的引擎。

然而，一个比引擎本身在哪里运行更重要的问题是关于流程模型的所有权和治理:

### 流程模型的所有权

独立于工作流模型的物理部署(理解为:引擎在哪里运行),必须非常清楚**谁**负责某个模型，**在哪里**维护它，**如何**部署变更。

在微服务架构**中，工作流模型的所有权必须属于拥有各自领域**的团队。

[![](../Images/1c70b7a26e0be3aa21c896f3becc52e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7v_arnDX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/896/1%2AL7Zd0tElgCB0NDO-qdvnSQ.png)

在流动零售示例中，有两种工作流模型:

*   订单履行:这属于订单履行业务能力，它的家在订单微服务中。
*   支付:这是支付微服务拥有的。

您必须根据您的领域边界来分配业务流程的所有权，这一点非常重要。不要仅仅因为你做了一个工作流模型，就做一个[BPM monolith](https://blog.bernd-ruecker.com/avoiding-the-bpm-monolith-when-using-bounded-contexts-d86be6308d8)——比如订单执行流程处理业务逻辑和其他与支付相关的细节。

### “编排流程”？

经常有人问我:但是“编排过程”在哪里？这很简单:在我对微服务的理解中，没有编排流程这种东西。

人们通常指的是**端到端的业务流程**，就像上面例子中的订单执行。当然，端到端流程是高度可见和重要的，但它像其他任何东西一样是域逻辑，并且进入微服务边界，在我们的示例中是订单微服务。从这个意义上说，包含端到端编排逻辑的订单微服务可能非常重要，但其组织方式与支付等其他微服务相同。

当说到“编排流程”时，有些人指的是只涉及流程的领域逻辑——因此相应的微服务可能没有其他逻辑(没有自己的数据、没有自己的编程代码等等)。这很好，但它仍然应该是一个微服务，因为即使是这种逻辑也需要明确的责任。通常逻辑会随着时间的推移而发展。

### 总结

所描述的主题是复杂的，没有容易在所有场景中采用的答案。我希望这篇文章能给你一些启发。让我们快速回顾一下:

[![](../Images/a59cb97477f05fe443ddcc44f6a95222.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6uC9TLPM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/779/1%2AF6JQ8-tas-I96sAJGgUdpg.png)

您需要选择您的**通信方式**:异步、RPC-ish 或使用工作流引擎的工作分配。根据您的选择，工作流引擎可以为您提供不同的帮助:

[![](../Images/c11ac6f342edd27adfbd21dd4c232fd1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ahaxweh5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/507/1%2AkWOk1eKl0rwu11mW2_lHKw.png)

**工作流模型的所有权**必须在各自微服务的领域内。工作流应该明确地集中在那个领域。

您可以集中或分散地运行工作流引擎**。**

**跟踪或管理**——你应该努力实现编排和编排的**平衡组合**。

感谢[铁斯·巴瑞尔](https://twitter.com/tiesebarrell)和[迈克·温特斯](https://twitter.com/wints)改进了这篇文章。

Bernd Ruecker 是 [Camunda](http://camunda.com/) 的联合创始人和开发商倡导者。我对开发人员友好的工作流自动化技术充满热情。*关注我的* [*Twitter*](http://twitter.com/berndruecker/) *或关注我的* [*简讯*](http://eepurl.com/cDXPRj) *。像往常一样，我喜欢收到您的反馈。下面评论或者[发邮件给我](http://bernd.ruecker@camunda.com)。*

* * *
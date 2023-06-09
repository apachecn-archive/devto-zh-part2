# 在 VPC 内缩放兰姆达斯

> 原文：<https://dev.to/paulswail/scaling-lambdas-inside-a-vpc-3fik>

因此，您正在构建一个 Lambda 函数，您需要它与 VPC 内部的资源进行对话，可能是 RDS 数据库或 Redis Elasticache。

你听说过 VPC Lambda 函数比标准函数更复杂，并且发现很难从 AWS 文档中获得完整的描述。当相关文档分布在如此多的页面上时，很难知道你不知道什么。

然而，你*确实*知道你需要回答的问题，以便确信你的职能将大规模执行:

*   它能够处理我的最大预计吞吐量吗？
*   有上升限制吗？
*   如果实际使用量超过这些限制，我的应用会受到什么影响？
*   在负载下，冷启动如何影响我的 Lambda 函数？

在本文中，我将向您展示如何回答所有这些问题，并帮助您**确保您的支持 VPC 的功能针对可伸缩性进行了优化配置**。

不在乎理解“为什么”，只想知道“如何”？直接跳到文章底部的**行动计划**部分。👇

## λ缩放概述

让我们先来看看λ函数的[缩放行为:](https://docs.aws.amazon.com/lambda/latest/dg/scaling.html)

> AWS Lambda 将根据您帐户的帐户级并发执行限制，动态扩展容量以应对流量的增加。为了处理任何流量的突发，Lambda 会立即增加你的并发执行函数一个预定的数量，这取决于它执行的区域。

为了详细说明“扩展容量”的含义，每个 Lambda 函数都是在一个容器中运行的(想想 Docker)。在 Lambda 最终处理顺序请求之前，一个容器可以处理几个小时。然而，如果在一个请求正在进行时有另一个请求进来，就需要一个新的容器来处理它。

[帐户级别并发执行限制](https://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html#concurrent-execution-safety-limit)被设置为每个地区 1000(尽管可以通过联系 AWS 支持来增加)。
这意味着您帐户中所有 Lambda 函数的吞吐量上限可以计算如下:

`max_requests_per_second = 1000 / function_duration_seconds`

因此，对于一个平均需要 0.5 秒完成的函数，您可以实现的绝对最大吞吐量是每秒`1000 / 0.5 = 2000`个请求。(但是，如果您的帐户中有其他功能也在此期间执行，此限制将会降低)。

## 向混合物中加入 VPC

当您[将 Lambda 函数连接到 VPC](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html) 时，可伸缩性变得更加复杂:

> 默认情况下，AWS Lambda 在 VPC 中安全地运行您的函数代码。然而，要使您的 Lambda 函数能够访问您的私有 VPC 内部的资源，您必须提供额外的特定于 VPC 的配置信息，包括 VPC 子网 id 和安全组 id。 **AWS Lambda 使用这些信息来设置弹性网络接口(ENIs ),使您的功能能够安全地连接到您的专用 VPC 内的其他资源。**

[![Lambda functions connecting to a VPC via an ENI](img/8b30254444fda5905b6ac29ec5b31dd8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W-DncP__--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://winterwindsoftware.com/img/blog-images/lambda-vpc-eni.png)

在每个函数容器和 VPC 之间建立 ENI 的要求从 3 个方面限制了 Lambda 函数的可伸缩性:

1.  可以创建的 ENI 数量有上限(ENI 容量)，因此限制了每秒可实现的最大请求数
2.  冷启动开销更大
3.  上升率有上限

### 约束#1: ENI 产能

医生建议:

> 如果您的 Lambda 函数访问 VPC，您必须确保您的 VPC 有足够的 ENI 容量来支持您的 Lambda 函数的比例要求。

...他们提供了下面的公式来告诉你你的函数需要多少个 Eni:

`ENI Capacity = Projected peak concurrent executions * (Memory in GB / 3GB)`

因此，如果您的预计峰值负载为 1，000，000 个请求/小时，您的平均功能持续时间为 0.5 秒，并且分配了 1GB 的内存，则 ENI 容量可以计算如下:

`Peak concurrent executions = 1000000/(60*60) * 0.5 = 139`

`ENI Capacity = 139 * (1GB / 3GB) = 47`

#### 计算你的实际 ENI 容量

但是这个公式没有告诉你在你的 VPC 中你当前有多少 ENI 容量*可用*，只告诉你的功能*需要多少*来达到你的预计峰值负载。从与该功能相连的子网内的 IP 地址范围中，为每个 ENI 分配一个专用 IP 地址。因此，为了计算 ENI 容量，我们需要获得所有子网中可用 IP 地址的总数。

为此，您可以使用 [VPC 控制台](https://console.aws.amazon.com/vpc/home#subnets:sort=SubnetId)列出您的 VPC 中的所有子网。然后获取`IPv4 CIDR`列的值，并将其插入 [IP CIDR 计算器](https://mxtoolbox.com/subnetcalculator.aspx)，以获得该范围内可用 IP 地址的数量。对连接到您的函数的每个子网执行此操作，并将这些值相加，得到您的总实际 ENI 容量。

### 约束#2:冷启动开销

所有 Lambda 函数都会导致“冷启动”，这是 AWS 提供一个容器来服务一个函数请求所花费的时间。然而，这种影响[对于启用 VPC 的 Lambda 函数](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html#vpc-configuring)更大:

> 当 Lambda 函数被配置为在 VPC 内运行时，它会导致额外的 ENI 启动损失。这意味着当尝试连接到网络资源时，地址解析可能会延迟。

除了影响导致冷启动的单个请求之外，它还会影响您的上升速率，因为它会有效地增加上升期间的平均功能持续时间。

### 约束#3:上升速率

Ramp-up rate 是 Lambda 提供新的函数容器来满足函数请求增长的速率。[文件](https://docs.aws.amazon.com/lambda/latest/dg/scaling.html#scaling-behavior)描述了如下的上升行为:

> 为了处理任何流量的突发，Lambda 会立即增加你的并发执行函数一个预定的数量，这取决于它执行的区域。
> 如果默认的即时并发增加值不足以适应流量激增，AWS Lambda 将继续以每分钟 500 次的速度增加并发函数执行的数量，直到达到您的帐户安全限制或并发执行函数的数量足以成功处理增加的负载。

“即时并发增加”值对于每个区域是不同的[。对于美国东部(N.Virginia ),是 3000。](https://docs.aws.amazon.com/lambda/latest/dg/limits.html)

但是，对于 VPC 功能:

> 因为 Lambda 依赖 Amazon EC2 为支持 VPC 的 Lambda 函数提供弹性网络接口，所以这些函数在扩展时也会受到 Amazon EC2 的速率限制。

你需要使用 EC2 控制台[检查你的账户](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html)的 EC2 限制，特别是“每个地区的网络接口”限制，默认为 350。如果这个值小于您的函数的“峰值并发执行”数，那么您将需要请求增加这个限制。

## 达到并发限制会怎样？

如果您的函数达到了它的并发限制，那么对它的任何进一步调用都会被抑制。Lambda 应用的[节流行为](https://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html#throttling-behavior)是不同的，这取决于事件源是否基于流，以及它是同步调用还是异步调用。对于同步调用的非基于流的事件源，行为是:

> Lambda 返回 429 错误，调用服务负责重试。ThrottledReason 错误代码解释了您遇到的是函数级限制(如果指定)还是帐户级限制。每个服务可能有自己的重试策略。每个节流调用都会增加 Amazon CloudWatch 函数的节流指标。

因此，如果您在 API 网关后面有一个 Lambda 函数，您的 web API 客户端将会收到一个 429 错误。

另一个[需要注意的事项](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html#vpc-setup-guidelines)是:

> 如果您的 VPC 没有足够的 Eni 或子网 IP，您的 Lambda 函数将不会随着请求的增加而扩展，您将会看到函数故障的增加。 **AWS Lambda 目前不会将由 Eni 或 IP 地址不足导致的错误记录到 CloudWatch 日志中。**

没有一个日志条目来描述为什么会发生这个错误将是一个很难诊断的问题。这种情况发生的唯一标志是 [`Invocation Errors`](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-functions-metrics.html) CloudWatch 指标增加。

## 你的行动计划

现在你知道 Lambda 如何扩展你的支持 VPC 的 Lambda 函数，下面是一个分步指南，确保你的函数为可伸缩性进行了优化配置:

1.  用这个公式计算你的`Peak Concurrent Executions`:`Peak Concurrent Executions = Peak Requests per Second * Average Function Duration (in seconds)`
2.  现在计算你的`Required ENI Capacity` : `Required ENI Capacity = Projected peak concurrent executions * (Function Memory Allocation in GB / 3GB)`
3.  如果`Peak Concurrent Executions` > `Account Level Concurrent Execution Limit`(默认= 1000)，那么你就需要[要求 AWS 增加这个限制](https://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html#concurrent-execution-safety-limit)。
4.  将您的函数配置为使用 VPC 内部所有可用的子网，这些子网可以访问您的函数需要连接的资源。这既最大化了`Actual ENI Capacity`，又提供了更高的可用性(假设子网分布在 2 个以上的可用性区域)。
5.  使用上面“实际 ENI 容量”一节中描述的步骤计算您的`Actual ENI Capacity`。
6.  如果`Required ENI Capacity` > `Actual ENI Capacity`，那么您将需要执行以下一项或多项操作:
    *   减少你的函数的内存分配来减少你的`Required ENI Capacity`。
    *   将任何不需要 VPC 访问的耗时代码重构到单独的 Lambda 函数中。
    *   在您的应用中实现节流处理逻辑(例如，在客户端中内置重试功能)。
7.  如果`Required ENI Capacity` >您的 EC2 [`Network Interfaces per region`](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html) 账户限额，那么您将需要[请求 AWS 增加该限额](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html#request-increase)。
8.  考虑[配置一个函数级并发限制](https://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html#per-function-concurrency)以确保您的函数不会达到 ENI 容量限制，并且如果您希望由于下游架构限制而强制限制在某个限制。
9.  [使用 CloudWatch 指标监控生产中函数的并发级别](https://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html#monitoring-concurrent-usage),这样您就可以知道调用是否由于 ENI 容量不足而被抑制或出错。

🔢如果您的 AWS 帐户中运行了大量 Lambda 函数，您应该尝试使用 [Lambda 缩放计算器](https://winterwindsoftware.com/lambda-scaling-calculator/)来获得一个交互式视图，了解不同的函数配置文件在负载下将如何缩放，以及何时将达到容量。

💌 ***如果你喜欢这篇文章，你可以注册[到我关于在 AWS](https://winterwindsoftware.com/newsletter/) 中构建无服务器应用的每周时事通讯。***
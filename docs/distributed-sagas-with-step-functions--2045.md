# 微服务的分布式传奇

> 原文：<https://dev.to/yos/distributed-sagas-with-step-functions--2045>

[![](img/4dd78cd910c217bd672a78a37e6ce448.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gu3wIQ1Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/MP85ikK.png)

在本文中，了解分布式 saga 模式，它如何帮助确保微服务的正确性和一致性，以及如何使用 AWS Step 函数作为 Saga 执行协调器。

> 这是我在 [AWS 会议](https://www.meetup.com/preview/AWS-SG/events/243871718)上发表的关于“阶梯函数的传奇”的[演讲的一个有抱负的传输。幻灯片可在](https://www.youtube.com/embed/I0PT5UvoJWo)[这里](https://goo.gl/hcB5sU)获得。

## 问题

微服务已经接管了软件。虽然这种方法有它的好处，但也带来了一些额外的复杂性。

[![The Death Star architecture](img/8a10c0f084d639f21efbde23d3a80385.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1QvChIQ3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/CBFiRjl.png)

想象一下，我们正在使用微服务方法构建一个旅游预订平台。

[![A single high-level action involves calling low-level actions across different microservices](img/4dd78cd910c217bd672a78a37e6ce448.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gu3wIQ1Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/MP85ikK.png)

> 在上图中，一个高级业务操作(“预订行程”)包括对不同的微服务进行几个低级操作。

为了处理客户端请求，我们创建了一个单一用途的 edge 服务(前端的后端[),它提供了组合对所有下游服务的调用的逻辑。在其核心，旅行社服务是一个编排层，通过组合不同微服务提供的细粒度功能来公开粗粒度 API。](https://samnewman.io/patterns/architectural/bff/)

[事务](https://en.wikipedia.org/wiki/ACID#Distributed_transactions)是软件应用的重要组成部分。没有它们，就不可能保持数据的一致性。
然而，在微服务领域，我们不再有单一的真相来源。状态分布在不同的服务中，每个服务都有自己的数据存储。

如果所有的服务呼叫都成功完成，那太好了！但是在现实世界中，失败是经常发生的。我们如何处理**部分执行**——当高级动作中的请求子集失败时？

使用微服务方法，您不能只在一个 ACID 事务中预订航班、汽车和酒店。为了保持一致，您需要创建一个分布式事务。

[![Partial execution: the Book Flight request failed while the other service calls succeeded](img/e05bece22b3e5d5d4cd054590db08db9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tcowR5AC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fbED2ot.pngs)

如果机票预订失败，你愿意保留酒店和汽车吗？大概不会。

在这种情况下，我们需要在 edge Travel Agent 服务中实现一些特定的并发控制逻辑，以处理任何潜在的故障，并尝试以某种方式从故障中恢复(我们是否取消了其他预订？如果机票预订后来重试并成功了呢？我们如何知道自己处于什么状态？)

> 如果没有某种并发控制机制，我们就有在应用程序中出现不一致数据的风险——这在分布式系统中尤其糟糕。

可以理解的是，这个控制逻辑可以把*变得非常复杂*。

处理部分失败和异步是困难的...这就是分布式传奇出现的原因。

## 分布式传奇

在分布式系统中，跨多个服务的业务事务需要一种机制来确保跨服务的数据一致性。分布式 Saga 模式**是一种用于管理故障的模式**，其中每个动作都有一个用于回滚的补偿动作。分布式 Sagas 有助于确保跨微服务的一致性和正确性。

[![](img/773a17121bffa10fdb9aa30c42848e46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s64rOWk5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/zTYiuNZ.png)

埃克托·加西亚-莫利纳和肯尼斯·塞勒姆在 1987 年的一篇研究论文中首次使用了“传奇”一词。它是作为长期数据库事务的一种概念性替代方案引入的。

[![](img/21eef40214cd820015443c9f4349ab42.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D7Tmigem--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/6jVNPfw.png)

最近，sagas 已被应用于帮助解决现代分布式系统中的一致性问题——如 2015 年[分布式 sagas 论文](https://github.com/aphyr/dist-sagas/blob/master/sagas.pdf)中所述。

[![](img/1bc4b3b237e3921ccea16efe79399b89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YOJJFmE9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/YTE2sxA.png)

一个**传奇**代表一个高级业务流程(比如预订一次旅行)，它由几个低级**请求**组成，每个请求在一个服务中更新数据。每个请求都有一个**补偿请求**,当请求失败或事件中止时执行。

[![](img/15f19bfb43b2b568fc8a75721cb9e986.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6zUt-Vfz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/9Z9X7PF.png)

例如，我们的旅游预订平台的“预订旅行”系列包括以下**请求** : `BookHotel`、`BookCar`和`BookFlight`。

[![](img/6e15189eef41dd0aa5f36003541e57ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4P_B22Eo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/40tLnQc.png)

一个请求有一个相应的**补偿请求**，该请求在语义上撤销请求。`CancelHotel`撤销`BookHotel`、`CancelFlight`撤销`BookFlight`等等。

> 请注意，某些操作在传统意义上是不可撤销的。
> 
> 发送给错误收件人的电子邮件无法取消发送。然而，我们可以通过发送另一封电子邮件来*语义撤销*该操作，该邮件会说‘对不起，请忽略之前的邮件。’>
> 
> 通过将应用程序的状态恢复到发出请求之前的原始平衡状态，在语义上对请求进行补偿可以撤销请求。

### 分布式佐贺担保

令人惊讶的是，分布式传奇*保证了*以下两种结果之一:

1.  要么**Saga 中的所有请求都成功完成**，要么

2.  请求的子集和它们的补偿请求被执行。

问题是分布式 sagas 要工作，请求和补偿请求都需要遵守某些特征:

*   **请求和补偿请求必须是[等幂](https://en.wikipedia.org/wiki/Idempotence)** ，因为同一个消息可能会被传递多次。无论相同的幂等请求被发送多少次，结果必然是相同的。幂等运算的一个例子是更新运算。非幂等操作的一个例子是每次都生成新的`id`的创建操作。

*   **补偿请求必须是[可交换的](https://en.wikipedia.org/wiki/Commutative_property)** ，因为消息可以按顺序到达。在分布式环境中，补偿请求可能会先于相应的请求到达。如果 a `BookHotel`在`CancelHotel`之后完成，我们仍然应该到达取消的酒店预订(而不是重新创建预订！)

*   请求可以中止，这将触发一个补偿请求。
    **补偿请求不能中止**，无论如何它们都必须执行到完成。

### 作为国家机器的传奇

我们如何定义一个传奇？作为[状态机](https://en.wikipedia.org/wiki/Finite-state_machine)。

[![](img/62a06ad12b69db87b93d6c439553aee2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bCxP81bc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/996aOoa.png)

> 很长一段时间以来，状态机一直是编程中的一个核心概念，非常适合协调许多具有快速、可预测性能的小组件。

状态机由不同的状态组成，每个状态执行一个特定的任务。状态机在组件之间传递数据，并决定应用程序操作的下一步。

### 分布式 Saga 实现方法

有几种不同的方法来实现 Saga 交易，但最常用的两种方法是:

*   **事件驱动的编排**:当没有中央协调时，每个服务产生并监听其他服务的事件，并决定是否应该采取行动。

*   **命令/编排:**当协调者服务负责集中 saga 的决策和排序业务逻辑时。

在本指南中，我们将着眼于后者。借助编排方法，我们定义了一个新的 Saga 执行协调器服务，其唯一职责是管理工作流并在需要时调用下游服务。

### 佐贺执行协调员

Saga 执行协调器是一种编排服务，它可以:

*   存储和解释一个传奇的状态机
*   通过与其他服务对话来执行 Saga 的请求
*   通过执行补偿请求来处理故障恢复

[![](img/7a5f4482d8b5086af8755695ccf55115.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vnljjMJN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/GsbgUQy.png)

构建这样一个服务是一项艰巨的任务，但幸运的是，我们可以重新调整现有服务的用途来实现我们的目标。

### AWS 步进功能

[AWS 步骤功能](https://aws.amazon.com/step-functions/)是一个工作流管理服务，可以用作 Saga 执行协调器来监督我们分布式 Saga 的执行。

[![](img/43cc9c27b08c3fa8426a98e6cc86d8ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PirhqSFu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/2NiZXXF.png)

> AWS Step Functions 是一个 web 服务，使您能够使用可视化工作流来协调分布式应用程序和微服务的组件。您从单独的组件构建应用程序，每个组件执行一个独立的功能或任务，从而允许您快速扩展和更改应用程序。
> 
> Step Functions 提供了一个图形控制台，将应用程序的组件可视化为一系列步骤。它会自动触发和跟踪每个步骤，并在出现错误时重试，因此您的应用程序每次都会按预期顺序执行。步骤函数记录每个步骤的状态，因此当事情出错时，您可以快速诊断和调试问题。

阶跃函数的工作方式如下:

[![](img/b99744fbca946ca7d1243946b79bbb00.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cB01NiHx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/XjQnhmD.png)

它使用自己的 AWS 状态语言领域特定语言来定义状态机:

[![](img/78cf51181a89c091c1ef16e5364d76ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mLbAn5aQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/w1laAxI.png)

有七种状态类型可以用来创建状态机。`Task`可以作为请求和补偿请求，而`Choice`和`Parallel`用于控制流。

[![](img/8371da80b58038fe06b09aae48144f7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fPnM19bN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/56sdoIH.png)

通过使用 [AWS 状态语言](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html)和 Step 函数定义状态机，您可以将组件协调到符合您业务需求的工作流中，添加重试逻辑、错误处理等等:

[![](img/894b5b826f13c583221b49cdbca55c37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r41UV52U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/FJPyX7y.png)

[![](img/64fe75d74e033e25997ac54a6fb99796.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kzaAOHlv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/VtLy9PK.png)

[![](img/923b6e986eec95e07f6495189511c720.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ipBrGGv_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/pCjNqlU.png)

AWS 还为您提供了一个 web 界面，您可以使用它从 AWS 控制台调试和跟踪步骤功能状态机的执行:

[![](img/ec28c17c3253cbb208f6350ef3da09ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4X8oTMnb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/RxLwcap.png)

### 实际操作 AWS Lambda 和 Step 功能

在这一节中，我们将为本指南开头的旅行预订示例构建一个分布式传奇。

[![A single high-level action involves calling low-level actions across different microservices](img/4dd78cd910c217bd672a78a37e6ce448.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gu3wIQ1Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/MP85ikK.png)

> 您可以在 Github 上克隆并运行示例应用程序。

```
git clone git@github.com:yosriady/serverless-sagas.git
cd serverless-sagas
serverless deploy 
```

> 注意，上面的例子要求您在您的机器上设置[无服务器框架](https://www.goingserverless.co)和 AWS 凭证。

部署后，您将获得一系列 AWS Lambda 函数 ARNs，您可以将其用作 saga 中的请求和补偿请求。

转到 AWS 步骤函数控制台，用示例应用程序中包含的 [`states.json`](https://github.com/yosriady/serverless-sagas/blob/master/states.json) 文件创建一个新的步骤函数。
我们使用 AWS 状态语言创建以下状态机:

```
{
  "Comment": "A distributed saga example.",
  "StartAt": "BookTrip",
  "States": {
    "BookTrip": {
      "Type": "Parallel",
      "Next": "Trip Booking Successful",
      "Branches": [
         {
          "StartAt": "BookHotel",
          "States": {
             "BookHotel": {
                "Type": "Task",
                "Resource": "arn:aws:lambda:{YOUR_AWS_REGION}:{YOUR_AWS_ACCOUNT_ID}:function:serverless-sagas-dev-bookHotel",
                "ResultPath": "$.BookHotelResult",
                "End": true
              }
          }
        },
        {
          "StartAt": "BookFlight",
          "States": {
            "BookFlight": {
                "Type": "Task",
                "Resource": "arn:aws:lambda:{YOUR_AWS_REGION}:{YOUR_AWS_ACCOUNT_ID}:function:serverless-sagas-dev-bookFlight",
                "ResultPath": "$.BookFlightResult",
                "End": true
              }
          }
        },
        {
          "StartAt": "BookCar",
          "States": {
             "BookCar": {
                "Type": "Task",
                "Resource": "arn:aws:lambda:{YOUR_AWS_REGION}:{YOUR_AWS_ACCOUNT_ID}:function:serverless-sagas-dev-bookCar",
                "ResultPath": "$.BookCarResult",
                "End": true
            }
          }
        }
      ],
      "Catch": [
        {
          "ErrorEquals": ["States.ALL"],
          "ResultPath": "$.BookTripError",
          "Next": "Trip Booking Failed"
        }
      ]
    },
    "Trip Booking Failed": {
      "Type": "Pass",
      "Next": "CancelTrip"
    },
    "CancelTrip": {
      "Type": "Parallel",
      "Next": "Trip Booking Cancelled",
      "Branches": [
        {
          "StartAt": "CancelHotel",
          "States": {
            "CancelHotel": {
              "Type": "Task",
              "Resource": "arn:aws:lambda:{YOUR_AWS_REGION}:{YOUR_AWS_ACCOUNT_ID}:function:serverless-sagas-dev-cancelHotel",
              "Catch": [
                {
                  "ErrorEquals": ["States.ALL"],
                  "ResultPath": "$.CancelHotelError",
                  "Next": "CancelHotel"
                }
              ],
              "ResultPath": "$.CancelHotelResult",
              "End": true
            }
          }
        },
        {
          "StartAt": "CancelFlight",
          "States": {
            "CancelFlight": {
              "Type": "Task",
              "Resource": "arn:aws:lambda:{YOUR_AWS_REGION}:{YOUR_AWS_ACCOUNT_ID}:function:serverless-sagas-dev-cancelFlight",
              "Catch": [
                {
                  "ErrorEquals": ["States.ALL"],
                  "ResultPath": "$.CancelFlightError",
                  "Next": "CancelFlight"
                }
              ],
              "ResultPath": "$.CancelFlightResult",
              "End": true
            }
          }
        },
        {
          "StartAt": "CancelCar",
          "States": {
            "CancelCar": {
              "Type": "Task",
              "Resource": "arn:aws:lambda:{YOUR_AWS_REGION}:{YOUR_AWS_ACCOUNT_ID}:function:serverless-sagas-dev-cancelCar",
              "Catch": [
                {
                  "ErrorEquals": ["States.ALL"],
                  "ResultPath": "$.CancelCarError",
                  "Next": "CancelCar"
                }
              ],
              "ResultPath": "$.CancelCarResult",
              "End": true
            }
          }
        }
      ]
    },
    "Trip Booking Successful": {
      "Type": "Succeed"
    },
    "Trip Booking Cancelled": {
      "Type": "Fail",
      "Cause": "Trip cancelled due to error.",
      "Error": "TripCancelledError"
    }
  }
} 
```

在上面的状态机中:

*   我们定义一个起始根状态`"StartAt": "BookTrip"`
*   在`States`部分，我们在我们的状态机中定义了一个状态列表，例如`BookHotel`和`CancelHotel` `Task`状态，以及用于并行执行的`BookTrip` `Parallel`状态。
*   每个状态可以有一个`Catch`子句，如果返回某种类型的错误，该子句允许您将执行路由到其他状态。在我们的例子中，我们为`BookTrip`配置了一个 catch，这样如果分支`BookHotel`、`BookFlight`和`BookCar`中的任何一个出错，就会执行补偿请求`CancelHotel`、`CancelFlight`和`CancelCar`。
*   如果执行失败，我们还使用`Catch`让我们的补偿请求自己重试。

> 记得更新任何`"Resource": "arn:aws:lambda:{YOUR_AWS_REGION}:{YOUR_AWS_ACCOUNT_ID}:function:serverless-sagas-dev-cancelCar"`以使用上一步部署的 AWS Lambda 函数 ARNs。

创建后，您将能够在 AWS 控制台上可视化和执行您的状态机:

[![](img/4b6e44543e9abf878856b156d0809df3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SC_AnTJ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vtqTzRg.png)

> 试试 AWS 状态语言和步骤函数吧！

### AWS 步骤功能汇总

AWS Step Functions 使用可视化工作流轻松协调分布式应用程序和微服务的组件。该服务使得管理与多个组件通信的分布式 sagas 变得简单。

AWS Step Functions 为您管理操作和底层基础设施，以帮助确保您的应用程序在任何规模下都可用。

# 在关闭

我们了解了什么是分布式 Sagas(微服务中处理故障的模式)以及它如何解决分布式事务的问题。它**有助于确保微服务**的正确性和一致性。

我们还了解了 Saga 执行协调器，以及如何开始使用 AWS 状态语言和 AWS 步骤函数定义状态机。

> [示例应用](https://github.com/yosriady/serverless-sagas)在 Github 上。只需点击部署！

感谢您的阅读！

# 补充阅读

*   [分布式传奇谈](https://www.youtube.com/watch?v=0UTOLRTwOX0)
*   [分发 Sagas 论文](https://github.com/aphyr/dist-sagas/blob/master/sagas.pdf)
*   [AWS 步进功能](https://aws.amazon.com/step-functions/)
*   [AWS 状态语言](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html)
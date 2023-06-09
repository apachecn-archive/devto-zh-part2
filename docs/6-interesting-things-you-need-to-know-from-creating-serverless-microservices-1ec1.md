# 创建无服务器微服务需要知道的 6 件趣事

> 原文：<https://dev.to/kylegalbraith/6-interesting-things-you-need-to-know-from-creating-serverless-microservices-1ec1>

[https://api.parler.io/ss/player?url=https://www.parler.io/audio/73240183203/d1c66092c7eadf368f8b9b1f3936a7e4849446f9.3391a1a7-2f32-4ac3-920d-6ac96f29f93c.mp3](https://api.parler.io/ss/player?url=https://www.parler.io/audio/73240183203/d1c66092c7eadf368f8b9b1f3936a7e4849446f9.3391a1a7-2f32-4ac3-920d-6ac96f29f93c.mp3)

我的最新项目 [parler.io](https://www.parler.io/) 是使用无服务器微服务从头开始构建的。现在它已经启动并运行了，似乎有必要记录当前的架构、我获得的优势以及我遇到的一些棘手问题。

首先，简单介绍一下 Parler 的背景。这是一项允许你将任何博客文章从 RSS 源转换成音频的服务。该音频可以下载、嵌入或发布到其他平台。

### 游戏中的工具和语言

在撰写本文时，该架构由五种不同的无服务器微服务组成。

每个微服务的基础设施都通过结合使用 [Terraform](https://www.terraform.io/) 和[无服务器框架](https://serverless.com/)来管理。这种工具的混合是我迄今为止所走的一条路，它很有效*好吧*，但是，它确实引入了一点混乱。

例如，你可能会想 Terraform 和无服务器框架管理的是什么。它大致分为这两类。

1.  Terraform 管理维护给定服务所需的任何构建、测试或部署基础设施。
2.  无服务器管理服务实际运行的基础设施，如 AWS Lambda 函数、API 网关端点，甚至 SNS 主题或用于事件处理的 SQS 队列。

老实说，有时候这感觉有点不对劲。原因是我可以很容易地将所有的基础设施移出`serverless.yml`并用 Terraform 来表示。我没有这样做的原因是，在部署作为 CI/CD 管道一部分的服务运行的基础设施时，我喜欢无服务器框架给我带来的简单性。

此外，我还喜欢使用 Terraform 模块构建新的微服务及其支持基础设施的简单性。通过一个 Terraform 模块，我可以代表一个新微服务的所有必要部分。这包括 AWS CodeCommit 中的新 Git 存储库，以及使用 AWS CodePipeline 和 CodeBuild 的整个 CI/CD 管道。

人们可能会有另一个迫切的问题，你的无服务器功能使用的是什么语言？打字稿。

做了很多。我已经成为打字爱好者，但我也非常喜欢 Node 的灵活性和工具性。因此，TypeScript 是一个很好的中间分割，到目前为止我对它很满意。

### 无服务器微服务架构

正如我前面提到的，有五种不同的无服务器微服务可以让 Parler 从书面博客帖子中产出高质量的音频。

在前端，我们有`parler-io`服务，它实际上是向用户显示的前端，以及支持这样做的 AWS 架构。毫不奇怪，该网站是完全静态的，使用盖茨比 V2 建立，并通过 AWS S3 和 CloudFront 的组合托管。如果这是一个有趣的前端架构，你想探索，[考虑检查我的学习 AWS 使用它的课程，我们了解所有关于](https://www.kylegalbraith.com/learn-aws)。

前端与几个不同的 API 端点通信。首先，我们有由 API 网关和 AWS Lambda 函数组成的`load-post`服务。它负责接收您的 RSS 提要并返回您可以选择转换的文章集合。

然后我们有了另一个 API 网关和 Lambda 组合的`post-conversion`服务。它有一个端点接收您想要转换的帖子，并进行必要的处理以将其转发给`conversion`服务。稍后我们会详细介绍这项服务。

接下来是最大的微服务，`status`服务。就前端而言，它的主要工作是返回转换的状态。本质上是请求的帖子已经被转换、处理，或者在某个地方出错了。它还具有额外的逻辑，用于为给定的转换返回嵌入的播放器。AWS 架构方面，由多个 API 网关和 Lambda 端点，两个事件驱动的 Lambda 函数组成，主存储为 DynamoDB。

最后一个微服务是`conversion`服务，它处理一篇博客文章到音频的实际转换。这由另一个 Lambda 函数组成，该函数接受一个转换作业，加载 post，并将其传递给一组语音转换服务，包括 AWS Polly。转换状态通过 SNS 主题发送，`status`服务可以监听该主题。

所有五个服务都是使用 AWS CodeCommit、CodePipeline 和 CodeBuild 的组合独立构建、测试和部署的。如前所述，这些服务是使用 Terraform 提供和维护的。

现在我们已经了解了情况，让我们来了解一下我遇到的一些棘手问题，以及随着项目的扩展我希望改进的地方。

### 观察至今

在将 parler.io 构建到这个早期 alpha 版本的过程中，我从微服务的总体环境中学到了一些有价值的经验。下面是我试图归纳的一些对其他项目有益的方法。

1.  **选择一个对你有意义的工具链。**虽然感觉很奇怪，但我的`terraform`和`serverless`工具对我来说效果很好。当然，我可以选择一个或另一个，这将使游戏中的工具最小化。然而，我必须做一些翻译。所有这些都是说，选择有助于你快速迭代交付的工具，但也不要害怕未来的支点。现在有意义的，6 个月后可能就没意义了。
2.  管理跨服务边界的依赖是很困难的。这是微服务(无服务器或无服务器)世界的普遍情况。作为开发者，我们要尽可能的遵守 DRY(不要重复自己)。说起来感觉很奇怪，但从长远来看，打破干燥或最小化跨微服务的共享代码是有益的。我发现共享代码*是必要的*是在前端/后端服务之间的日志、监控或模型等方面。前者我已经转移到一个单独的回购，可以在其他服务中利用。后者我仍在努力确定我想走哪条路。
3.  **一开始是一个清晰的微服务，将来可能会变成不止一个。**我在上面简单地谈过这个问题，`status`服务已经变得有点大了。这里有不止一组业务逻辑，这可能意味着该服务在不久的将来应该被分解。我对此的假设是:懒惰地重构我的数据模型，这引入了与一个数据存储的耦合，DynamoDB。注意你的数据是如何被访问和隔离的，这可以让你的服务之间的界限更加清晰。

这些是我从微服务中学到的一般观察和经验。我不认为这三个中的任何一个是专门针对无服务器微服务本身的。然而，下面是我对亚马逊网络服务中无服务器微服务的一些观察。

1.  **无服务器框架非常适合为给定服务提供、部署和维护基础设施，但它还可以做得更好。与大多数框架一样，框架中有一些微妙的东西是奇怪的或缺失的。例如，没有现成的 Lambda 函数的 SQS 触发器。**
2.  **每个微服务一个 AWS 账户。**这是*而不是*Parler 架构的当前状态，但很可能是我前进的方向。通过在帐户级别隔离服务，我们可以获得一些好处。首先，我们的资源限制可以具体限定在帐户中运行的服务范围内。第二，其他资源，如 VPC、安全组、权限等。可以特定于单个服务。最后，就 AWS Lambda 而言，我们可以避免成为自己吵闹的邻居:您的一个函数窃取了所有调用，实际上使其他函数处于饥饿状态。
3.  **AWS 码*服务相当不错**。我之前提到过，我有一个 [Terraform 模块](https://github.com/slalompdx/terraform-aws-codecommit-cicd)，可以为新的微服务搭建所有必要的基础设施。这包括新的 CodeCommit repo、CodePipeline 和两个 CodeBuild 阶段。这个装置太棒了。实际上，我有一个给定微服务的 CI/CD 和一个 Terraform 模板，并更新了一个`buildspec.yml`，以便运行我需要的步骤。这些服务还没有获得最好的声誉，因为在 CI/CD 领域还有许多其他工具(例如 Circle CI、Travis 等)，但我认为人们应该再看看这些工具。

### 包装完毕

我不会将本文中的任何内容归类为适用于您自己的无服务器架构的宽泛建议。这些只是我在开发 [parler.io](https://parler.io) 早期收集的一些想法和经验。

也就是说，我认为在构建下一个微服务或产品时，记住这些是有用的。

无服务器为您带来的强大功能、灵活性和敏捷性不容否认。当然也有不切实际的情况。如果你有一个持续运行的 AWS Lambda 函数，其成本可能会失控。

但是，对于大量的工作负载，无服务器微服务将释放您的能力，让您只专注于服务试图解决的问题。如果您自动化您的部署，使用基础设施作为代码，并有一些工具来保持轻量级。使用无服务器，您可以真正加速您的应用程序开发。

如果您有兴趣了解更多关于 [parler.io](https://parler.io) 的信息，请查看并亲自体验一下。当我升级到 MVP 版本时，我总是在寻找关于如何改进服务的反馈。
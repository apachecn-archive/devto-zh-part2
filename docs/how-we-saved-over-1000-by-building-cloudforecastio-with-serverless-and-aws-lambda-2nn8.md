# 我们如何通过使用无服务器和 AWS Lambda 构建 CloudForecast.io 节省了 1，000 多美元。

> 原文：<https://dev.to/cloudforecast/how-we-saved-over-1000-by-building-cloudforecastio-with-serverless-and-aws-lambda-2nn8>

在之前的博客[帖子](https://medium.com/cloudforecast/benefit-from-what-we-learned-spending-2-million-annually-on-aws-f747e6a3bbb3)中，我们描述了我们推出[【云预报】](https://www.cloudforecast.io/?utm_source=medium&utm_medium=blog&utm_campaign=serverless) 的旅程，以及每个初创公司面临的一些问题。在本帖中，我们将回答这个问题“用我们的资源构建这个产品的正确方法是什么？”

**“100 美元创业”概念**

一年多来，我一直痴迷于一本名为《100 美元创业》的书，作者是克里斯·吉列博。在他的书中，克里斯建议他的读者快速推出他们的产品，同时要具有成本效益，以在你的想法失败时限制损失。)。我们试图将这一建议应用到我们业务的各个方面，包括我们的技术决策。

**我们的目标/要求**

牢记 100 美元的启动概念，我们首先列出了在构建 CloudForecast 时对我们很重要的需求:

*   **快速发布**:我们很高兴能够构建 CloudForecast 来帮助公司节省 AWS 的成本。我们想尽快把它展示给我们的客户，以保持我们的兴奋感。花费时间调试配置、部署流程等。不会使最终产品变得更好。我们一直在寻找一种开箱即用的解决方案，可以让我们一起成长，并让我们能够立即专注于我们的 MVP。
*   **成本效益**:作为一家早期创业公司，我们希望通过构建一个能够根据我们的客户群无缝扩展的系统来避免固定成本。

**AWS Lambda +无服务器=简单+专注+高性价比**

对于下一步，我们就如何实现我们的目标和要求进行了头脑风暴。我们讨论了管理我们自己的实例，使用容器和半打其他想法，但我们想要简单的东西，所以我们决定使用无服务器解决方案(或 FaaS)。虽然有一些缺点(例如冷启动等)，但我们认为无服务器更适合我们的使用情形(零管理、无闲置成本的按执行付费和自动扩展)。

对于开发设置和部署流程，我们决定将[无服务器框架](https://serverless.com/)用于 AWS Lambdas，原因如下:

*   我们可以专注于编写我们的产品，让无服务器管理其余的(许可、事件管理)
*   易于配置和部署
*   无服务器支持多种平台(GCP、AWS 等)，这可以避免将来的麻烦。我们首先选择了 AWS Lambdas，因为我们的客户数据将存储在 S3。为了降低网络成本，这是一个合理的决定
*   伟大的文档和社区(Github，Gitter，Slack)

我们最初的需求相当简单:我们需要 4 个 AWS lambda 函数，每个函数都有自己的 cron 工作，每个 lambda 都需要与各种 AWS 产品(RDS、DynamoDb、SQS 等)对话。所有这一切都通过一种简单的方法来管理多个环境(开发与生产)通过一种简单/有效的方法来管理资源权限。

我们是这样做的:

*   我们根据环境使用了 4 个[函数](https://serverless.com/framework/docs/providers/aws/guide/functions/)和[外部 JSON 文件](https://serverless.com/framework/docs/providers/aws/guide/variables#reference-variables-in-other-files)变量:

[![](../Images/dc0535eb5b51cedb025fb558d59fa61d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kTbRVJDy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AE58m7w8y2BYeDgV9)

*   使用了 [iamRoleStatements](https://serverless.com/framework/docs/providers/aws/guide/iam/#custom-iam-roles) 来配置所有权限

[![](../Images/82a728df2f5ddda2c24e18b5e1c0a697.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hDuonfSd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2Ax0fjcElVTbIeVz_3)

有了这两个代码片段，我们能够建立我们的大部分架构。4 将通过 SNS 相互交互的功能。两个函数将在 cron 计划上运行 check(通过“schedule.rate”参数)以检查需要重新处理的文件，并通过 SNS 触发另一个函数。这种配置将能够在控制成本的同时轻松扩展。我们能够使用 iamRoleStatements 来配置权限，从而完全隔离我们的环境。我们使用一个外部变量来管理简单的 YAML 文件中的环境变量。

我们考虑运行几个小实例来完成一项工作，这一年至少要花费我们 1000 美元。同时，AWS Lambda 成本是没有意义的，因为我们一天只运行数万个函数。

下一步是什么？

使用无服务器帮助我们快速启动 CloudForecast.io。通过让我们的第一批客户了解他们的 AWS 支出，我们为他们节省了数千美元。

我们正在为下一个项目将 AWS Athena 集成到 CloudForecast 中。与 AWS Athena 集成将带来新的功能，使最终用户能够更深入地了解他们的 AWS 支出。每个公司都有独特的消费模式，这将让他们完全明白他们的钱花在了哪里。我们将很快写更多关于我们的雅典娜集成。

如果你对这篇文章或我们在 [CloudForecast.io](https://www.cloudforecast.io/?utm_source=medium&utm_medium=blog&utm_campaign=serverless) 的工作有任何疑问，请随时通过[Francois @ cloud forecast . io](//mailto:francois@cloudforecast.io)联系我。我们希望收到您的来信！

-弗朗索瓦&云预报团队

*特别感谢[凯尔·加尔布雷斯](https://dev.to/kylegalbraith)审阅本草稿。查看他的每周简讯边做边学。“边做边学”是一份免费的每周策划的云、区块链和编码简讯，每周发送到您的收件箱。*
# 无服务器应用程序的连续交付模式

> 原文：<https://dev.to/markoa/continuous-delivery-patterns-with-serverless-applications-28fl>

无服务器技术的采用肯定在增长，新兴的产品公司在其上构建。CloudZero 最近给[写了一篇详细的
文章](https://www.cloudzero.com/blog/continuous-delivery-in-the-world-of-serverless)，讲述了他们为全无服务器系统实现连续交付的经验。

无服务器是一个新的范例，在这个范例中，我们既不需要管理也不需要扩展运行我们代码的服务器。承诺是需要更少的操作工作来为客户提供价值。

问题是代码部署单元现在是一个函数，而不是任意复杂的应用程序。部署还包括配置功能所连接的托管服务和基础架构。这意味着需要新的工具和实践来实现最佳的开发人员生产力，一如既往，这围绕着能够快速移动。正如 CloudZero 的 Ben Peterson 所指出的，“我们的目标是从发货到生产的时间始终不超过五分钟”。这是如何实现的:

1/ **在开发和测试环境中复制您的无服务器云提供商的条件**。例如， [LocalStack](https://localstack.cloud) 使用 Docker 在
localhost 上提供 AWS 云 API(dynamo db、S3、Kinesis 等)。

2/ **使用框架管理代码部署**。例如，[无服务器框架](https://serverless.com)提供了在任何云供应商上配置和部署应用程序的单一工作流。AWS 客户可以使用 [SAM](https://github.com/awslabs/serverless-application-model) 在代码中定义应用。无服务器应用程序没有单独的“构建”和“部署”步骤，因为没有开发人员需要管理的工件——这是由框架处理的，您的 CI/CD 工具应该本机支持此工作流。

3/ **安全存储云密钥和其他秘密**。由于部署应该是自动化的，CI/CD 工具也需要支持这一点。例如，AWS 密钥不一定能被公司的每个人访问，当然也不能被整个互联网访问。如果你的项目是开源的，持续集成是面向公众的，交付是面向公司的。例如，[信号量](https://semaphoreci.com/?utm_source=devto&utm_medium=social&utm_campaign=serverless)为存储和管理组织范围的秘密提供了一流的支持，可以跨项目重用，并与团队权限混合使用。

4/ **自动化测试仍然是工作流程的关键部分**。前端的普通 JavaScript 测试工具，后端功能的 Python / Go / Java 工具都适用。无服务器功能很小，自动化测试应该运行得很快。

5/ **拥抱多语言编程**。例如，CloudZero 开发人员选择的语言是 Python，但他们也使用 Kotlin 和 Gradle 围绕 AWS Kinesis 构建数据管道。

6/ **将代码库的验证(CI)与交付(CD)分开**。正如本·彼得森指出的:

> 像我们的技术堆栈一样，我们管理的应用程序集也是多样化的，这包括开发工作流和部署到我们的 AWS 客户的要求。Semaphore 在这些方面提供了灵活性——支持私有回购和管理公共存储库和贡献者分支的能力，为不同分支的部署提供不同的逻辑，保持部署细节私有，以及从某些分支手动部署。支持每个分支的不同部署配置也非常有用。

要更深入、实际地探究开发和部署无服务器应用程序的细节，[请阅读 CloudZero 博客](https://www.cloudzero.com/blog/continuous-delivery-in-the-world-of-serverless)上的完整文章。

对于无服务器的有效 CI/CD，您有什么建议？欢迎在评论中分享。

*本帖原载于[旗语博客](https://semaphoreci.com/blog/2018/08/22/continuous-delivery-patterns-with-serverless.html)* 。
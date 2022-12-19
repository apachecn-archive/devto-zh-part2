# 了解 AWS 批处理:简介和示例项目

> 原文：<https://dev.to/joshuaakahn/understanding-aws-batch-a-brief-introduction-and-sample-project-3248>

最近，我把大部分开发工作都集中在“无服务器优先”上——如果我能用 AWS Lambda 构建它，我会的。也就是说，有些类型的计算工作负载根本不适合无服务器模式。在大多数情况下，这些往往是长时间运行的作业或大数据分析，需要数小时的持续工作。

基于 [AWS Batch](https://aws.amazon.com/batch/) 构建的解决方案允许开发人员通过专注于所需的业务逻辑来构建高效、长期运行的计算作业，同时 AWS 管理工作的调度和供应。我最近为一个客户进行了一次批量调查，并构建了一个[样本项目](https://github.com/jkahn117/aws-batch-image-processor)，我想在本帖中分享一下。

### AWS 批量简介

批处理计算跨多个计算实例异步和自动运行作业。虽然运行单个作业可能是微不足道的，但大规模运行多个作业，尤其是有多个依赖项的作业，可能更具挑战性。这就是使用 AWS Batch 这样的完全托管服务带来显著优势的地方。

AWS Batch 将其工作分为四个部分:

*   **作业** —提交给 AWS 批处理的工作单元，无论是作为 shell 脚本、可执行文件还是 Docker 容器映像来实现。
*   **作业定义** —描述如何执行您的工作，包括 CPU 和内存要求以及提供对其他 AWS 服务的访问的 IAM 角色。
*   **作业队列** —列出您的作业要完成的工作。您可以利用具有不同优先级的多个队列。
*   **计算环境** —运行您工作的计算资源。环境可以配置为由 AWS 管理或由您自己管理，还可以配置为运行作业的实例的数量和类型。您还可以允许 AWS 选择正确的实例类型。最后，AWS 拥有的调度器评估何时何地运行已经提交给作业队列的作业。

下一节中描述的示例项目为我们的简单批处理作业定义了上述每一项(调度程序除外)。我们只关注业务逻辑的开发(完成批处理工作)和如何完成该工作的配置…AWS 管理其余的工作。

### 样本项目:图像处理

AWS Batch 可以支持各种资源密集型任务，从分析复杂的财务数据到筛选药物相互作用。也就是说，我们的示例相当简单…通过 [Amazon Rekogniton](https://aws.amazon.com/rekognition/) 处理和标记图像(是的，这可能是一个 Lambda 函数，但我们今天重点关注批处理)。

[![Sample project architecture](../Images/6a9f5a11b779118d951921505f075673.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OKql385P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.iamjkahn.com/assets/images/1%2AkmsdH3HOpadg9ek3S25Qew.png)

当图像被放入 S3 桶中时，会调用一个 Lambda 函数，该函数将提交一个新的 AWS 批处理作业。该作业被实现为 Docker 容器映像，它存储在 [Amazon 弹性容器注册中心(ECR)](https://aws.amazon.com/ecr/) 中。我们的工作是一个简单的 Ruby 应用程序，它接受一组命令行参数作为其工作的输入。我已经使用[地形](https://www.terraform.io/)来管理这个项目的基础设施(见 [template.tf](https://github.com/jkahn117/aws-batch-image-processor/blob/master/template.tf)

从我对 AWS 批处理的探索中获得的一些意见/经验:

*   需要三种不同的 IAM 角色:(1)批处理服务角色；(2)底层实例的 EC2 实例角色；以及(3) ECS 任务角色(与 DynamoDB 和 Rekognition 交互)。任务角色最为熟悉，因为它定义了作业可以与之交互的 AWS 服务；但是，实例角色也很重要(使用*amazonec 2 containerserviceforec 2 role*托管角色)。
*   AWS Batch 提供了[故障排除指南](https://docs.aws.amazon.com/batch/latest/userguide/troubleshooting.html)来帮助解决常见问题。例如，由于没有使用前面提到的实例角色，我遇到了“卡在可运行状态的作业”。
*   作为作业定义的一部分，您可以定义参数和环境变量。我使用参数作为创建作业时可能覆盖的值，使用环境变量作为不应更改但需要传递给作业的值(例如 AWS 区域)。
*   作业定义中定义的命令类似于 Docker RUN 命令。这里，我选择通过命令行与我的作业交互，通过作业定义命令传递参数值。

关于实现的更多细节，请参见我在 GitHub 上的 [aws-batch-image-processor 知识库。](https://github.com/jkahn117/aws-batch-image-processor)
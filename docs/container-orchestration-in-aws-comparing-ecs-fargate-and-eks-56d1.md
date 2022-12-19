# AWS 中的容器编排:比较 ECS、Fargate 和 EKS

> 原文：<https://dev.to/diogoaurelio/container-orchestration-in-aws-comparing-ecs-fargate-and-eks-56d1>

在进入新的酷小子，即 AWS EKS，AWS 托管的 Kubernetes 产品之前，你可能想了解它是如何工作的，并与现有产品进行比较。在本文中，我们将重点区分不同的 AWS 容器编排解决方案，即 AWS ECS、Fargate 和 EKS，并比较它们的优缺点。

### **简介**

在我们深入比较之前，让我们总结一下每个产品是关于什么的。

ECS 是 AWS 提供的第一个容器编排工具。它基本上由 EC2 实例组成，这些实例已经安装了 docker，并运行一个 Docker 容器与 AWS 后端进行对话。通过 ECS 服务，您可以启动任务——不受监控的容器，通常适用于短期操作——以及服务容器，AWS 会监控这些容器，并保证在它们因任何原因关闭时重新启动。与 Kubernetes 相比，它相当简单，有利有弊。

Fargate 是即将到来的第二个服务，旨在从你那里抽象出容器之下的所有东西(运行它们的 EC2 实例)。换句话说，一个纯粹的容器即服务，你不需要关心容器在哪里运行。Fargate 遵循了 ECS 中的两项核心技术进步:将 ENI 直接分配给一个集装箱的可能性以及在集装箱级别上集成 IAM。我们稍后将对此进行更详细的讨论。

下面的图片来源于 AWS 博客这里的[说明了 ECS 和 Fargate 服务之间的区别。](https://aws.amazon.com/blogs/compute/aws-fargate-a-product-overview/)

[![fargate-1](../Images/5efbd5d9bcbf228fc96a52b1a15aa450.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lG8rksDY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datacenternotes.files.wordpress.com/2018/10/fargate-1.png)

EKS 是最新的产品，仍然只在部分地区提供。使用 EKS，您可以抽象启动 Kubernetes 集群的一些复杂性，因为 AWS 现在将管理主节点——控制平面。Kubernetes 是一个更加丰富的容器编排器，提供了诸如网络覆盖、允许您隔离容器通信和存储供应等功能。不用说，管理起来也要复杂得多，在开发运维工作方面的投资也更大。

像 Kubernetes 一样，您也可以使用 kubectl 与 EKS 集群通信。您将需要在本地配置 [AWS IAM 认证器，以便与您的 EKS 集群](https://github.com/kubernetes-sigs/aws-iam-authenticator)进行通信。

### **成本**

让我们从成本开始，这是软件开发人员经常忽视的头号嫌疑。

**ECS** ，是一个简单的服务，其中控制平面被 AWS 抽象掉。您只需启动 Worker 节点，它由安装了 Docker 的 EC2 实例和一个运行的容器守护程序组成，该守护程序包含最少的元数据，可以知道它属于哪个集群，并从 AWS ECS 控制平面接收指令。因此，使用 ECS，您只需为每个工作节点付费，请阅读 EC2 实例。

Fargate 从你那里抽象出容器的托管平台 EC2 实例。这显然意味着，与 ECS 相比，您要为每个集装箱支付额外费用。所以是的，Fargate 每个集装箱的成本自然会更高。另一方面，为了与纯 ECS 进行公平的比较，您还应该考虑与 ECS 相关的维护成本。故障排除、升级 ECS 实例代理、更新 EC2 实例包等所花费的时间。，转化为工资的形式。因此，不应该被遗忘。

**EKS** ，作为幕后的 Kubernetes，需要主节点一直运行以监控集群，即 EKS 控制平面。这不是免费的，将花费您最低 0.20 美元/小时，相当于每月 144 美元。这只是开始到处玩，没有任何工作者节点。从现在开始，您只需支付为 Worker 节点启动的 EC2 实例的正常价格，就像 ECS 产品一样。

### **自动缩放**

ECS 允许您在任务级别进行扩展。记住:一个任务可能是一个或多个容器。与 EC2 非常相似，您可以配置规则来扩展正在运行的任务数量。然而，这些容器运行在 EC2 实例上，可能没有可用的 CPU 或 RAM 资源。因此，在实践中，这里的技巧是在 ECS 群集级别定义另一个自动扩展规则，您可以根据群集上的 CPU 或 RAM 预留来定义扩展或缩减。

**Fargate** 正如预期的那样，使自动缩放变得更加容易。在这里，您只需要在任务级别定义它，就可以开始了。

有关 [ECS/Fargate 自动缩放的更多信息，请点击此处](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html)。

就在最近 [AWS 为 EKS](https://aws.amazon.com/blogs/opensource/horizontal-pod-autoscaling-eks/) 引入了与 Kubernetes 水平 Pod 自动缩放器(HPA)的集成。与 ECS 非常相似，这将允许您在 pod 级别进行扩展，这也意味着您还需要在 EC2 级别进行扩展以支持 pod 增长。更多关于开始使用 [HPA 的信息，请点击](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)。

此外，如果您已经运行了一个 EKS 集群，请确保您的 [EKS 版本支持 HPA](https://docs.aws.amazon.com/eks/latest/userguide/platform-versions.html) 。所有新的 EKS 集群都将推出平台版本 eks.2+，这意味着在撰写本文时它们已经支持 HPA。

现在，让我们从技术上更深入地了解区分这三种服务的三个主要网络方面。

### **联网:流量负载均衡**

在 AWS 将 EKS 与他们更复杂的负载平衡器(即应用 LB 和网络 LB)集成之前，从网络的角度来看，流量仍然会非常低效。由于 EKS 只支持经典的 AWS ELB，流量在 EC2 实例中随机、盲目地分布，容易出现多跳传播。

ECS 和 Fargate 都清晰地与 AWS ALB 和 Network LB 集成，允许您直接指向目标容器。Cloudnout 在关于这个话题的博客文章中提供了很好的插图。

### **联网:ENI**

乍一看，将网卡直接分配给容器似乎不是什么大事。然而，这实际上意味着更高级别的安全性。这样，您就能够分配一个专用于该单独容器的安全组，而不是简单地打开整个集群的所有端口。

使用 **ECS** ，您可以通过选择在“awsvpc”模式下启动任务，选择将 ENI 与容器直接关联。然而——总有但是，不是吗？-可分配给每个 EC2 实例的 Eni(读取虚拟网卡)的最大数量因 EC2 类型而异；有人可能会说，目前这一数字仅限于 2 到 15。因此，如果您运行许多小型容器，这个场景实际上意味着创建一个由小型 EC2 实例组成的非常大的 EC2 集群。[下面的列表举例说明了这种说法](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI)。

另一方面，使用 EKS，您可以选择为一个容器组分配一个专用网络接口，Kubernetes 称之为 pod。因此，这意味着该 pod 内的所有容器将共享同一个内部网络。然而，好消息来了，您可以在每个实例中放置多达 750 个 pod。这对 EKS 来说是一个更大的优势。

### **联网:服务发现**

AWS 最近启动了 ECS/Fargate 的服务发现。这是 ECS 落后于 Kubernetes 的一个关键区别，Kubernetes 使用网络覆盖来实现本质上的容器级 DNS。

然而，需要注意的是，只有在“awsvpc”网络模式下启动的容器才支持此功能。

### **安全策略控制(AWS IAM 集成)**

回到安全主题，支持 ECS/Fargate 的另一个优点是可以将 IAM 实例概要文件分配给容器。这些政策规定了什么？基本上可以访问所有其他 AWS 资源，如 S3 桶，数据库/数据仓库，如 DynamoDB，Aurora，Redshift，队列系统，如 SQS 和 Kinesis 等。你明白了。

同样，这里的论点类似于 ENI:在容器级别分配专用 IAM 角色比在 EC2 实例级别安全几个数量级。

ECS 和 Fargate 允许您在容器级别指定权限。目前，EKS 不允许你这样做。这意味着您只能在 Workers/EC2 级别限制权限。

### **集装箱登记- AWS ECR**

这里没有什么特别的:正如预期的那样，所有服务都与 AWS 容器注册中心集成，允许您从内部 AWS 注册中心提取。

### **总结**

总之，ECS 和 Fargate 与 AWS 生态系统的集成更加紧密。另一方面，Kubernetes 与通用开源生态系统有着更紧密的集成，允许您更加不受云供应商的限制。

像往常一样，这里是一些有用的资源汇编，用来写这篇文章，可能对你也有帮助:

*   [Nathan Peck 的 AWS docker 编排工具介绍比较](https://medium.com/containers-on-aws/choosing-your-container-environment-on-aws-with-ecs-eks-and-fargate-cfbe416ab1a)
*   [来自 Cloudnaut 的精彩技术总结](https://cloudonaut.io/eks-vs-ecs-orchestrating-containers-on-aws/)
*   [Fargate 简介，并附有详细的 GUI 演练](https://medium.com/@remy.dewolf/aws-fargate-first-hands-on-experience-and-review-1b52fca2148e)
*   [AWS ECS/Fargate 自动扩展配置文件](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html)
*   [集装箱级直接 ENI 映射的 AWS 新网络模式“awsvpc”](https://aws.amazon.com/blogs/compute/introducing-cloud-native-networking-for-ecs-containers/)
*   [AWS 关于 Fargate 的博文](https://aws.amazon.com/blogs/compute/aws-fargate-a-product-overview/)
*   [ECS 任务角色的 AWS 文档(容器级)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html)
*   [AWS EKS 和 ClodFormation 教程演练](https://hackernoon.com/quickly-spin-up-an-aws-eks-kubernetes-cluster-using-cloudformation-3d59c56b292e)
*   [ECS 支持服务发现](https://aws.amazon.com/blogs/aws/amazon-ecs-service-discovery/)
*   [EKS 水平自动缩放](https://aws.amazon.com/blogs/opensource/horizontal-pod-autoscaling-eks/)
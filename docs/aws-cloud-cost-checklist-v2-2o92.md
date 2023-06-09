# AWS 云成本清单 V2

> 原文：<https://dev.to/sensedeep/aws-cloud-cost-checklist-v2-2o92>

*最初发布于:[https://www . sense deep . com/blog/posts/stories/check list-to-lower-AWS-cloud-costs . html](https://www.sensedeep.com/blog/posts/stories/checklist-to-lower-aws-cloud-costs.html)T3】*

对于许多公司来说，云计算是变革性的。这些优势是引人注目的:提高了灵活性，增强了响应能力，而且我们不要忘记，降低了资本支出。

然而，在云中创建服务器、数据库、负载平衡器和容器的便利性和速度往往会导致失控和成本增加——有时甚至会令人震惊。

这是清单的第二版。它从[版本 1](//checklist-to-lower-aws-cloud-costs-v1.html) 开始进行了重组，增加了新的内容来跟踪快速发展的云环境。感谢贡献物品的人。我尽量保持列表紧凑和集中，但如果你有一个项目应该添加到列表中，请评论。

## **取得控制权**

*   [ ]定期审核您的资源。清点并检查您是否需要您创建的所有资源和服务。很容易在云中开始一些事情，然后就迷失了方向。这包括:EC2 实例、RDS 数据库、elb、快照、ECS 任务、VPC、安全组等。 [Howto](https://aws.amazon.com/config/) 。

*   [ ]使用一致的资源标签对资源进行分类，并追溯到所属团队。 [Howto](https://aws.amazon.com/about-aws/whats-new/2014/01/29/track-and-manage-instance-use-and-spending-with-new-ec2-usage-reports/) 。 [Howto](https://aws.amazon.com/answers/account-management/aws-tagging-strategies/) 。

*   [ ]在他们自己的帐户中运行开发、测试和试运行环境，与生产环境分开。这使得控制云资源变得更加容易，也更加安全。使用 [AWS 组织](https://aws.amazon.com/organizations/)合并计费。 [Howto](https://aws.amazon.com/organizations/) 。

*   [ ]不同地区的价格差异很大。例如:一个 AWS On-Demand M4.large 在美国东部 1 区是 73 美元/月，在亚太东南部 2 区是 91 美元/月。选择离你的顾客最近的最便宜的地区。 [Howto](https://aws.amazon.com/ec2/pricing/on-demand/) 。

*   [ ]定期(可能每季度一次)运行 AWS Trusted Advisor，检查是否存在容量过剩和安全问题。 [Howto](https://console.aws.amazon.com/trustedadvisor/home?#/dashboard) 。

*   [ ]按预期每月预算的 25%、50%和 75%启用计费警报。这样，当事情失去控制时，你会很快得到提醒。 [Howto](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/gs_monitor_estimated_charges_with_cloudwatch.html) 。

## **实例**

*   [ ]选择并重新评估每个应用程序的实例类型。实例类型的价格可以相差几个数量级。 [Howto](https://www.ec2instances.info/) 。

*   [ ]迁移到较新的实例类型。AWS 通常鼓励通过价格迁移到更新的实例类型。例如:M5.large 在美国东部 1 区是 70 美元，而 M4.large 在同一地区是 73 美元。

*   [ ]按 CPU、内存和磁盘监控您的应用程序性能，以找到多余的容量以及减少实例类型和附加存储需求的机会。

*   [ ]现货实例通常是最便宜的实例，比按需价格低 80%。但是现场服务器是短暂的，可以在没有警告的情况下终止。对可变的非基本能力使用现场实例。在大多数地区，下班后和周末的现货价格是最便宜的。为 AWS 回收您的所有 Spot 实例做好准备。 [Howto](https://console.aws.amazon.com/ec2sp/v1/spot/launch?region=us-east-1) ， [Howto](https://aws.amazon.com/ec2/spot/instance-advisor/) 。

*   [ ]如果不使用现场实例，您的恒定按需生产能力可以在保留的实例上。如果可能的话，预先付款以锁定最低价格。检查您的账单，确保您使用了所有购买的保留实例容量。 [Howto](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#ReservedInstances:sort=reservedInstancesId) 。

## **缩放**

*   [ ]根据负载而不是过度调配来扩展您的自动扩展组和数据库副本。如果 CPU 持续 5 分钟大于 60%,则考虑向上扩展；如果持续 20 分钟小于 30%,则考虑向下扩展。 [Howto](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html) ， [Howto](https://aws.amazon.com/autoscaling/) 。

*   [ ]关闭所有空闲资源。评估何时不需要您的开发、测试和试运行环境。仅通过这一步，您就可以节省高达 70%的云费用。

*   [ ]端接未使用的 elb。根据需要使用 [Terraform](https://www.terraform.io/) 进行破坏和重建。

*   [ ]如果使用容器，您可以通过打包容器来减少实例总数，从而最大限度地提高服务器/群集实例上的 CPU 和内存利用率。

## **无服务器**

*   [ ]将无服务器模式用于具有峰值负载的基于事件的服务。你不用为无服务器的空闲时间或管理服务器付费。

*   [ ]不要将无服务器用于持续、繁重的负载。对于此类工作负载，无服务器的运营成本是专用服务器的 3 倍。 [Howto](https://medium.com/@PaulDJohnston/when-not-to-use-serverless-jeff-6d054d0e7098) 。

## **网络流量**

*   [ ]通过在边缘缓存静态内容，减少实例的网络流量。考虑一下 [CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html) 、 [CloudFlare](https://www.cloudflare.com/) 等边缘缓存服务。

*   []elb 非常昂贵，尤其是如果每个微服务使用一个 ELB。您可以通过使用不同的目标规则在多个服务上共享一个 ALB，它适用于具有多个证书的 SSL。 [Howto](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html) 。

*   [ ]如果您的出站网络数据速率非常高，请设计您的部署，以最大限度地减少区域间和区域间的流量。使用多个 az 对于可用性来说是必不可少的，但是尽量将流量局限在 az 内。 [Howto](https://datapath.io/resources/blog/what-are-aws-data-transfer-costs-and-how-to-minimize-them/) 。

## **储存**

*   [ ]随着时间的推移，S3 存储可能会成为一项巨大的成本。制定政策，定期检查不需要的 S3 存储。

*   [ ]扫描孤立的 EBS 快照和分离的 EBS 卷。这些通常会随着时间的推移而累积，因为当某些实例终止时，未连接的卷不会自动删除。

*   [ ]将很少访问的 S3 数据迁移到 [AWS Glacier](https://aws.amazon.com/glacier/) 。检索速度较慢，但成本低得多。 [Howto](https://www.online-tech-tips.com/computer-tips/how-to-move-amazon-s3-data-to-glacier/) 。

*   [ ]为所有 CloudWatch 日志设置过期限制。默认设置是永不过期。 [Howto](https://console.aws.amazon.com/cloudwatch/home?#logs:) 。

## **自动化**

*   [ ]尽早投资工具和自动化——人力成本很高。使用控制台非常适合原型开发，但是在云中实现自动化的回报是巨大的。自动化资源创建、审核、集中日志记录和安全扫描。

*   [ ]使用自动化工具，如 [CloudFormation](https://aws.amazon.com/cloudformation/) 或 [Terraform](https://www.terraform.io/) ，而不是手动创建基础架构。这不仅更加安全，而且使控制和审计资源更加容易。 [Howto](https://blog.gruntwork.io/an-introduction-to-terraform-f17df9c6d180) 。

*   [ ]在重新发明轮子之前利用 90 多种 AWS 服务。与您自己构建相比，AWS 构建块通常可用于更快、更低成本地获得解决方案。

## **移动**

*   [ ]考虑其他云提供商。虽然总体而言，AWS 可能具有竞争力，但你可能会发现替代的云提供商，如 [Azure](https://azure.microsoft.com) 和 [Google Cloud](https://cloud.google.com/) 对于你的特定工作负载来说要便宜得多。

*   [ ]知道何时离开云。在大规模一致的情况下，对于某些服务，内部托管可能比云托管更可取。 [Dropbox](https://dropbox.com/) 和[栈溢出](https://www.stackoverflow.com/)就是因为这个原因从云端迁移了服务。

## 关于 SenseDeep

SenseDeep 是 AWS 开发人员的一个观察平台，用于加速无服务器应用程序的交付和维护。SenseDeep 通过集成的无服务器开发人员工作室，在整个生命周期中帮助开发人员创建可观察、可靠和可维护的应用，该工作室包括设计和调试工具以及对无服务器应用的深入洞察。

要尝试 SenseDeep，请在浏览器中导航至:

[https://app.sensedeep.com](https://app.sensedeep.com)。

要了解有关 SenseDeep 的更多信息，请访问:

[https://www.sensedeep.com/product](https://www.sensedeep.com/product)。
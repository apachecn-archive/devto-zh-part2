# 如何降低 AWS 云成本:清单

> 原文：<https://dev.to/sensedeep/how-to-lower-aws-cloud-costs-a-checklist-29eb>

*最初发布于:[https://www . sense deep . com/blog/posts/stories/check list-to-lower-AWS-cloud-costs . html](https://www.sensedeep.com/blog/posts/stories/checklist-to-lower-aws-cloud-costs.html)T3】*

对于许多公司来说，云计算是变革性的。这些优势是引人注目的:提高了灵活性，增强了响应能力，而且我们不要忘记，降低了资本支出。

然而，在云中创建服务器、数据库、负载平衡器和容器的便利性和速度往往会导致失控和成本增加——有时甚至会令人震惊。

该清单是一组简单的项目，有助于减少您的云账单。

如果你有什么我可以添加到列表中的，请评论。

## **控制—仅运行您需要的内容**

[ ]很容易在云中开始一些事情，然后就迷失了方向。监控您拥有的按类型运行的资源的数量，并追溯到拥有它的团队。大量使用资源标签进行分类。 [HowTo](https://aws.amazon.com/about-aws/whats-new/2014/01/29/track-and-manage-instance-use-and-spending-with-new-ec2-usage-reports/) 。

[ ]定期审核您的资源。清点并检查您是否需要正在运行或已经创建的所有资源和服务。这包括:实例、RDS 数据库、elb、快照、ECS 任务、VPC、安全组等。

[ ]按预期每月预算的 25%、50%和 75%启用计费警报。这样，当事情失去控制时，你会很快得到提醒。 [HowTo](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/gs_monitor_estimated_charges_with_cloudwatch.html) 。

[ ]定期(可能每季度一次)运行 AWS Trusted Advisor，以解决容量过剩和安全问题。 [HowTo](https://console.aws.amazon.com/trustedadvisor/home?#/dashboard) 。

## **挑选合适的区域**

[ ] AWS 价格因地区而异。比如:按需 M4.large 在美东-1 是 73 美元/月，在 AP-东南-2 是 91 美元/月。选择离你的顾客最近的最便宜的地区。 [HowTo](https://aws.amazon.com/ec2/pricing/on-demand/) 。

## **选择正确的实例类型**

[ ]选择并重新评估每个应用程序的实例类型。实例类型的价格因数量级而异。慎重选择。按 CPU、内存和磁盘监控您的应用程序性能，以发现过剩的容量和缩减实例类型的机会。 [HowTo](https://www.ec2instances.info/) 。

[ ]迁移较新的实例类型。AWS 有时鼓励通过价格迁移到更新的实例类型。例如:M5.large 在美国东部 1 区是 70 美元，而 M4.large 在同一地区是 73 美元。

## **使用基本生产能力的预留实例**

[ ]您的固定生产基础能力应该基于保留的实例。如果可能的话，预先付款以锁定最低价格。检查您的账单，确保您使用了所有购买的保留实例容量。 [HowTo](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#ReservedInstances:sort=reservedInstancesId) 。

## **使用现货实例**

[ ]现货实例通常是最便宜的实例，比按需价格低 90%。但是斑点是短暂的。对可变的非基本能力使用现场实例。在大多数地区，下班后和周末的现货价格是最便宜的。准备好 AWS 收回你所有的点。 [HowTo](https://console.aws.amazon.com/ec2sp/v1/spot/launch?region=us-east-1) ， [HowTo](https://aws.amazon.com/ec2/spot/instance-advisor/) 。

[ ]考虑一个自动 spot 替换服务，它将按需实例透明地转换为 spot ( [自动 spot](https://autospotting.org/))。这在自动缩放组中特别有用。

## **掉电闲置资源**

[ ]关闭所有空闲资源。评估何时不需要您的开发、测试、qa 和试运行环境。仅通过这一步，您就可以节省高达 70%的 DevOps 账单。

[ ]关闭未使用的 elb。根据需要使用 [Terraform](https://www.terraform.io/) 进行破坏和重建。

## **放大缩小**

[ ]根据负载扩展您的自动扩展组和数据库副本。如果 CPU 持续 5 分钟大于 60%,则考虑向上扩展；如果持续 20 分钟小于 30%,则考虑向下扩展。 [HowTo](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html) ， [HowTo](https://aws.amazon.com/autoscaling/) 。

## **聚合 ELBs**

[]elb 很贵，尤其是如果您每项微服务使用一个 ELB 的话。使用较新的 AWS ALB 服务，您可以通过使用不同的目标规则在多个服务上共享单个 ALB。它也可以通过多个证书与 TLS 一起工作。 [HowTo](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html) 。

## **减少网络流量**

[ ]通过在边缘缓存静态内容，减少实例的网络流量。考虑 [CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html) 、 [CloudFlare](https://www.cloudflare.com/) 等边缘缓存服务。

## **修枝贮藏**

[ ]随着时间的推移，S3 存储可能会成为一项巨大的成本。制定政策，定期检查不需要的 S3 存储。对孤立的 EBS 快照和分离的 EBS 卷执行类似的操作。

[ ]将很少访问的 S3 数据迁移到 [AWS Glacier](https://aws.amazon.com/glacier/) 。检索速度较慢，但成本低得多。 [HowTo](https://www.online-tech-tips.com/computer-tips/how-to-move-amazon-s3-data-to-glacier/) 。

[ ]为所有 CloudWatch 日志设置过期限制。默认设置是永不过期。 [HowTo](https://console.aws.amazon.com/cloudwatch/home?#logs:) 。

## **知道什么时候离开**

[ ]知道何时离开云。在大规模一致的情况下，内部托管可能比云托管更可取。 [Dropbox](https://dropbox.com/) 和[栈溢出](https://www.stackoverflow.com/)就是因为这个原因从云端迁移过来的。

## 关于 SenseDeep

SenseDeep 是 AWS 开发人员的一个观察平台，用于加速无服务器应用程序的交付和维护。SenseDeep 通过集成的无服务器开发人员工作室，在整个生命周期中帮助开发人员创建可观察、可靠和可维护的应用，该工作室包括设计和调试工具以及对无服务器应用的深入洞察。

要尝试 SenseDeep，请在浏览器中导航至:

[https://app.sensedeep.com](https://app.sensedeep.com)。

要了解有关 SenseDeep 的更多信息，请访问:

[https://www.sensedeep.com/product](https://www.sensedeep.com/product)。
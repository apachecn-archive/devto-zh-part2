# 轻松连接到 EC2 实例

> 原文：<https://dev.to/ajardin/connecting-to-ec2-instances-without-a-headache-3onl>

AWS 提供了一个强大的特性，叫做**自动缩放**。如果在您的基础架构上配置它，您将能够自动调整容量，以尽可能低的成本保持最佳性能。但是在 EC2 实例上打开 SSH 连接会更加困难，因为相关的 IP 地址会定期改变。

使用自动缩放有两个主要优点([源](https://aws.amazon.com/ec2/autoscaling/))。

> 您可以使用 Amazon EC2 Auto Scaling 对 EC2 实例进行集群管理，以帮助维护您的集群的健康和可用性，并确保您正在运行所需数量的 Amazon EC2 实例。
> 
> 您还可以使用 Amazon EC2 Auto Scaling 来动态扩展 EC2 实例，在需求高峰期间自动增加 Amazon EC2 实例的数量，以保持性能，并在淡季减少容量，以降低成本。

除此之外，通过使用**堡垒架构**来保护您的应用服务器是一个安全最佳实践。换句话说，只有一个 EC2 实例将暴露在公共子网中，而所有其他 EC2 实例都将配置在私有子网中。要访问这些服务器，您需要通过 bastion 主机打开一个 SSH 连接。

[![AWS bastion architecture](img/3ddd3636457226b7caddbef5817241f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r_1tLAH4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ajardin.fr/public/img/bastion_architecture.png)

即使这是云基础设施的首选方法，但当您需要通过 SSH 访问应用服务器时，这种方法就不那么用户友好了。希望这不是你在“生产就绪”平台上经常做的事情，因为 AWS 提供了足够的服务(尤其是 CloudWatch)让你监控所有的实例。

但是，当您在构建新的基础设施时，能够快速 SSH 到特定的 EC2 实例是很有用的。默认情况下，您需要首先从 AWS 控制台检索 IP 地址，然后将它们粘贴到您的终端中。当 EC2 实例一天更新几次时，这是对时间的巨大浪费。

有时，我决定创建一个小脚本来改善这种情况。原理非常简单:我将使用我们之前定义的 instances 标记，而不是使用 IP 地址，以方便计费监控。如果你想用我的剧本，你需要三样东西:

*   EC2 实例上的标签`Environment`，基本上是相关平台的名称。
*   EC2 实例上的标签`Name`，以`Environment`值为前缀。
*   在执行脚本的计算机上有一个名为`jq`的命令行 JSON 处理器。

长话短说，一切都在下面描述！您可能需要更改的唯一一件事是 SSH 帐户，因为我在我的代码片段中加入了`ec2-user`。
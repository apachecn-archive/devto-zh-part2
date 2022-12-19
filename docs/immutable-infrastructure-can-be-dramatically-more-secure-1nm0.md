# 不可变的基础设施可以更加安全

> 原文：<https://dev.to/sensedeep/immutable-infrastructure-can-be-dramatically-more-secure-1nm0>

*最初发布于:[https://www . sense deep . com/blog/posts/stories/immutable-infra structure-can-be-draulic-more-secure . html](https://www.sensedeep.com/blog/posts/stories/immutable-infrastructure-can-be-dramatically-more-secure.html)T3】*

不可变服务器是这样一种服务器，它一旦被部署，就永远不会被修改、修补或升级。如果需要的话，它仅仅被一个新的更新的实例所替换。不可变的基础设施将这种方法扩展到整个云。

当资源被恶意修改时，不可变的基础设施可以更容易地检测到，从而本质上更加安全。

Chad Fowler [在 2013 年介绍了不可变服务器](http://chadfowler.com/2013/06/23/immutable-deployments.html)，当时他将普通的可变基础设施描述为:

> 它变得很挑剔。它只接受以某种手动方式进行的部署。初始化脚本不再工作，除非你做一些特殊的和意想不到的事情…系统变成了一个纸牌屋。你害怕任何改变，害怕取代它，因为你不知道它是如何运作的。

不可变基础设施的美妙之处在于，它不仅解决了许多可靠性问题，还转变了安全性。

## 易变性，为妥协之矢

可变性是黑客的氧气。几乎所有持续攻击都会以某种方式修改底层系统。这些变化包括替换二进制文件或库、添加程序或根工具包、开放网络端口和修改关键数据。防止这些变化是防止攻击的关键。检测这种变化对于有效防御至关重要。

> 尽管如此，防止对易变系统的修改是很困难的。检测不可变系统上的变化很容易。

在一个被修补和更新的可变系统中，很难确切地知道对系统的更改是否被授权。Linux 让这变得尤其困难。

在 Linux 中，二进制文件和库分散在许多目录中:/boot、/bin、/usr/bin、/usr/lib、/opt/bin、/usr/local/bin 等等。配置文件同样分布在/etc、/opt/etc、/usr/local/etc、/usr/lib 等目录中。这些目录中有些文件不应该被修改，有些文件会定期更新。当系统更新服务运行时，它们通常也会在这些目录中创建临时文件。因此，很难锁定所有这些目录，同时执行授权的系统和软件更新。

通过各种手段，您可以尝试检测哪些修改是授权的，并且只允许在特定的时间窗口内进行系统更新。但这也为黑客打开了机会之窗。聪明的攻击者可以利用他们的黑客攻击来配合授权的系统更新，从而“偷偷通过大门”而不被发现。

> 不可变的服务器解决了这一切。对于不可变的服务器，任何改变都是妥协的表现——因为任何修改都是不被授权的！

当入侵检测系统与不可变服务器耦合时，它们可以实时检测系统的变化，并立即隔离、停止或终止服务器。

扩展到整个云，不可变范式使整个云服务能够被精确配置和监控任何未经授权的更改。因此，攻击者很难在不被立即发现的情况下修改或破坏服务。

在云端，Terraform 可以审计配置的偏差，并可以重新应用所需的配置。在 AWS 上，这意味着检测 VPC、服务组、帐户、IAM 角色等的变化。

[![](img/97fb3e0f4f8506ede592ddc4109200d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MM2YcnAn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wjcib4e51ezffbxcpblk.png)

这是云中安全操作的未来。不变的基础设施已经存在，并将成为规范。有了像 [Terraform](https://www.terraform.io/) 这样的工具，构建不可变的服务器和配置比以往任何时候都更容易。

## 关于 SenseDeep

SenseDeep 是 AWS 开发人员的一个观察平台，用于加速无服务器应用程序的交付和维护。SenseDeep 通过集成的无服务器开发人员工作室，在整个生命周期中帮助开发人员创建可观察、可靠和可维护的应用，该工作室包括设计和调试工具以及对无服务器应用的深入洞察。

要尝试 SenseDeep，请在浏览器中导航至:

[https://app.sensedeep.com](https://app.sensedeep.com)。

要了解有关 SenseDeep 的更多信息，请访问:

[https://www.sensedeep.com/product](https://www.sensedeep.com/product)。
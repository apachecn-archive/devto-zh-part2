# AWS 安全组的 5 个不容忽视的最佳实践

> 原文：<https://dev.to/totalcloudio/5-not-to-ignore-best-practices-for-aws-security-groups-2e14>

为了 24X7 全天候保护 AWS 资源免受不必要的攻击，您必须始终拥有 VPC、网络访问控制列表(NACLs)和 AWS 安全组(SG)的正确组合。

> [T0】 AWS security group is a cloud firewall to help protect applications and data. They restrict access to certain IP addresses or resources. They always guard your AWS security boundary, as long as you configure them in the right way! Here are five best practices that should never be ignored when configuring AWS SGs.

## **#1 确保 EC2 SGs 没有大范围的端口打开**

大型端口范围打开时，会将实例暴露给不必要的攻击。更重要的是，它们使得追踪漏洞变得非常困难。例如，您的 web 服务器可能只需要打开 80 和 443 端口，而不是所有端口。

我们观察到的最常见的错误之一是用户打开 0.0.0.0/0 范围的所有端口，急于访问实例，然后忘记修复它！

在 EC2 实例上运行时，您必须将您的应用仅暴露给在相应安全组级别上启用的那些端口。

## **#2 明智地使用 ELB 的 SGs 限制 ec2 的上网**

即使 web 服务器的端口 80 或 443 需要访问 0.0.0.0/0，也不需要直接打开。使用 AWS ELB 接收来自互联网的所有传入流量，并将其转发到这些 web 服务器或 ec2，然后只允许这些 ec2 接收来自 ELB 的传入流量。将这些实例的入口规则锁定到 ELB SG。这样，没有人能够直接接触到 ec2。

## **#3 从不保留独立的安全组&将修改仅限于某些角色**

AWS 上的安全配置是动态的。管理员可以随时更改这些配置。由于存在未连接的 SG，错误配置的风险会增加，因为这使得管理员很难在大规模基础架构中找出连接的和未连接的 SG。他/她更改错误 SG 的配置或者意外地将它们附加到公共实例的可能性变得很高，从而无意中使您的环境受到攻击。

专家提示:识别未使用的安全组的最简单方法是浏览安全组列表，选择全部，然后单击删除。AWS 显示附加的 SG 列表，并提示仅删除未附加的 SG。单击删除后，只会删除未连接的 SG。最好的方法是使用自动化程序来完成这项工作，作为您定期审计的一部分。

就像管理员不小心误配置了 SG 一样，其他有权修改 SG 的 IAM 角色也可能误配置 SG。仅授权特定用户修改特定于资源的安全组。

通过一起检查实例元数据、网络 ACL 和 SG，持续关注 IAM 策略。只有经过选择的用户才能更改给定区域中的特定安全组，以便他们可以根据需要进行更新或修改。

## **#4 不忽略 SG 的出站规则；**果断设置限制

默认情况下，AWS SG 没有任何入口规则，出站端口对互联网开放。这强调了一个事实，即不仅仅是进入规则需要关注。然而，出口规则也需要注意。因此，连接到应用层的 SGs 应该只允许到它需要连接的层的出口连接。例如，如果 web 层仅被授权发起到应用层的连接，则出口规则应该仅指向应用特定的端口和特定的子网(例如，VPC、安全组)。

## **#5 跟踪 SGs 创建时的变化率&其端口打开&关闭，生产中**T3】

如果创建了一个 SG，并且其端口在很短的时间内打开和关闭，比如说不到 30 分钟，则通常是可疑活动。尤其是在生产环境中。最好的方法是在生产环境中解析 AWS Cloud Trail 日志，与通常的模式进行比较并监控活动。

## **结论:**

AWS 安全组还有数十种其他最佳实践。比如避免向生产环境的其他实例开放 SSH/RDP。所有这些都很重要，但上面列出的是“必须做的事情”可以用木偶、大厨、RunDesk 等工具。与 AWS 一起自动执行安全审计。然而，在不同的仪表板上跟踪所有这些工具是令人不知所措的。

此外，SG 本身无助于降低安全风险。正如我们之前所说，VPC、NACLs 和 SGs 的正确组合可以弥补您基础设施的安全性。

> ### Having said that, multiple dashboards to monitor and manage all these resources will consume the team's energy. A single visual console presents the relationship between all these resources, showing all port connections and details of entry and exit, which is helpful to quickly discover vulnerabilities.

TotalCloud Inc .即将推出这样的安全观。这种新视图提供了一个集中的视觉环境，能够实时提示 3D 空间中的安全漏洞。所以请继续关注我们！

附注:我们也列出了 AWS NaCl 的[五大最佳实践。](http://blog.totalcloud.io/5-not-to-ignore-best-practices-for-aws-nacls-network-access-control-lists/)

注:最初发布于 2018 年 8 月 24 日 [blog.totalcloud.io](https://blog.totalcloud.io/5-not-ignore-best-practices-aws-security-groups/) 。
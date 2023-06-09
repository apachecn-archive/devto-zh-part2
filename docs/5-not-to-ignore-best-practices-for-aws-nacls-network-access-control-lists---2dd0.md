# AWS NACLs(网络访问控制列表)的 5 个不容忽视的最佳实践

> 原文：<https://dev.to/totalcloudio/5-not-to-ignore-best-practices-for-aws-nacls-network-access-control-lists---2dd0>

亚马逊网络服务(AWS)从业者一次又一次地建议将 AWS NACLs(网络访问控制列表，也发音为“Nakles”)、VPC 和 AWS 安全组(SGs)正确地结合起来，以 24X7 全天候保护资源免受不必要的攻击。

AWS NACLs 充当相关子网的防火墙，控制入站和出站流量。而 SGs 在资源级别充当防火墙。

在我们之前的一篇帖子中，我们谈到了针对 AWS 安全组的 5 个不容忽视的最佳实践。在本帖中，我们将带您了解 NACLs 的一些最佳实践。

# **AWS NACLs 的无状态之美**

在应用 AWS NACLs 的最佳实践之前，理解其基本特征以及通过其无状态行为微调流量的能力是很重要的。

与有状态的 SG 不同，AWS NACLs 是无状态的。因此，适用于传入规则的更改将不适用于传出规则。也就是说，如果您希望实例通过端口 80 (HTTP)进行通信，那么您必须添加允许端口 80 的入站和出站规则。

在配置 NACLs 之前，必须记住一些建议，例如:

## **#1 小心默认的 NACLs，尤其是那些带有生产服务器的**

创建 VPC 时，它会附带一个默认网络 ACL，允许所有入站和出站规则。如果创建自定义 NACL，入站和出站规则都会被拒绝。如果您尚未创建自定义网络 ACL，则子网将自动与 VPC 的默认 ACL 相关联。这将“允许”所有流量流入和流出网络。

> ***“谨慎地允许流量进出 NACLs，尤其是在运行生产服务器的情况下。最重要的是，持续检查允许所有入站流量的 NACLs。”***

下面是一个例子:将一个 NACL 分配给一个公共子网，该子网包含可以通过端口 80 (HTTP)和临时端口 1024-65535 接收和发送互联网流量的实例。并拦截端口 2049 (NFS)或易受拒绝服务攻击的端口上的流量。

> ***“一定要确保在配置过程中不要使用大范围的端口或者过度许可 NACLs，除非你的应用或者容器需要大范围的端口，比如 Kubernetes。”***

## **#2 结合 VPC 内的安全组使用，完善安全**

在 VPC 配置 SGs 和 NACLs 有助于减少 AWS 上托管的应用程序的攻击面。它们相互补充，因为 SGs 允许定义应用程序内资源的流量，而 NACLs 允许定义应该在子网级别明确拒绝的端口、协议和流量源。

这里有一个例子来说明 SGs 和 NACLs 是如何互补的。比方说，您有一个两层的 web 应用程序，web 服务器在一个 SG 中，数据库在另一个 SG 中。入站 NACL 规则允许连接到世界各地的 web 服务器，而数据库只允许来自一组 web 服务器的入站连接。因此，web 服务器允许端口 443 连接，而数据库允许 MySQL 的入站 3306 连接。

> ***"要防止服务器通过互联网发起连接，您可以删除 web 服务器和数据库 SGs 的出站规则。这样，web 服务器将允许所有出站流量建立会话。并且数据库必须将出站连接限制在 web 服务器的专用子网 IP 范围内。***

## **#3 留意无效的 NACL‘否定’法则**

无效或错误配置的拒绝规则助长了对 VPC 的“过度许可”访问。这会导致 DoS 或 DDoS 等攻击。请注意网络 ACL 中拒绝规则的顺序。这一点至关重要，因为 ACL 是按顺序评估的。例如，在下图中，拒绝规则被定义为阻止到易受攻击端口 2049 的入站流量。如果规则不阻止任何人的访问(0.0.0.0/0)，入站拒绝规则将被声明为无效，应重新配置以防止攻击。

## **#4 考虑限制**

在您的 AWS 基础设施中配置 NACLs 之前，最好先了解它们的局限性。这里有一些你绝对不能忽视的限制:

-每个列表的入站和出站规则默认限制为 20 个。AWS 根据要求提供额外的规则，但是绝对最大值是 40。

-每个 VPC 的网络 ACL 数量上限为 200。

## **#5 检查 NACLs 上无限制的出站流量**

创建/应用网络 ACL 时，您可以应用入站限制或出站限制。在配置期间，注意允许来自所有端口的流量的出站规则。限制对所需端口或端口范围的访问。这使得不需要的角色拥有最少的特权，并在子网级别降低了未经授权访问的可能性。

# **按 AWS NACL 规则玩**

虽然最佳实践有助于避免错误或不必要的流量，但有几条 NACL 规则是你绝对不能忽视的，例如:

-NaCl 总是以升序读取，每个规则都应用于匹配的数据包。不管后面的规则是否匹配，这些规则都适用。用有组织的编号系统仔细排列 NACL 规则是很重要的。

- AWS 网络 ACL 规则(入站和出站)根据目的端口定义

-编号可以从 1 开始，最大为 32766。在赋值时，建议在每个 NACL 规则之间留出至少 50 个数字的间隔，这样就有足够的空间给序列中的其他规则供以后使用。另外，在创建规则时，不要忘记应用出站回复规则来允许对入站请求的响应。

-每个网络 ACL 包括一个编号为星号(“*”)的规则。如果数据包与任何其他编号的规则都不匹配，此规则可确保入站/出站流量被拒绝。这条规则既不能修改也不能删除。

-指定创建自定义规则时所分配协议使用的端口范围。

# **结论:**

作为第二道防线，NACLs 按规则运行。但是您需要在不同的场景下适当地配置它们。AWS 记录了以下场景的规则:

[场景 1: VPC 拥有一个公共子网](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenario1.html)

[场景二:VPC 有公网和私网](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenario2.html)

[场景 3:具有公共和私有子网的 VPC 和 AWS 管理的 VPN 访问](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenario3.html)

[场景 4:只有私有子网的 VPC 和 AWS 管理的 VPN 访问](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenario4.html)

此外，使用 Terraform，您可以编写 NACLs 规则。这有助于创建一个安全的基础设施，并将其作为代码(IaaC)格式保存在基础设施中。

将上述所有规则考虑在内并应用最佳实践，您总是可以改善您的 AWS 安全状况并减少攻击面。

但是，仅仅按照最佳实践配置 NACLs 是不够的。持续检查这些是最重要的。如果您正在使用 Terraform 代码或类似于 [云托管](https://github.com/capitalone/cloud-custodian) 的工具来监控和验证 NACL 规则，这并不重要。

最重要的是如何分析路由表和 NACL 配置，并进一步持续映射子网和 NaCl 之间的全部入站和出站流量，以实时维护 AWS 安全。

> ### ***Having said that, it is still laborious to switch between multiple tabs and dashboards or lines of code. It is the best way to use a single visual console to analyze and display the entire AWS network topology from VPC level to resource level.***

## TotalCloud Inc .将很快推出一种新的视图，该视图将提供一个集中的视觉环境，实时提示 3D 空间中的安全漏洞。所以请继续关注我们！

-结束-

###### 参考文献:

###### [T1】https://docs . AWS . Amazon . com/VPC/latest/user guide/VPC-network-ACLs . htmlT3】](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html)

###### [https://d0 . AWS static . com/whites/AWS-security-best-practices . pdf](https://d0.awsstatic.com/whitepapers/aws-security-best-practices.pdf)

###### [https://www . cloud conformity . com/conformity-rules/VPC/network-ACL-deny-rules . htmlT3】](https://www.cloudconformity.com/conformity-rules/VPC/network-acl-deny-rules.html)

###### [https://www . cloud conformity . com/conformity-rules/VPC/网络-ACL-outbound-traffic-all-ports . html](https://www.cloudconformity.com/conformity-rules/VPC/network-acl-outbound-traffic-all-ports.html)

注:最初发布于 2018 年 9 月 20 日 [blog.totalcloud.io](https://blog.totalcloud.io/5-not-to-ignore-best-practices-for-aws-nacls-network-access-control-lists/) 。
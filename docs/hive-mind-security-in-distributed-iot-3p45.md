# 蜂群思维，分布式物联网中的安全

> 原文：<https://dev.to/terceranexus6/hive-mind-security-in-distributed-iot-3p45>

# 蜂巢思维，不，不是外星人，我们说的是硬件！

[![](../Images/0849a82b29d99198b8e44ec391f534c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ghwf-Sgu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252Fl3V0ma60jQqGCoJyM%252Fgiphy.gif%26f%3D1)

自从我的安全冲刺出版物([1](https://dev.to/terceranexus6/security-sprint-week-1---my-project-and-first-studies-1ko)[2](https://dev.to/terceranexus6/security-sprint-week-2---choosing-a-nice-point-to-sniff-and-using-tcpdump-for-packet-analysis--e9)[3](https://dev.to/terceranexus6/security-sprint-week-3---distributed-ledger-system-and-our-first-catch-the-flag-training-dpj)[4](https://dev.to/terceranexus6/security-sprint-week-4-ca-pairs-creation-with-openssl-eia)[5](https://dev.to/terceranexus6/security-sprint-week-5-network-capture-probe--a33)[6](https://dev.to/terceranexus6/security-sprint-week-6---perl-forensic-and-new-limits-2eci)[7](https://dev.to/terceranexus6/security-sprint-week-78-a-chat-with-a-security-expert-44l9)[8&9](https://dev.to/terceranexus6/security-sprint-week-9-privacy-politics-and-the-internet-o71)[10&11](https://dev.to/terceranexus6/security-sprint-week-10--11-bluetooth-hacking--4kh3)[extra](https://dev.to/terceranexus6/security-with-haskell-3cio))以来，我已经有很长时间没有休假了，以便对我一直从事的一些安全领域进行更深入的学习、工作和实践。我最后的努力是在分布式系统和如何在无处不在的网络中应用安全。我的目标是写一篇关于这个问题的更大的论文，但是现在我想分享一下围绕这个有趣话题的可能性。

## 分而治之！

**无处不在的计算**正带领我们走向用户和技术之间全新的关系。一步一步，我们忘记了我们周围的技术，专注于我们的目标，这可能意味着也忘记了风险。安全性是每个系统中的一个主要问题，物联网正在揭示一个新的风险领域，为此，我最近发现，受一篇论文[1]的启发，我正在研究在无处不在的网络中使用低成本硬件来应对和预防攻击。

使用分散模型的要点是既允许使用*低成本*硬件，因为安全性在模型中而不是设备中，又使攻击更加困难。没有一个中心点，使得每一个目标*都同样相关*，并且设计一个快速攻击更加困难。在该模型中，我们应该考虑相互通信的互连节点，并且交换的消息在添加到公共日志之前必须被签名。

使用发送和接收信息的传感器互连网络，共享公共内存日志中的状态内容，并随机检查邻居节点，将允许快速的攻击检测，即使考虑到假阳性。如果一个区域被认为是被*污染的*，其余的节点可以被禁止签署信息交换，以便**控制和窒息攻击**。

使用*进化计算*甚至有可能确定在被认为受污染的区域采取的步骤，并防止相同类型的第二次攻击。

这种预防和响应模式可能是虚假节点的受害者，这些节点与该区域未被污染的其他节点进行通信，并在网络中注入错误的内容。

## 准备好了吗？进攻！

假设我们有两种主要可能的攻击，被动和主动。被动攻击是由间谍节点执行的，它们在网络中捕捉相关信息，或者在任何情况下使通信变慢，但不会将错误的信息注入网络，这被认为是主动的。两者可以一起执行来污染整个网络，但是为了防止窒息或隔离，应该仔细构建工作。当心，劣质图纸来了！

[![](../Images/597100027578a0006efd48b6a2cc0a6b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_xkEvdhQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x753o0sngjtj85u31yln.jpg)

在图中，有一个由三个不同区域组成的网络，可以完美地作为房子里的传感器。所有节点具有彼此之间的“事务”的公共日志，该日志具有作为令牌的时间和内容信息。正常内容和响应时间的范围应该是预设的。在第一个事务中，T1 (A -> B)的目标是正常、健康的通信。来自 A1 的任何连接的节点可以随机地签署它，并且见证通信的状态。在 T2，不仅涉及更多的节点(A1 和 A2)，而且我想显示一个假阳性，这是考虑和警告，但不标记为主要污染。在 T3 中，一些新朋友受到牵连:*，♥(主动攻击者)和☾(被动攻击者)。☾(以及其他节点中其他可能的被动攻击者)的影响使得♥首先标记*和 f 之间被污染的通信。如果这次攻击成功，它将作为健康通信添加到日志中。哎呀！

即使通信是分散的，因此创建相关的攻击意味着应该协调时间和资源的组合，以便不警告健康的节点，这是非常困难的，因为事务是随机激活的。无论如何，不是不可能的。

我仍在调查和进行试验，为此我使用基本的开放硬件，如带有简单传感器的 arduino nano 设备，因此它们可以同时工作并检查它们的邻居。网络模拟也是一个非常便宜的选择，但是我能说什么呢，我爱硬件。

一旦我有了好的表现，我会在公开回购中发布一些代码，但我很乐意听到你对此的想法和意见！

*【1】:1。阿吉特·库玛尔·亚伯拉罕，拉斐尔·法尔肯，马里奥·科彭。:无线传感器网络的计算智能，978-3-319-47715-2。*
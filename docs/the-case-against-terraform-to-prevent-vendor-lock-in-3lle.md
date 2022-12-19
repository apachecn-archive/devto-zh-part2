# 反对 Terraform 以防止供应商锁定的案例。

> 原文：<https://dev.to/david_j_eddy/the-case-against-terraform-to-prevent-vendor-lock-in-3lle>

[![Terraform](../Images/9a5427e421a5b9916d7c4fc74533fb9c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t4I25vV3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidjeddy.com/wp-content/uploads/2018/07/hashicorp_terraform.png)

*Cross 来自我的博客[https://davidjeddy.com](https://davidjeddy.com/?p=836)。

免责声明:在我们深入讨论反对 Terraform 的理由之前，让我声明:我个人认为这是一个了不起的工具，它使各种规模的组织在当今世界的现代超高速市场中保持竞争力。也就是说，不考虑工具/过程/方法的缺点对每个人都是有害的。

来自 Hashicorp 的基础设施代码(IaC)产品 Terraform 自称拥有“跨提供商的安全工作流程”和“简单而强大”。围绕 Terraform 的社区甚至声称，Terraform 可以防止供应商锁定(即，为一项服务与单个提供商绑定)。在谷歌上搜索短语“Terraform 防止供应商锁定”，你会看到超过 64000 个结果。似乎是一个相当热门的话题！

[![Nonsensical Infographics provided by https://chadhagen.com/Nonsensical-Infographics](../Images/7c98cc6cd4e3c1e6fec308e64aa43e8a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W2iGQf6R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidjeddy.com/wp-content/uploads/2018/09/Nonsensical_2_bottom.png)

虽然我喜欢 Terraform，但用它来防止供应商锁定是扯淡；防止供应商锁定的信念是一个由非问题框定的自由的虚假承诺。你已经被锁定了:Windows，，Li/Unix；iOS，安卓；本地，云；。NET、Java、OSSAWS，GCP，Azure，OpenStack。一旦你选择了一个提供商，你就被锁定在这个提供商上，而没有努力去迁移到另一个提供商。

我是你的头...“但有了 Terraform，你只需更换供应商，就万事大吉了……”。真的吗？GCP 拥有具有相同网络和根卷吞吐量的 t2.micro 实例？Azure 和 AWS 联网一样？AWS 对长期存储的读取访问时间与 GCP 相同？不，不，他们没有。当前的云提供商虽然提供一些相同的基本服务级别，但没有提供足够的重叠，以至于在配置文件中翻转一个值就可以迁移环境。

云服务提供商的优势不在于一般产品，而在于小众产品。目标应该是以对您的组织提供的服务整体化的方式利用利基专业化。是的，你可以，也应该，在合适的时间和地点使用所有的提供者。将 GCP 用于人工智能，Azure 用于虚拟桌面，AWS 用于存储和 BI 数据分析。选择一家供应商并不意味着你只能选择他们。如果你的 IT 部门说不是这样，那么有人有其他的动机不接受“正确的工作使用正确的工具”的心态。

[![Nonsensical Infographics provided by https://chadhagen.com/Nonsensical-Infographics](../Images/a271aab6d24fc76348c408220c4f847b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VDUAdnEg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidjeddy.com/wp-content/uploads/2018/09/Nonsensical_1_top.png)

在这一点上，必须指出的是，使用多重云是复杂的。大量的网络、计算、需要注意的瓶颈、定价方案、恢复过程等，等等...我看不出这与软件业最近经历的从整体到微观服务的转变有什么不同。应用程序已经被分解成越来越小的部分。以至于三大提供商都提供 FaaS(功能即服务)产品。此时，每个功能基本上都指向一个 URI，因此托管在另一个云提供商上的 URI 对应用程序和最终用户是透明的。配置保存在 IaC 文件中，机密保存在机密管理器中，部署合并到您已有的 CI/CD 流程中。

总而言之；认为有一种神奇的解决方案可以把你从未来的自我中拯救出来的想法是错误的。对你未来自我/业务状况的担忧，在推动入职的过程中对你不利。不要再看那些天花乱坠的宣传，不要再看那些华而不实的营销策略，不要再看那些精彩的展示。供应商锁确实存在，并将永远存在。利用所有供应商的专业产品；即使这意味着错过今天的“酷新玩具”。我们都知道计算变化有多快。
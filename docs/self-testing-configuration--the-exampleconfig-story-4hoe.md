# 自测配置–example . config 案例

> 原文：<https://dev.to/craignicol/self-testing-configuration--the-exampleconfig-story-4hoe>

[![Some halloween cakes reflected in a mirror](img/6647cdc87ab3dea6a1add2497dab6b71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q2T_ADj9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://craignicol.files.wordpress.com/2017/11/dsc_0101.jpg%3Fw%3D320%26h%3D179)T3】镜像

我们都是优秀的开发人员，从不在源代码中存储生产秘密。不过，我们得把它们放在某个地方。

如果你是一个 ASP.Net 开发者，你[将你的秘密存储在一个单独的文件](https://www.michaelcrump.net/azure-tips-and-tricks113/)中，你如何在发布时检查它们？

在之前的工作中，我们在 AWS EC2 中构建一个应用程序，这意味着我们不能坐在可爱的 Azure Key Vault/secrets.json 工作流之上，而是使用一个安全文件 drop 作为部署的一部分。我们有几次发布失败了，因为本地可用的秘密没有包含在发布包中，导致网站无法启动。我们没有。Net 核心配置对象，所以我们所做的是在 example.config 文件中创建配置文档，其中包含我们期望的所有键，并填充了虚拟值。在启动时，我们将 example.config 中不包含任何秘密的密钥列表与实际的配置进行比较，并通过报告哪些密钥不同来使启动失败，这样我们就有了一种非常快速的方法来检查配置是否与预期同步。
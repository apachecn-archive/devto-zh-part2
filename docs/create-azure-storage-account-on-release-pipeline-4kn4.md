# 在发布管道上创建 Azure 存储帐户

> 原文：<https://dev.to/meanin/create-azure-storage-account-on-release-pipeline-4kn4>

# 简介

Azure 是一个越来越受欢迎的云平台。在 Azure DevOps 上配置 CI/CD 是一种非常好的体验。您可以创建一个构建管道，在那里您的软件将被构建并打包成一个安装产品。您还可以创建一个完整的发布管道。通常只将人工制品部署到某个空间。当我玩它的时候，我错过了一些为我准备一个全新环境的任务。

想象一下，您可以有多个环境，由大量服务组成，每个服务都连接到不同的数据库。您正在创建一个新的，并想检查所有其他的工作是否正常，新的没有破坏任何东西。我会创建一个全新的环境，部署基础设施，部署服务，运行单元测试、集成测试和回归测试，以确保所有组件都处于良好状态。

我在 Azure DevOps 平台上找不到的部分是让我能够安全地创建数据库和存储连接字符串的任务。稍后在部署时，将此连接字符串传递到服务配置中，而不是以纯文本形式传递给任何解决方案文件。这就是我开始开发 Azure DevOps 扩展的方式。

# 安装扩展

一个 [Azure 存储帐户](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview)包含所有的 Azure 存储数据对象:blobs、文件、队列、表和磁盘。表存储是 Azure cloud 上可用的基本 NoSQL 数据库。用于存储非关系数据的简单文档数据库。我现在将集中讨论这个问题。

若要安装此扩展，您需要 Azure DevOps 门户上的组织。你可以从这里的开始[。在此门户上，您必须拥有安装扩展的权限。然后在](https://azure.microsoft.com/en-us/services/devops/?nav=min)导航[，或者在如下的发布管道窗口中搜索`manage storage account release tools`:](https://marketplace.visualstudio.com/items?itemName=meanin.storage-account-managment) [![img](../Images/2331e778c822ca1975a5a253688e488d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FAK2BVNP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/meanin/dev-to-articles/master/img/2018-10-08-create-azure-storage-account-on-release-pipeline/add-tasks.png)

# 配置任务

[![img1](../Images/c90e010a2c9098231899b57d8df3117d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HFQNHPLw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/meanin/vsts-tasks/master/screenshots/createstorageaccount.png)

要使用此任务，您必须拥有一个配置了服务主体的[Azure 资源管理器连接。设置新的存储帐户名称(这是必需的，但如果存储帐户存在，它不会破坏任何东西)。为您的存储帐户设置位置，您可以将其保留为默认值，然后它将从资源组位置继承。最后需要做的是设置](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=vsts#sep-azure-rm) [Sku](https://docs.microsoft.com/en-us/rest/api/storagerp/skus/list) (性能层)。如果您愿意，可以通过设置名称在表存储中创建一个表。

# 局限性

这是扩展的第一个发布版本。目前，唯一受支持的存储版本是 Storage v1。我将感谢所有的反馈:)
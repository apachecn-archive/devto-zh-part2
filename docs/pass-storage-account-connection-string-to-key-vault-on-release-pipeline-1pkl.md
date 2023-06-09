# 将存储帐户连接字符串传递到发布管道上的密钥保管库

> 原文：<https://dev.to/meanin/pass-storage-account-connection-string-to-key-vault-on-release-pipeline-1pkl>

# 简介

我在这里写了几个关于一朵蔚蓝色的云的句子。大多数发布渠道都会以某种方式改变应用配置，以适应新的环境。对于数据库连接，通常，我们将连接字符串注入到上述配置中。你从哪里得到一个连接字符串？你说，从数据库里。但是，当在同一个发布管道上创建一个数据库时，如何实现这一点呢？

对我来说，这是一个缺失的部分，从 Azure DevOps 上可用的任务创建一个完全可操作的发布管道环境。当然有 [ARM 模板部署](https://docs.microsoft.com/en-us/azure/devops/pipelines/apps/cd/azure/deploy-provision-azure-vm?view=vsts)，但我不是它的大粉丝。

我决定创建我自己的任务，该任务将获取一个连接字符串，并将其传递给一个[密钥库](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-overview)——存储您的秘密、密钥和证书的安全存储库。

# 安装扩展

若要安装此扩展，您需要 Azure DevOps 门户上的组织。可以从这里开始[。在此门户上，您必须拥有安装扩展的权限。然后导航](https://azure.microsoft.com/en-us/services/devops/?nav=min)[到这里](https://marketplace.visualstudio.com/items?itemName=meanin.storage-account-managment)。

# 配置任务

[![img](img/b54981f2c120120466d1b0dd639a3104.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K70qsoKi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/meanin/vsts-tasks/master/screenshots/connectionstringtokeyvault.png)

要使用此任务，您必须拥有一个配置了服务主体的[Azure 资源管理器连接。设置要传递到密钥库中的连接字符串的存储帐户名。设置密钥存储库名称(如果不存在，将立即创建)。设置一个键名。如果您的密钥库尚不存在，您可以为它设置一个位置，或者它将从资源组(由服务主体限定范围)继承位置。瞧，任务执行完成后，您的密钥安全地存储在密钥库中。](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=vsts#sep-azure-rm)

这是该任务的第一个发布版本。我将感谢所有的反馈:)
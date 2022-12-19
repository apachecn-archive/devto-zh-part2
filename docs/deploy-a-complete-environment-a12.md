# 部署完整的环境

> 原文：<https://dev.to/meanin/deploy-a-complete-environment-a12>

# 简介

持续集成有助于团队在同一项目中并行工作。最常见的场景是使用一些 git 服务器，并将工作集成为从特性分支到开发分支的拉请求。许多工具提供了一种简单的方法来从合并的代码库构建一个产品(安装包)。连续部署是一种基于所需环境用最新代码维护应用程序状态的方法。

想象一下，在将新的变更推向生产之前，您想要在专用环境中进行所有的测试。或者，您可能希望在每个新创建的环境中播种一些数据？有时，在这种特定的环境中，存储/丢失的数据可能会引入不必要的错误或不必要的情况，需要您的注意，然后才能认为它已经过测试/可用。

# 想法

我想到了一个如何创建一个全新环境的想法，只是为了测试的目的。这样，您可以始终确保没有旧数据，只有系统正常工作所需的数据。没有什么会干扰测试用例，在新的开始上没有阻塞。

我正在使用微软 Azure Cloud，因为它是我最熟悉的。在最简单的情况下，我想创建一个存储(Azure Table Storage)，将它的连接字符串传递给 key vault(秘密的集中存储)，在那里播种数据，最后部署一个使用数据的应用程序。

# 解

### 1。创建存储帐户

有几种方法可以在 Microsoft Azure Cloud 上创建存储帐户。首先，有一个 Azure 门户——管理云资源的 web 界面。如果你更熟悉 devops 的工作，你会使用 [Azure Powershell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-6.12.0) (目前是跨平台的)或者 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) 。

在我的场景中，我希望将存储帐户创建流程与持续部署流程相集成。我可以编写一些 PowerShell 脚本，并将它作为一个步骤添加到我的 CD 定义中。我还可以为我的 CI/CD 工具编写一些自定义扩展，这将为我创建新的存储帐户提供一个很好的视图:)我正在使用 [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/) (它是 Visual Studio Team Services 的继任者)。在投入到 Azure DevOps 扩展开发的一些战斗之后，我最终得到了我以前的一篇帖子中描述的东西:

[![meanin image](../Images/eac22e42f116bab1ea6a5ece836378db.png)](/meanin) [## 在发布管道上创建 Azure 存储帐户

### (罗马尼亚语)

#devops #azure #microservices](/meanin/create-azure-storage-account-on-release-pipeline-4kn4)

看起来像下面。不错吧？您可以使用设计良好的用户界面来创建您需要的资源，而不是使用 PowerShell。
[![img](../Images/c90e010a2c9098231899b57d8df3117d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--HFQNHPLw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/meanin/vsts-tasks/master/screenshots/createstorageaccount.png)

### 2。将连接字符串传递给密钥库

在每个环境中使用不同的数据库/存储可能会很困难。一种方法是为每个环境使用一个专用的配置文件，但是凭证存储在版本控制系统中。第二种方法是配置一个专用的托管环境，在那里存储凭证，但是这给配置新环境带来了问题。在部署任何东西之前，需要知道所有的凭证。

幸运的是，还有另一种方法，这是我最喜欢的。在源代码管理之外存储所有必需的凭证，并在 CD 进程上获取实际值(覆盖配置文件)。这可以在专用的秘密集中存储中完成。微软 Azure 云提供了一个名为 [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-overview) 的组件。Azure DevOps 门户提供了一种从密钥库中获取数据的简单方法，你可以在[微软文档](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/variable-groups?view=vsts&tabs=yaml)上了解更多信息。

因此，我想到了一个主意，在 CD 过程中，就在创建完成之后，将我新创建的存储帐户的连接字符串推送到所选的密钥库。除了内嵌 PowerShell/CLI 脚本之外，我没有找到任何现有的方法来做到这一点。我决定为 Azure DevOps 创建另一个任务，也是在一篇专门的帖子中描述的:

[![meanin image](../Images/eac22e42f116bab1ea6a5ece836378db.png)](/meanin) [## 将存储帐户连接字符串传递到发布管道上的密钥保管库

### (罗伦斯保威十八章二分钟后开始读)

#devops #azure](/meanin/pass-storage-account-connection-string-to-key-vault-on-release-pipeline-1pkl)

和以前一样，我认为从事代码基础工作的开发人员不应该关心配置部署脚本。看起来像下面:
[![img](../Images/b54981f2c120120466d1b0dd639a3104.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K70qsoKi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/meanin/vsts-tasks/master/screenshots/connectionstringtokeyvault.png)

### 3。种子台存储

您是否曾经存储过数据，如果没有这些数据，哪种产品将无法在数据库端工作？数据不是永久的，在应用程序的生命周期中会发生变化，因此需要存储在 DB 中，但是没有它应用程序将无法启动。我想象一些用户配置值，由管理员处理的默认值，等等。在这种情况下，通常缺少的部分是种子功能。同样，我找不到任何开箱即用的东西。

我越是深入开发 Azure DevOps 扩展，就越能找到新任务的灵感。现在我创建了一个帮助将数据植入 Azure 表存储的工具。看我的帖子:

[![meanin image](../Images/eac22e42f116bab1ea6a5ece836378db.png)](/meanin) [## 种子台存储

### paweruciński 11 月 13 日 182 分钟阅读

#devops #azure #microservices](/meanin/seed-table-storage-10an)
It needs an input JSON file, with predefined two fields which are Azure Table Storage restriction - PartitionKey and RowKey. Any other field can names as needed by an application. The task configuration needs an Azure subscription, Storage Account Name, table name and of course path to seed file. In this case, it is a local JSON file.
![img](../Images/41e278f25657fa6de3c7394941726fd3.png)

### 4。应用程序部署

在 Azure 云中托管应用程序的默认方式是将其部署到应用程序服务。它支持多种语言，如 c#/。net，Java，PHP，静态网站(Angular/React/others)，甚至 node.js 或者 python。它能够使用 Windows 操作系统，Linux，或 docker 容器。查看微软文档以了解更多。配置非常简单。有几个字段需要您的注意，如 Azure 订阅、应用类型和应用服务名称。更多信息，请访问此[页面](https://docs.microsoft.com/en-us/azure/devops/pipelines/targets/webapp?toc=/azure/devops/deploy-azure/toc.json&bc=/azure/devops/deploy-azure/breadcrumb/toc.json&view=vsts)。

# 总结

经过一些努力，现在我能够通过 Azure DevOps 发布管道部署一个完整的环境。作为第一步，创建存储帐户/表。连接字符串作为机密传递给密钥库。下一个所需的表用种子数据填充。最后，使用从密钥库中获取的凭证部署应用程序，配置值被覆盖。从开发人员的角度来看，在这个特定的环境中，应用程序对哪个数据库、哪个表进行写/读是透明的。他们可以专注于交付功能，而不是纠结于部署过程、连接字符串、验证、凭证等。

最后，我准备了一个简短的 ASP.NET 核心演示项目，它从 Azure Table 存储表中读取所有数据，并将其呈现在网站上。在我的 github 上可以找到一个代码库:

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png) [意为](https://github.com/meanin)/[ASP-net-core-azure-storage-account](https://github.com/meanin/asp-net-core-azure-storage-account)

<article class="markdown-body entry-content container-lg" itemprop="text">

# ASP-net-core-azure-存储-帐户

</article>

[View on GitHub](https://github.com/meanin/asp-net-core-azure-storage-account)

As you can see in the config file, there is only connection string to a local emulator, instead of real Azure Table Storage instance.

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionString": "UseDevelopmentStorage=true",
  "DemoTableName": "MyTable"
} 
```

还包括一个 seed.json 文件，它可以从其他地方获取，但为了简单起见，它存储在一个代码库中。

```
[
  {
    "PartitionKey": "partitionKey1",
    "RowKey": "1",
    "OtherColumn": "value1",
    "FourthColumn": "123"
  }    ...
] 
```

在触发发布的几分钟后，我看到了这个全新的 web 应用程序，显示了一个源代码库一无所知的表格。瞧啊。
[![img](../Images/c51544456bed0f36bf95ddf4b7b21ca8.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--PiiA0fTT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/meanin/dev-to-articles/master/img/2018-11-21-deploy-complete-environment/deployed-application.png)

# P.S

不，不是 PowerShell)如果你有任何扩展 Azure DevOps 的需求，有一些闪亮的新部署任务，可以联系我:)
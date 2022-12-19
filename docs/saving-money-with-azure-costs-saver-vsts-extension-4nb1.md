# 使用 Azure Costs Saver VSTS 扩展节省资金

> 原文：<https://dev.to/akuryan/saving-money-with-azure-costs-saver-vsts-extension-4nb1>

自从我开始使用 Azure Web apps，我就开始思考:我们如何优化 PaaS 的预算支出？对于虚拟机来说，很明显，您需要停止它们。虚拟机 v1 需要一个额外的 PowerShell 命令来取消对它们的资源调配，以停止在它们身上花钱，而虚拟机 v2 在停止时会自动取消资源调配。但是对于 Azure SQL 数据库和 Azure Web 应用来说，事情就没那么简单了。

所以，我花了一些时间去调查。主要问题是，我不想做繁琐的设置，例如传递资源组名称、web 应用程序名称、虚拟机、SQL 数据库及其大小，同时跟踪所有更改。由于我们使用资源组在环境基础上对资源进行分组，我发现我可以从一个资源组中过滤所有资源，并找到那些可以缩减或取消配置的资源(web 应用程序和 SQL 数据库可以缩减，虚拟机可以取消配置)，而不会损害整体应用程序。例如，删除 Redis 缓存需要在重新创建时更新应用程序设置。我觉得使用简单的 PowerShell 脚本很难分享，而且是解决问题的简单方法。所以我决定为它建立一个 VSTS 扩展(teamcytime meta-runner 有一天也会跟进)。看看我的 [Azure Costs Saver 扩展](https://marketplace.visualstudio.com/items?itemName=anton-kuryan.AzureCostsSaver#overview)的结果。

## 工作原理

你可以在[我的 GitHub 账号](https://github.com/akuryan/vsts.extensions/blob/master/AzureCostsSaver/buildtask/budget-saver.ps1)找到扩展的核心。

*   将应用服务计划缩减到 S0 大小，只有一个跑步者。目前，它没有内置检查 web 应用程序是否有暂存槽，如果有暂存槽，您也不能降级到 B0。此外，B 层的磁盘空间大小限制为 10 Gb，而 S 层最多允许 50 Gb。在缩减之前，当前参数作为标记保存在资源上。
*   将 SQL 数据库缩减到 S0 大小，将 SQL 服务器上的当前参数保存为标记。
*   缩减弹性 SQL 池
*   取消调配虚拟机(单一虚拟机和 VMSS 虚拟机)。

向上扩展时:

*   对于 Web 应用程序:标签是从应用程序服务计划中读取的，它们被升级回标签中写的原始大小和工作人员数量。
*   对于 SQL 数据库:从 SQL 服务器读取标记，并将 SQL 数据库升级回所需的大小。
*   虚拟机再次启动(VMSS 的虚拟机也是如此)

## 这怎么可能成立

由于手动运行某些东西不是我希望做的事情，更不用说连续几次了，我决定将它设置为 VSTS 版本；不过，这个扩展也可以用于构建。

我的版本是在一个资源组中放大或缩小资源，对应于两个环境:T(测试)和 A(验收)。该版本计划在周一至周五每天 13:00 自动创建:

[![Setting up at Azure DevOps](../Images/5dbfc7f561cb2323585e2e2df7609bd7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c15HVo-X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dobryak.org/wp-content/uploads/2018/11/Azure-Costs-Saver1-600x235.png)

该版本由两个环境组成:一个是缩小版，称为缩小版，从周一到周五的 22:00 在版本创建后执行一次；另一个称为恢复，在周一到周五的 06:00 发布创建后执行一次。这种设置确保了在晚上和周末我们的资源处于最小规模，而在白天它们以最大功率执行。

[![Setting up at Azure DevOps](../Images/3feaf467d7bc60e13d03c21844bd153e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HILV2RPJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dobryak.org/wp-content/uploads/2018/11/Azure-Costs-Saver2-600x275.png)

每个 VSTS 环境由 1 个阶段和 2 个任务组成，以扩大或缩小 T 和 A 资源组的资源；每个任务都是 Azure 成本节约任务。

[![Setting up at Azure DevOps](../Images/5258292c71a688ea07aad614b973c07c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T4wWiY9P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dobryak.org/wp-content/uploads/2018/11/Azure-Costs-Saver3-600x187.png)

任务中的字段及其含义:

*   显示名称:为了方便起见，任务的名称
*   Azure 连接类型:现在设置为 Azure 资源管理器。
*   Azure RM 订阅:一个下拉列表，其中包含可供您访问或通过 VSTS 端点配置的订阅
*   缩减资源？:带有“是”和“否”选项的下拉列表。如果选择“是”，资源将被缩减，当前大小(缩减前)将被写入标记；如果选择“否”，资源将升级到存储在标记中的大小。
*   资源组名:适用资源所在的资源组的名称。

## 结论

使用我的 Azure Costs Saver VSTS 扩展，您可以轻松地在您的 Azure 订阅中为非生产环境节省一些成本，只在需要时使用全部性能-完全自动化。
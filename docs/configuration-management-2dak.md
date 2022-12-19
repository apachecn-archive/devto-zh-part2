# 结构管理

> 原文：<https://dev.to/techcommunity/configuration-management-2dak>

# 使用 OpenCM 管理您的数字业务平台

| 2018 年第四期 | [![](../Images/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://techcommunity.softwareag.com/ecosystem/download/techniques/2018-issue4/SAG_DBP_Configuration_TECHniques_Oct18_WEB.pdf) |
| --- | --- |

OpenCM 是一个开源的配置管理治理工具，强调控制不同 webMethods 安装的正确配置设置的能力。单个服务器上的常规 webMethods 安装包含 500-1，000 个配置属性，因此，当安装数量增加时，无法手动记录，也无法审核。OpenCM 现在是一个开源工具，可供任何希望提高洞察力和控制水平的客户使用，以降低在无效上下文中执行 QA 验证和生产运行时的风险。

配置属性自动基线化的起点是提取所有可能的配置信息(例如，集成服务器(is)包版本、JVM 内存设置、Terracotta fix 版本、通用消息传递领域设置、JDBC 连接属性等)。).提取的信息存储在 OpenCM(离线)中，然后可以作为属性值应该是什么的“真实来源”。它还是一个运行时属性存储库，存储特定时刻属性值的实际信息(运行时快照)。有了这些现成的信息，就有可能执行各种 CM 治理功能:

*   断言配置设置是它们应该是的(在“真实的来源”基线配置和当前运行时之间)
*   断言不同安装之间的配置设置是相同的(例如，两个群集节点之间、测试和生产之间等)。)
*   了解与默认产品设置(首次安装时的设置)相比，特定安装中发生了哪些变化

许多组织已经为不同的环境(比如测试与生产)分离和隔离了运行时域，但是 OpenCM 允许一个统一的视图，从而支持跨这些域的 CM 治理。

## 持续审计

OpenCM 提供了自动化提取、比较和生成审计环境所需的报告的完整过程的能力。它使用软件 AG 平台管理器作为检索运行时信息的主要工具。

[![](../Images/c4863de9157579ec21788ddd9afb359a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aTfRycFI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/ConfManagement_1.jpg/bb8a281d-161a-4f55-abe4-67ba610cbed6%3Ft%3D1539784084716)

**图 1:** 持续审计:提取、比较和报告

## 可视化

OpenCM 还附带了一个用户界面，允许在您的数字业务平台基础架构环境中轻松导航，并提供轻松深入详细配置项目值的能力。例如，只需点击几下鼠标，就可以迅速看到生产中的当前修复级别。

[![](../Images/9ddf74bf44590c9ff3b436251f3f0ee5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xtxg8zjF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/ConfManagement_2.jpg/18059a2f-a52a-453d-8a2b-4a628d7ae988%3Ft%3D1539784249164)T3】图 2: OpenCM 用户界面:可视化安装属性

## 审计差异和报告

执行断言时生成的比较报告之一是一个 Excel 电子表格，它显示了跨多个环境的每个安装的值。当一行显示为红色时，这意味着特定属性的一个值(或多个值)不同，而实际上它们应该是相同的。

[![](../Images/8d9f8d4414a6884453cd5b289710bd4d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jg4hKNEW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/ConfManagement_3.jpg/cad957f7-3fb5-4753-b115-834360a3c3dc%3Ft%3D1539784354926) 

**图 3:** 汇报:突出差异

我们建议通读《OpenCM 用户指南》,因为该工具基于更广泛的配置管理策略，并且其当前的功能满足了一个成熟的 CM 存储库的所有需求的一个子集，尤其是在变更管理方面。

然而，提取信息、执行配置设置的自动基线(与手动填写报告相反)以及执行连续和自动审计的能力被认为是已经存在的 CM 基础。

## 未来能力

关于如何改进 OpenCM 使其成为更有用的工具，有很多想法，我们也欢迎所有的想法。以下是一些精选的例子:

*   推断不同已安装组件之间的通信契约，即，然后应该能够检测跨环境配置设置(可能已被错误配置)，例如，到“测试”通用消息传递提供者的“开发”集成服务器 JNDI 连接。
*   配置属性馈送到其他自动化工具，例如，使用 Command Central(用于供应、配置更改或修复安装)，从 OpenCM 检索必要的配置信息，而不是将它们冗余地存储在外部脚本和属性文件中。
*   能够从 OpenCM(通过脚本/UI)驱动配置更改，即任何配置更改的起点都是从真实源驱动到目标运行时安装。这意味着 OpenCM 将成为整个变革过程的一部分。这也可能包括引入可追溯性信息(例如，知道谁在什么时候改变了什么)。
*   将现有安装镜像到新安装的能力，例如，以最少的工作量(按需)设置类似生产的安装以进行维护的能力。
*   生成各种报告:例如，生成设置具有源:端口和目标:端口组合的新环境所需的防火墙规则列表的能力；负载平衡器配置也是如此。

可以在软件公司 GitHub 找到 OpenCM 工具和文档，网址:[https://github.com/SoftwareAG/OpenCM](https://github.com/SoftwareAG/OpenCM)

## 结论

数字业务平台的规模越大，错误配置的风险就越大。组织越大，维护配置属性的离线文档版本的工作量就越大。这进而导致离线信息过时、不正确、重复和缺乏覆盖的可能性更高。OpenCM 应该有助于缓解这些问题，从而提高数字商业平台运行时的整体质量。
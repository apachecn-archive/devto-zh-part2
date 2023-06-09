# 云部署

> 原文：<https://dev.to/techcommunity/cloud-deployment-1ajp>

# 面向专业开发者的 iPaaS

使用基于网络的可视化拖放工具，在云中开发新的集成非常容易。但是这些并不总是提供专业开发人员所需的完整的集成、调试和测试能力。webMethods Integration Cloud 提供了使用 Designer(Software AG 的全功能专业开发环境)来开发和部署集成到 Software AG 的 webMethods Integration Cloud 中的能力。这种模式通常被称为“提升&转移”,因为它使用户能够轻松地提升现有的集成，并将它们转移到云中。从预定义的云解决方案环境中进行选择。让 Software AG 处理环境配置、部署集成、管理修复、补丁和升级，并在云中提供 CI/CD 支持。然后探索现成的监控仪表板，以跟踪解决方案运行状况和流服务行为以及警报。专业开发人员现在有了一条通向多功能、多云平台 iPaaS 的直接途径。

| 2018 年第四期 | [![](img/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://techcommunity.softwareag.com/ecosystem/download/techniques/2018-issue4/SAG_Cloud_Deployment_TECHniques_Oct18_WEB.pdf) |
| --- | --- |

## 拔河:有云还是无云？

几乎所有组织都在调查或已经利用云来卸载 IT 系统。然而，将集成部署到云可能会很复杂。与云平台提供商合作、安装和维护软件版本、根据需求进行扩展以及保持应用程序最新不仅耗时，而且容易出错。

在云中开发新的集成可能更容易，但不能提供像 Software AG Designer 这样丰富的工具来创建、编排、测试和调试复杂的业务逻辑。使用 webMethods Integration Server 创建高性能复杂应用程序的专业开发人员需要基于 EclipseTM 的设计工具提供的高级功能。这些开发人员中有许多更喜欢在内部部署，这样他们就可以使用满足他们需求的开发工具。

更大的挑战是，大多数组织已经部署了大量现有的集成。如果新项目部署在云中，而旧的集成继续驻留在内部，那么它必须以两种不同的模式工作。

理想的解决方案是拥有一个专业的开发工具，可以将您现有的集成“提升并转移”到云中，并在云中创建新的集成，而不会给 IT 组织带来额外的负担。

为了满足这一需求，Software AG 创建了云部署功能，允许集成开发人员:

在 Designer 中开发新的集成、API 和业务逻辑。

融合了集成服务器及其内置服务以及数据和云连接器的能力。然后通过选择预定义的部署环境部署到集成云。该环境是自动供应的，您的集成、API 和业务逻辑也随之部署。

**将现有的内部集成和 API 从 Designer 部署到云。**

选择您想要的服务和套餐，按需将其部署到基于云的环境中。一旦它们被部署到云中，Software AG 将处理从自动升级到修复/补丁管理和 CI/CD 支持的所有事情。基于云的监控仪表板使用户能够跟踪解决方案运行状况和流服务行为。

**集成内部资源**

这是通过使用 VPN 隧道来安全传输您的敏感数据来实现的。能够访问不迁移到云的核心业务数据至关重要。用户可以设置一个 VPN，用于与定义的端点进行内部数据连接。一旦您在选定的环境中部署了您的集成，请对其进行配置以访问您提供的端点。

**管理云中的应用程序准备和迁移**

云中部署的环境自动支持从一个阶段迁移到另一个阶段。用户选择目标阶段，配置环境，然后只需将资产从上一阶段拉到当前阶段，并为密码和端点等配置资产设置适当的值。更新可以很容易地部署到相同的环境中。

webMethods Integration Cloud 使专业集成和 API 开发人员能够轻松地创建、发布和部署他们的解决方案到一个完全托管的、可扩展的云环境中。

## 在云中部署集成的五个步骤

**1。许可证:**您必须拥有集成云中的租户和云部署许可证。到内部数据的 VPN 连接可能需要额外的权限。

**2。Designer setup:** 确保下载新版本的 Designer，以获得可用于云部署的新特性。从设计器中，打开首选项并选择软件 AG- >集成云。在这里，您将为您的集成云租户输入连接 URL 和身份验证详细信息。每个都可以有一个唯一的名称。

**3。服务和目的地:**从 Package Navigator 中，上下文菜单现在会有一个部署到云的选项。这带来了一个向导，用于将资产发布(例如，提升&转移)到云中。选择资产后，您可以为目标选择预配置的集成云租户连接。

[![](img/ce401c8310b2e9c225439b0523ff6577.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xJxrkOfA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/CloudDepl_1.jpg/c0763cd0-e1bd-4416-beef-2efee6dd2cad%3Ft%3D1539856149067) 

**图 1:** 正在部署服务

**4。Landscape:** 接下来，您从一组 Landscape 部署模式中进行选择。这些模式从简单部署的最小配置到生产就绪配置，包括集群集成服务器、通用消息传递和 Terracotta 内存数据管理。

[![](img/d45e16379588ec45a9030ecb2d82e15e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tZxFu8k6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/CloudDepl_2.jpg/593d3501-2ba9-414c-bcaa-9cff9f34bef7%3Ft%3D1539856382632) 

**图二:**选择风景

**5。Scale:** 决定您需要多少集成服务器、通用消息服务器和 Terracotta 服务器，并指定解决方案的 CPU 和内存特性。

使用基于 Kubernetes /Docker 的管理层，可以自动部署和管理选定的解决方案环境，并将您的服务和包部署到该环境中。现在，您可以使用 Integration Cloud developer UI 在 Blockly 界面中浏览您的资产。通过内置的基础设施和流量监控和警报来维护您的解决方案的健康。修复和升级作为托管服务的一部分自动处理，具有高可用性和 CI/CD 支持。当然，集成云使您能够随着性能需求的增加进行纵向和横向扩展。

## 监控:我的解决方案发生了什么情况？

为了了解您的解决方案发生了什么，监控组成它们的服务和集成以及可能影响它们性能的底层基础设施是很有用的。集成云现在提供现成的

预定义监控仪表板。在最高级别，有一个仪表板显示该集成云上托管的所有解决方案的整体 KPI 状态和警报。

[![](img/90cb0f6031d35238465377b787befb43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WBJ6J3mM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/CloudDepl_3.jpg/701d6f80-5c97-452b-a458-d2e2b2260c12%3Ft%3D1539856717121) 

**图 3:** 监控仪表盘

还为每个运行时(Integration Server、Universal Messaging 和 Terracotta)预定义了基于 KPI 的仪表板、一个警报仪表板和一个用于监控流的仪表板。Prometheus 中收集的 KPI 也可以通过 REST 端点访问，以便用户在需要时进行额外的分析。

## 结论

对于那些希望轻松地将现有集成重新托管或“提升和转移”到云的现有集成服务器开发人员来说，集成云是正确的选择，因为他们不需要设置服务器、安装软件、导出和导入他们的包以及管理云容器。它易于扩展，并且您可以使用内置的基于 KPI 的仪表板来监控您的基础架构和集成。

对于希望保留现有集成并使用 Designer 的全部功能在云中构建新集成的开发人员来说，这也是一个很好的选择。它已经链接到您的内部测试工具，您可以在本地或云中部署的同时，继续在相同的开发环境中进行设计、编排和调试。

有了 Software AG 来管理云部署和运营，专业开发人员可以专注于他们最擅长的事情:构建解决方案来支持业务。
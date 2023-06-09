# 自然

> 原文：<https://dev.to/techcommunity/naturalone-3hfl>

# 做好自然 9 的准备

**让您的自然应用开发环境为 2050 年及以后做好准备！从现在开始，NaturalONE 是 Natural 9.0 版本的默认开发环境。**

| 2018 年第四期 | [![](img/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://techcommunity.softwareag.com/ecosystem/download/techniques/2018-issue4/SAG_NaturalONE_TECHniques_Oct18_WEB.pdf) |
| --- | --- |

## 2050 年及以后

Natural 9 是一个重要的版本，对我们所有的客户都有非常重要的意义。这个新版本不仅提供了许多有价值的新特性，而且是一个重大转变，改变了现在和将来用 Natural 开发和维护应用程序的模式。从 Natural 9 开始，NaturalONE 将成为 Natural 的默认开发环境。

**会有什么变化？**

在 Natural 9 中对于大型机、UNIX 和 Linux 来说，编辑器默认是禁用的。这包括程序编辑器、数据编辑器和地图编辑器。客户将免费获得 NaturalONE 和 Natural Development Server，而不是编辑器。Natural for Windows 不受此更改的影响。

这对你的实践意味着什么？

如果您从 Software AG 物流服务中心订购 Natural 9，您将收到一个新的 Natural 许可证密钥。使用此许可证密钥，您可以激活 NaturalONE 和 Natural Development Server 的免费版本，这些版本具有与商业版本相同的功能。

安装后，您可以使用“Tech”命令来检查自然编辑器设置的状态，如图 1 所示。Tech 命令中的编辑器设置应该显示“禁用”如果您随后调用编辑器，您将收到自然系统消息 NAT7743“编辑器在自然模式下被禁用。请改用 NaturalONE。”当您调用编辑器(如 List 命令)时，此消息也会出现在其他情况下。

[![](img/53a4a5b9dfb3df30652a770b469ddf72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h8tONro0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/natural1.jpg/697f78de-1ac1-4a25-8426-75f8e610aa16%3Ft%3D1540196728427) 

**图 1:** 使用 Tech 命令验证自然编辑器被禁用。

请注意，在使用编辑器 API 的程序中，不会禁用“Edt”和“Adhoc”命令。附加产品，如自然构造和自然 ISPF 也将继续像以前一样工作。

如果您试图将免费的 NaturalONE 版本与编辑器仍处于启用状态的自然版本一起使用，您将会收到自然系统消息 NAT7744“免费的 NaturalONE 只能在编辑器被禁用的情况下使用。”如果您购买了 NaturalONE，您将不会收到自然系统消息 NAT7744。

## 与自然一起发展——新方式

**入门**

到目前为止，您可能在库中管理您的自然源代码，并将源代码保存在 Fuser 文件中。对于 Natural 9 和 NaturalONE，您需要停止使用 Fuser 来管理源代码，而开始使用与 NaturalONE 集成的存储库。我们建议您使用像 Git 或 Subversion 这样的开源存储库，但是也可以使用来自其他供应商的存储库，比如 Microsoft Team Foundation Server。您仍然可以使用 Predict Application Control (PAC)或 Treehouse N2O 等产品来控制从测试环境到生产的各个阶段。

在您选择了一个兼容的存储库之后，您的新的和改进的开发之旅就可以开始了。只需将您的应用程序(包括 step 库)逐步添加到 NaturalONE 工作区中的 NaturalONE 项目中，并将它们提交到存储库。从这一刻起，唯一的“真理之源”是存储库，而不是融合器文件。所有现有的自然源代码现在都在存储库的控制之下，包括所有自然对象类型，如程序、地图和数据区域。对现有自然源代码和用 NaturalONE 编辑器创建的新自然对象的所有修改都将提交到存储库中。您不再需要将源代码保存在 Fuser 文件中。

**基于存储库的团队开发**

使用 NaturalONE 的诸多优势之一就是改进团队协作。开发人员可以简单地从存储库中“签出”自然源代码的最新版本的副本。如果几个开发人员在同一个源代码上工作，不管是偶然的还是有意的，当代码在“签入”后被合并时，对代码的所有更改都可以被保留开发人员还可以创建一个新的分支，他可以在这个分支中独立于其他开发人员工作，然后在他的特性工作完成后，将其与主分支合并。

与 Fuser 方法相比，维护代码变更的历史是使用存储库的另一个优点。源代码的所有版本都保存在存储库中，可以重新激活旧的源代码，并比较不同的版本，看看真正发生了什么变化。

基于存储库的团队开发比传统的开发方法有很多优势，如图 2 所示。

[![](img/cfdf94837c89b65acf82937b007e70b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fa4B0FtT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/natural2.jpg/86ac01a4-34f1-4910-aaef-3b6a7be9ac95%3Ft%3D1540196732023) 

**图 2:** 使用 NaturalONE，您将收获基于库的团队开发的好处。

**部署**

当一个或多个特性准备好进行集成测试或生产时，您可以从存储库中将最新版本部署到大型机、UNIX、Linux 或 Windows 上的目标自然环境中。这里的部署意味着自上一次部署以来的所有自然源代码更改都将被自动识别并复制到目标环境中的一个或多个库中。这时，福瑟回来了。自然源代码被临时保存在 Fuser 文件中并被编目。编目过程完成后，源代码将从 Fuser 中删除。这避免了 Fuser 中的源代码与存储库中的源代码之间可能存在的不一致。

您可以继续使用现有的工具，如 Predict Application Control、N2O 或 Sysmain 和 Sysobjh 等工具，将自然对象代码复制到集成测试或生产中。

我们强烈建议您遵循 DevOps 方法来优化这里描述的流程。这将使您能够在不牺牲质量的情况下，更快地向最终用户提供已实现的功能。DevOps 方法涵盖了从开发到运营以及从持续集成到部署的所有阶段。

## 让 DevOps 回归自然，管理代际变化

通过将编辑工作转移到 NaturalONE，您现在将受益于为 Natural 使用一个支持敏捷开发和 DevOps 的基于 Eclipse 的现代开发环境。当您采用 DevOps 方法时，您可以开发新的应用程序并更快地更新现有的自然应用程序，以更好地满足不断变化的业务需求并降低应用程序开发成本。要了解更多关于将 DevOps 引入自然的好处，请阅读[www.b2b.com/naturalone-bringing-devops-to-natural](http://www.b2b.com/naturalone-bringing-devops-to-natural)

提供像 NaturalONE 这样的一流开发平台还可以让您更轻松地招募新人才和管理员工队伍的代际变化。Adabas & Natural 的几个客户已经想出了如何使用 NaturalONE 管理他们团队中人才的代际变化。它们吸引并留住年轻的开发人员，并吸引所有代人一起工作，分享技能和知识，并进行创新。以下是他们成功管理员工代际变化的秘诀:[https://resources . software ag . com/adabas-natural/managing-generation-change](https://resources.softwareag.com/adabas-natural/managing-generational-change)

## 成功案例

一家欧洲保险公司通过强大的培训计划和 NaturalONE 的使用吸引并留住了年轻的开发人员——34 岁左右，其中一半是 20 多岁的年轻人。一个 17 岁的实习生只用了五天就学会了自然。了解一下这家保险公司是如何吸引和留住年轻开发者的这个视频:[https://resources . software ag . com/YouTube-product-videos-for-Uber flip/see-how-insurance-company-alte-oldenburger-masters-generation-change](https://resources.softwareag.com/youtube-product-videos-for-uberflip/see-how-insurance-company-alte-oldenburger-masters-generational-change)

## 总结

我们的许多客户在使用 NaturalONE 进行基于存储库的开发时已经获得了立竿见影的好处。我们相信你也会受益匪浅。迁移到 NaturalONE 可以让您利用众多 Eclipse 可用的免费开源插件，这对下一代开发人员非常有吸引力，这使得雇佣新员工和激励年轻开发人员使用 Natural 变得更加容易。

Java 程序员会发现 NaturalONE 因其熟悉的图形用户界面(GUI)和向导而极具吸引力。NaturalONE 还让您能够协调整个组织的应用程序开发，无论您是用 Natural、COBOL 还是 Java 开发应用程序，以便在大型机或 LUW 上本地运行。

通过将 NaturalONE 作为默认的自然开发环境来摆脱绿屏开发环境是实现 Adabas & Natural 2050+战略的重要基石。通过在敏捷、数字化就绪的架构中重新利用您对独特 Adabas & Natural 应用程序逻辑和高价值数据的投资，您可以快速转型为数字化软件驱动的业务，并将中断降至最低。
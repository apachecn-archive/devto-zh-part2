# 开发和软件股份公司

> 原文：<https://dev.to/techcommunity/devops-and-software-ag-2jhf>

# 文化如何应用于数字商业平台

**本文简要概述了什么是 DevOps 以及它如何应用于 Software AG 产品。**

| 2018 年第四期 | [![](img/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://techcommunity.softwareag.com/ecosystem/download/techniques/2018-issue4/SAG_Devops_and_SoftwareAG_TECHniques_Oct18_WEB.pdf) |
| --- | --- |

## devo PS——流行语背后是什么？

DevOps 是最近的流行语。它描述了一种统一软件开发(Dev)和软件运营(Ops)的文化。通过这种方式，它推广了广泛的已知和使用的技术，如自动化测试、自动化发布管理、基础设施管理和监控。所有这些结合起来的目标是缩短开发周期，并将高质量的代码频繁地推向生产系统。

[![](img/7a988ed4c283a3b2771b08e8fb376e1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--seIz7_q---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/Devops_1.jpg/ed1c666c-05bb-45cc-87e2-b49a4a84999d%3Ft%3D1539611152002) 

**图 1:** Dev + Ops = DevOps

如上所述，这些技术并不新鲜。自动化测试、持续集成、自动化基础设施建立和发布管理已经被许多公司使用了几十年，并由经验丰富的开发人员推广。这些也是精益和敏捷原则的重要组成部分，这些原则现在是开发组织事实上的标准。

那么，为什么 DevOps 最近成为热门话题呢？如果同样的敏捷原则被应用到商业策略中，想法可以很快被真实的客户验证，那么你的企业成功的机会就会大大增加。这种“快速尝试，快速失败”的心态使用软件作为载体，在 Eric Ries 的畅销书《精益创业》中有很好的描述。现在，它被许多公司使用，成为我们日常生活的一部分:早在 2009 年，Flickr 就实现了一种文化，允许每天进行 10 次以上的部署。网飞每天向其生产系统发布超过 50 次，而优步在不影响业务的情况下迅速发布了微部署。这种业务敏捷性允许公司以最小的投资更快地创新和验证许多新特性(A/B 测试)。

这些企业都是基于网络的，拥有大量客户，这也不足为奇。我们生活在一个云计算已经成为看待计算能力的新方式的时代，这意味着基础设施可以以一种完全不同的方式来看待——不是静态的，而是一次性的，仅按需需要的，是一种资产。为了让事情更清楚一点，我将使用宠物和牛的类比。

本地服务器可以被视为宠物:它们是独立的物理机器，有一个管理员团队负责管理，因此它们有电源，温度也合适。每台服务器都必须在其自己的维护窗口中安装最新的补丁。如果软件解决方案需要更大的可扩展性，则必须购买、设置、安装新的服务器，然后将其包含在解决方案中。

云计算和容器化让我们可以把服务器看成一群牛。每个服务器都是一个类型的实例。如果进行了更改，它可以无缝地应用于整个服务器集合，也可以只应用于其中的一部分。如果一个服务器坏了，可以用另一个实例替换它。如果需要可伸缩性，需要一个新的服务器实例，它会自动加入其他实例。所有这些活动对管理员来说都是自动化的，因此管理员可以专注于对业务来说最重要的事情—拥有一个可扩展的工作解决方案。这非常符合 DevOps 的文化。

[![](img/65430027f12e9f3c499d2d3c1effbdcb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i708duiN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/Devops_2.jpg/6f5aebfc-b8ef-493d-bc13-bc5434f20737%3Ft%3D1539611556663)

图 2: 宠物对牛

简而言之，DevOps 是围绕软件开发和操作的文化和原则，它应该能够以非常快的速度将想法推向市场，而不会损害质量或干扰业务。

## DevOps 和软件 AG 产品

仔细观察，DevOps 可以大致分为两个方面:快速、高质量地交付解决方案的能力，以及轻松管理基础设施的能力。

[![](img/caf0552f4cd321303325b05393bf49cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BL3qQDKz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/Devops_3.jpg/6d240244-3df3-4591-a8f3-a43d75d1cd9f%3Ft%3D1539611699271)

**图 3:**devo PS 的两种成分

持续集成/持续交付(CI/CD)原则涵盖了快速发布/交付部分，而基础设施管理可以由 PaaS 功能(如 webMethods 集成云)、托管 Software AG 产品的 Docker 映像或由 Software AG Command Central 管理的内部解决方案来处理。

## CI/CD

**Docker**

使用 Docker 时，交付是围绕基础设施进行的。这些解决方案正在被构建到容器中，然后被一个阶段一个阶段地测试和推广。Docker 映像本质上是不可变的，这确保了无论测试什么，都可以不加修改地提升到生产环境中。环境特定的配置是通过环境变量获得的。这种方法是 Docker 和 Software AG 的标准，不会偏离它。

[![](img/4cf0ac6f9f778c158918d0f99758a9c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--axjaQPF---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/Devops_4.jpg/3492154b-2dbd-4d80-9b95-8d8892d3cf15%3Ft%3D1539612306503) 

**图 4:** 完成 Docker CI/CD 流程

整个数字业务平台必须取得更多进展才能在 Docker 上可用，但 Software AG 已经实现了第一个里程碑，在 Docker Store 上发布了名为“微服务容器”、“阿帕玛”、“API 网关”、“API 门户”和“TerracottaDB”的“集成服务器”容器作为官方映像。

有关官方软件 AG Docker 图像的更多信息，请访问 [Docker 商店](https://store.docker.com/search?q=softwareag&type=image&source=verified)。

**内部**

内部 CI/CD 已被样品和物品覆盖。在[与 webMethods 9.x](http://techcommunity.softwareag.com/web/guest/techniques-blog/-/blogs/continuous-integration?mkt_tok=eyJpIjoiTkRsaVlUQTVOMlV6Tm1VNSIsInQiOiJzSFk2RTloVEtkNEFDRmFYVjlLRzVVeTNDN0pnQXduY3JqdENzMkxpRzh6bDg2aytDUVd6UUpRa2VvRno3MDdUNTFoVTJicFNpUDJ5KzhPLy9ES2UvSTdZaUEyb2Q5K2hSUzhFdnRMeE9mWXY1NVJDT1Mxck1wb0g3ZktPZlRiYyJ9) 的持续集成中可以找到更深入的探讨。

## 基础设施管理

**Docker/cloud**

容器和云解决方案是管理大规模基础设施的自然方式。升级、修补、监控和横向扩展等功能直接来自容器编排(如 Docker Swarm、Kubernetes)或所选的云系统。

**内部部署**

维护内部部署基础设施的标准方式就像一只宠物:你必须密切监控它，定期应用修复程序，进行备份和所有其他种类的操作活动。这个

通常需要系统管理员的全力关注。

近年来，有很多工具旨在实现运营方面的自动化，如 Chef 和 Puppet。软件公司提出了自己的大规模基础设施管理工具:Command Central。它支持:

*   大规模安装
*   修复安装/修复卷展栏之间的对齐
*   质量配置
*   整个环境的管理和生命周期管理
*   通过 CLI、REST API 和声明性描述(作为代码的基础设施)实现上述所有自动化
*   从 9.8+版到 9.12+版的脚本迁移

可以从中央 UI 控制台执行安装和维护。这些活动也可以编写脚本并集中执行。Command Central 支持的迁移路径使客户能够自动升级到最新版本。当您的足迹超过 5 到 10 个产品实例时，这种在自动化方面的投资对于安装和迁移都是有意义的。虽然 Command Central 不会像对待牲口一样对待您的内部基础设施，但它肯定会降低 Software AG 数字业务平台的总拥有成本，并使您的业务更加敏捷。

## 结论

DevOps 是一个非常广泛的话题，与其说是一种技术，不如说是一种文化。拥有它是昂贵的，并且不容易维护，你需要熟练的人来执行它，但是如果应用它，它可以以看不见的水平加速你的创新步伐。

这篇文章旨在提供 DevOps 的总体概述，以及如何将这种文化应用到最常见的软件 AG 用例中。

要了解更多 DevOps 和其他相关主题，请访问我们的社区:[http://techcommunity.softwareag.com/](http://techcommunity.softwareag.com/)和我们在[http://github.com/softwareag](https://github.com/softwareag)的官方 GitHub 组织
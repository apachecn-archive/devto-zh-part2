# Terracotta DB 的集成服务器适配器

> 原文：<https://dev.to/techcommunity/integration-server-adapter-for-terracotta-db-32ak>

# 应用程序的内存数据存储

【2017 年 10 月，Software AG 推出了新的大数据产品 Terracotta DB。Terracotta DB 是一个用于操作和分析工作的统一数据平台。它有一个改进的 Ehcache API 和一个全新的用于内存存储和分析的商店 API。Terracotta DB 将 NoSQL 数据存储的强大和便利与 Software AG 速度最快的内存数据网格技术的性能结合在一起。与此同时，webMethods 适配器团队为 Terracotta DB 的 Store API 发布了一个新的适配器，提供了从 webMethods Integration Server (IS)读取、写入和查询 Terracotta DB 的简单拖放、结构化访问。凭借灵活的数据模型、内存索引和专门构建的快速可重启性，对 Terracotta DB 内存存储的访问是对 is 开发人员工具箱的一个强大补充。

| 2018 年第二期 | [![](img/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://techcommunity.softwareag.com/ecosystem/download/techniques/2018-issue2/SAG_Integration_Server_Adapter_TECHniques_Apr18_WEB.pdf) |
| --- | --- |

## Terracotta DB 适配器:通向 webMethods 世界的桥梁

webMethods 开发人员习惯于使用流逻辑快速开发集成应用程序。加速开发过程的主要优势包括使用简单的拖放方法、可视化映射、通过结构化 IS 文档类型的轻松集成和管理连接。Terracotta 传统上公开 Java APIs，并要求显式的连接管理。Terracotta DB 适配器引入了一个新的范例。它是一个基于适配器运行时(ART)的适配器，使用 Java API 与 Terracotta DB Store API 进行交互。它在结构化的拖放适配器 UI 中提供插入、更新、删除和查询功能。并且可以在适配器内部创建和管理连接。

## 简单而一致:连接到 Terracotta DB

适配器连接是由 IS 管理的基于 ART 的标准连接。用户在适配器的管理页面中配置 Terracotta DB URI。IS 提供自动连接池。适配器客户端旨在提供健壮且可调的重新连接行为。

[![](img/fe08f921c0ddf01cb5867f3f217e8acb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B-cWMKA2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/1.AdapterConnectionArchitecture.jpg/726874bd-52b2-415d-b235-2bc2443099fd%3Ft%3D1523443154948)

*图 1:适配器连接架构*

通常，可以编辑、删除、查看、复制以及启用或禁用连接。图 1 展示了适配器客户机如何连接到 Terracotta，同时在适配器运行时利用集中式连接管理。

## 一个新的模型:理解 Terracotta DB 中的数据存储

SQL 数据库提供了一种高度结构化的数据交互方式，以及对静态数据模型进行自省的能力。在 Terracotta DB 中，数据被组织成称为数据集的灵活集合。没有预定义的架构或表类型；每一行单元格或记录可以有不同的数据，但属于同一个数据集。

[![](img/42969050d0d8b132c9876a9c178195d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CwPkkOpW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/2.PersonDatasetExample.jpg/7a7c3ed6-0178-4aac-afb3-5e6eea0717d8%3Ft%3D1523443159595)

*图 2:人员数据集示例*

与 Terracotta BigMemory 类似，数据可以存储在堆外和磁盘上。Terracotta DB 部署在活动服务器和镜像服务器的条带中，这些服务器可以集群在一起，使用户能够跨多个条带透明地查询数据。

图 2 显示了一个人员数据集示例。数据可能来自不同的来源，例如，互联网上的表单、结构化的内部数据库或支持票据，但数据集的非结构化性质使其易于收集和集中相关信息，而无需进行昂贵而复杂的数据转换。Terracotta 商店中记录的无模式设计是经典关系数据库和 Terracotta DB 之间的一个关键区别。在跨许多不同设备或装置收集相同类型的数据并以不同格式交付的用例中，这种模型的威力变得显而易见。能够处理大量的动态数据是 Terracotta DB 的核心价值。

## 新模式:与 Terracotta DB 交互

Terracotta DB 适配器提供了一组丰富的机制，用于与动态的 NoSQL 风格的数据集进行交互。

*   **用户指定的** -用户定义要插入或删除到数据集中的单元格，提供单元格名称和单元格类型。
*   **IS doctype** -单元格定义是从 IS 文档的字段中自动解析出来的。数据集定义中引用了 IS 文档。这种模式使得数据可以很容易地从一个系统转移到另一个系统，而流提供了决策逻辑。
*   **数据采样** -根据用户提供的行限制，从 Terracotta 商店的数据集中采样单元定义(单元名称和类型)。适配器返回在采样行中找到的所有单元格类型的定义。能够方便地查询最常见的记录格式是一个有用的工具，用于与本质上不可预测的数据模型进行交互。

用户可以在 Terracotta DB 适配器中创建插入、更新、查询和删除服务。更新服务的一个独特功能是“向上插入”的能力因为所有行都不相同，如果更新操作应用于多行，则该行中可能不存在该单元格。“upsert”操作允许创建新小区或更新现有小区。类似地,“删除”功能可以从一组行中删除一个单元定义，当且仅当该单元存在于该行中。

[![](img/2b6f2d3f0dc0e954c034ebc77cb2c5f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cDAOP9Vh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/3.table.jpg/e65a645a-a4a9-4ae1-84d7-6b18a48e5de5%3Ft%3D1523443165266)

适配器的查询服务查询给定的键。用户可以使用标准的关系运算符(=，<>)基于单元格名称过滤查询结果，然后使用操作数(and，OR)连接这些过滤参数以形成更复杂的查询。

## 常见问题

*   Terracotta 大内存正在被 Terracotta DB 取代吗？不会。这是两种不同的产品，使用情形也不同。
*   我当前的 Terracotta 许可证会发生什么情况？它将继续适用于 Terracotta BigMemory。Terracotta DB 是一个单独许可的产品。
*   Terracotta DB 适配器能与我当前安装的 Terracotta BigMemory 兼容吗？不，Terracotta DB 适配器只适用于 Terracotta DB。
*   我可以在具有 Terracotta BigMemory 的集群中运行 Integration Server 并为我的应用程序连接到 Terracotta DB 吗？是的，您可以使用 Terracotta BigMemory 以集群模式运行 IS，并设置一个 Terracotta DB 适配器来连接 Terracotta DB。

## 如何了解更多

这里有 Terracotta DB 的免费试用版:[https://www . software ag . com/us/products/terra cotta/data fabric/terra cotta _ DB/default . html](https://www.softwareag.com/us/products/terracotta/datafabric/terracotta_db/default.html)
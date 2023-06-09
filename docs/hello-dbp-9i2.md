# 你好 DBP

> 原文：<https://dev.to/techcommunity/hello-dbp-9i2>

**了解如何实施涉及 Software AG 数字业务平台(DBP)许多组件的用例。这一系列短片展示了如何利用 DBP 功能来解决一些典型的业务和技术挑战。**

| 2018 年第三期 | [![](img/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://bit.ly/2LwsQ7K) |
| --- | --- |

在软件行业，程序员通常从最简单的应用程序开始学习一门新语言——比如打印出“你好，世界！”本着同样的精神，视频系列“Hello DBP”展示了尽可能简单的端到端场景，涵盖了许多 DBP 功能。这些以开发人员为中心的视频一次处理一个业务或技术问题，描述 DBP 组件协同工作解决问题的方式，然后展示一个带有六分钟计时器的现场演示，以尽可能多地构建该实现。

随意直接跳转到 [Hello DBP 播放列表](https://www.softwareag.com/corporate/products/bis/dbp_demos/demos.html)，或者继续阅读了解更多。

[https://www.youtube.com/embed/?listType=playlist&安培；list = pl 3 hwmrsyjxioscurnxfo _ Pv _ yrhvgsh 7 o](https://www.youtube.com/embed/?listType=playlist&list=PL3HwmrSYjxiOScRUnxfO_Pv_yrhVGSh7o)

在[www.softwareag.com](https://www.softwareag.com/corporate/default.html)随时收看《你好 DBP》

## 简介

“Hello DBP”的故事以一家虚构的公司 ThingCo 为中心，该公司生产各种东西、小配件和小工具。他们已经经营了很长一段时间，但他们认识到需要提供卓越的客户体验，并及时、敏捷地应对市场和竞争挑战。为此，他们选择了 Software AG 的 DBP 作为软件堆栈，并正在学习如何利用这些行业领先的功能，在首席执行官设定的紧张时间表内推出他们的最新产品 MegaThing XL。

**使用 Alfabet 的战略和企业架构**

ThingCo 的东西部门正在推出他们的新 MegaThing XL，但公司的其他部门也在忙于许多项目和业务目标。了解 ThingCo 如何使用 Alfabet 来跟踪其业务战略和优先事项，并确定重复或互补的 IT 项目。

* * *

**冲刺 1–Cumulocity 物联网试验**

MegaThing XL 将产生大量测量结果，ThingCo 希望为其客户提供对仪表盘和分析的完整访问。CIO 和开发人员使用他们的第一个开发 sprint 来评估 Cumulocity 管理来自这些东西的数据的适用性。

* * *

**Sprint 2–Cumulocity 和 webMethods 集成服务器**

这个 sprint 展示了如何使用 CloudStreams Connector for web methods Integration Server 向 Cumulocity 注册一个新东西，它包装了 REST API 调用。它还通过 CloudStreams 连接器模拟从事物中收集健康统计数据。

* * *

**Sprint 3–集成服务器和 terra cotta DB**

ThingCo 需要一个单一且快速的存储库来存储其业务中所有应用程序的信息。这个 sprint 展示了使用 Terracotta DB 作为使用 terra cotta DB adapter for Integration Server 的事物元数据的内存主存储。

* * *

**Sprint 4–集成服务器 REST 服务**

在之前的 Sprint 中，我们为事物的生命周期操作构建了一些可重用的服务:创建、读取、更新和删除。现在看看将这些转换成 REST APIs 有多容易，可以在 ThingCo 的 it 项目中使用。

* * *

**Sprint 5–集成服务器和 webMethods API 网关**

ThingCo 希望将这些 API 提供给客户组织中的开发人员，以便他们可以构建自己的分层应用程序。API Gateway 无需定制编码就能做到这一点，包括复杂的安全协议。这个 sprint 走过这些步骤。

* * *

**Sprint 6–API Gateway 和 webMethods API Portal**

让第三方开发者可以使用 API 当然很好，但是 ThingCo 如何共享 API 定义和文档，以及使用这些 API 的简单方法呢？这个 sprint 展示了 API Portal 实现这些功能是多么容易。

* * *

**Sprint 7–集成服务器和数字事件服务**

ThingCo 的目标架构包括关键流程里程碑的通知，如创建或删除一个事物。换句话说，他们想要一个事件驱动的架构。数字事件服务(DES)是 DBP 的内置功能，非常适合 ThingCo 的需求，可以无缝、简单地发送或接收来自不同组件的事件。这个 sprint 展示了使用现有的 Integration Server 消息传递基础设施和 Software AG Universal Messaging 发出数字事件是多么容易。

* * *

**Sprint 8–DES 和事件持续性**

ThingCo 必须遵守与涉及某些事物的活动相关的严格政府法规，这意味着要对事物的数字事件进行审计跟踪。开源的 ElasticSearch 引擎是一个长期存储事物事件的低成本存储库，数字事件持久性使得向 ElasticSearch 或 Hadoop 存储事件或从中检索事件变得很容易。

* * *

**Sprint 9–DES 和阿帕玛**

这个 Sprint 展示了如何设置一个简单的阿帕玛应用程序来监听事物健康测量的变化(作为 DES 事件接收)，检测有问题的模式并发出一个新的数字事件来表示警报。

* * *

**Sprint 10–mash zone NextGen 与 DBP 数据馈送**

ThingCo 代理需要能够看到警报以及关于事物的实时和历史数据。这是 MashZone NextGen 仪表盘的一个完美用例，内置了 Terracotta DB、DES 和事件持久性的数据。

* * *

**Sprint 11–IS 和 webMethods AgileApps**

通过之前的 Sprint，我们的“Hello DBP”应用程序正在生成事件和警报，并且可以通过仪表板查看。当健康警报响起时，ThingCo 希望为技术人员提供一个简单的标签系统，以便他们在调查问题时记录工作和文件进度。请看我们在几分钟内构建这样一个没有自定义编码的售票系统。

* * *

**Sprint 12–web methods 动态业务协调器和 AgileApps**

在实现了由每个健康警报触发的简单售票系统后，ThingCo 意识到有许多误报，每次创建一张票都是大材小用。因此，解决方案是

构建一个小型动态流程应用程序，使用用户任务来对警报进行分类，并且只有当它看起来像一个严重警报时，才为技术人员创建票证，以便更详细地查看它。

* * *

**总结和总结**

最后的视频回顾了“Hello DBP”应用程序所使用的功能和架构。它还展示了 MegaThing XL 的最新营销活动。

## 自己试试

就像最简单的 Hello World 示例不会教读者如何编写完整的应用程序一样，这个“Hello DBP”视频系列也不能代替学习如何构建复杂的 DBP 应用程序。尽管如此，我们还是发布了 Hello DBP 使用的底层资产，以及如何将其构建到 GitHub 资源库的说明:[https://github.com/softwareag/hello-dbp](https://github.com/softwareag/hello-dbp)。它不包括产品的安装或配置，因为这在官方产品文档中有所涉及。

我们渴望听到“Hello DBP”是否有帮助，以及如何改进或扩展它。欢迎给我们发电子邮件，地址是[你好，DBP@softwareag.com](//mailto:DBP@softwareag.com)。
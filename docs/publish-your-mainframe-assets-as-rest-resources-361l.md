# 将您的大型机资产作为 REST 资源发布

> 原文：<https://dev.to/techcommunity/publish-your-mainframe-assets-as-rest-resources-361l>

## 加入数字经济无需重写代码

**数字经济的背后是快速增长的 API 经济。API 是数字接口，使组织能够向内部或外部的不同方提供其应用程序数据和功能。**

Adabas & Natural 产品管理总监 Juergen Lind 和 Software AG 高级首席软件专家 Bernhard Fricke

| 2018 年第四期 | [![](img/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://techcommunity.softwareag.com/ecosystem/download/techniques/2018-issue4/SAG_webMethods_EntireX_TECHniques_Oct18-WEB.pdf) |
| --- | --- |

一个简单的例子说明了 API 经济的工作原理，即餐馆、银行和零售商店如何通过将 Google Maps 的地图服务添加到他们的移动应用程序或网页来帮助他们的客户找到他们的位置。在这种双赢的交易中，地图服务公司通过将其地图专业技术外包给其他组织来赚钱，这些组织可以为其用户提供市场上最好的地图服务，而不必投资于不属于其核心竞争力的能力。在真正的数字经济中，你将从使用你的 API 的其他人那里获得报酬。

[![](img/6037ec7b3f0d18976eb8dc0267c2ac9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nUN_Ycpp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/entirex.jpg/2682c409-1e4d-462d-a601-7710f3e3f1d7%3Ft%3D1539850663000) 

**图 1:**API 经济

大型组织内部的其他开发人员可以使用 API，组织单位之间以及外部也可以使用 API。这是 100%数字经济中新的互动方式。大型组织内部的其他开发人员可以使用 API，组织单位之间以及外部也可以使用 API。这是 100%数字经济中新的互动方式。

## 大型机应用如何参与数字经济的秘密:休息

那么，如何才能满足数字经济的需求呢？当您的后端有一个遗留系统时，您如何能够敏捷并快速地在新设备上实现新功能？幸运的是，由于一种称为表述性状态转移(REST)的新架构风格，您的大型机应用程序可以轻松地在数字经济中发挥作用，而无需重写应用程序或创建繁琐的服务。

当然，原则上，您可以重写您的大型机应用程序来参与数字经济。不幸的是，对大多数公司来说，这是不可能的。不仅快速上市的压力与重新编程大型机应用程序所需的开发时间不相容，而且与重写相关的成本和风险也太高，因此这不是一个可行的选择。

有了 EntireX 生成的 REST APIs，您的大型机应用程序中的数据和函数的不可否认的价值可以很容易地被利用，并被带到数字经济的前沿。COBOL、自然主机程序和 REST——这些来自不同年代的不可调和的技术——将无缝地协同工作。

## 自然动手的例子

让我们仔细看看一个具体的例子:想象一个现有的名为 EMPLOYEE 的自然服务器。它为数据库视图雇员实现了列表和细节功能的访问逻辑:

[![](img/a1ac23950e2876886abc06c488734ccf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZBqEAhYJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/entirex2.jpg/3678584f-610d-4ef5-9140-fa5064f1e36b%3Ft%3D1539850614000) 

**图 2:** 用 EntireX 提取自然服务器雇员的 API

使用 EntireX，您基本上可以分三个阶段开发场景，如图 2 所示:1 提取服务器接口，2 REST 资源，3 测试 REST 资源。

## 提取服务器界面

切换到 EntireX 透视图，调用 Natural 的 IDL 提取器并选择 Natural 服务器*雇员*。检查**重新设计接口**来塑造自然服务器以产生用户定义的映射。从单个自然服务器雇员中提取出两个接口:*getlistoemployees*和*getdetailsofeemployee*。

详见 EntireX 文档:*常见集成场景* > *REST 场景* > *从 *REST* 调用自然*。另一个有用的来源是早期的技术文章“如何从 webMethods Integration Server 构建对 Natural 的调用-现代方式”(参见相关文章下的(1))。

## 生成剩余资源

在软件 AG IDL 文件的上下文菜单中，选择集成服务器>生成 webMethods IS Connection 选择单选按钮**创建一个新的 EntireX 适配器连接**；在下拉框中选择 **EntireX RPC 连接**；选中**创建或更新 REST 资源**复选框；按**下一步**，定义适配器服务的属性，如包、连接名等；按**下一步**并指定**剩余资源名称**；按完成。好了...

[![](img/7108f47a2e824a3bb72357b122829791.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uow0cAk4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/entirex3.jpg/bed17e36-0aa0-438c-a38b-7417a8f21388%3Ft%3D1539850618000) 

**图 3:** 用 EntireX 生成剩余资源

## 测试休息资源

使用适当的 REST 客户端，指定 URL、方法和头，并调用您的 REST 资源:

[![](img/6ae3a3c8902d2ddd056907f295442ec7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T80zEkNZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10000089/entirex4.jpg/f83773d0-589a-40d1-9cce-de37fb312c81%3Ft%3D1539850622000) 

**图 4:** 测试休息资源

## 提供 COBOL 资产作为剩余资源

甚至 COBOL 也可以在数字经济中发挥作用，可以作为 REST 资源通过 EntireX 提供:为了生成 REST 资源，提取的 API 是来自自然资源还是 COBOL 资源并不重要。要从 COBOL 源代码中提取 API，请参考 EntireX 文档:*常见集成场景* > *REST 场景* > *从 *REST* 中调用 COBOL* 或更早的技术文章“如何从 webMethods Integration Server 中构造对 COBOL 的调用—现代方式”(参见相关文章下的(2))。

## 总结

正如我们所看到的，使用 EntireX，只需要点击几下鼠标就可以提供您的自然或 COBOL 大型机资产作为 REST 资源。有了 REST APIs，您的大型机服务就可以像最近刚起步或已建立的互联网企业中的任何 API 一样使用。用户甚至不会注意到它是否来自大型机。通过托管组织中最有价值的数据和业务服务，您的大型机应用程序可以在 API 经济中发挥核心作用。多亏了 EntireX 和 REST，所有现代工具都可以使用它。您的大型机数据和服务将不再被视为专有。

EntireX 10.3 提供 REST 支持(上市日期:2018 年 10 月)。要了解更多信息，请阅读 webMethods EntireX 文档中的[常见集成场景](http://documentation.softwareag.com/webmethods/wmsuites/wmsuite9-9/EntireX/9-9_EntireX/scenarios/overview.htm)。

## 相关文章

"[如何从 web methods Integration Server-the modern way](http://techcommunity.softwareag.com/techniques-blog/-/blogs/how-to-call-natural-from-webmethods-integration-server-the-modern-way)"技术中塑造对 Natural 的调用，2016 年 7 月(第 3 期)。

[如何从 webMethods Integration Server 塑造对 COBOL 的调用——现代方式](http://techcommunity.softwareag.com/techniques-blog/-/blogs/how-to-shape-a-call-to-cobol-from-webmethods-integration-server-the-modern-way)《技巧》2018 年 1 月(第 1 期)。

## 术语表

**API **–应用编程接口

* *用户定义的映射** -高级提取以形成对 COBOL 的调用

* *CVM ** -用户自定义映射产生的客户端服务器映射文件。

**IDL **–界面描述语言

**REST **–表述性状态转换一种架构风格来模拟万维网的结构行为

* *是**–web methods 集成服务器
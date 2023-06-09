# 认识全新的现代 SharePoint 服务器:SharePoint 2019 RTM 就在这里！

> 原文：<https://dev.to/mlisidoro/meet-the-new-modern-sharepoint-server-sharepoint-2019-rtm-is-here-2oak>

帖子[认识新的现代 SharePoint 服务器:SharePoint 2019 RTM 来了！](https://blogit.create.pt/miguelisidoro/2018/11/01/meet-the-new-modern-sharepoint-server-sharepoint-2019-rtm-is-here/)最早出现在[的博客上](https://blogit.create.pt)。

SharePoint 2019 终于来了！

最后，SharePoint Server 2019 RTM 版本已经[发布](https://techcommunity.microsoft.com/t5/Microsoft-SharePoint-Blog/Announcing-General-Availability-of-SharePoint-Server-2019/ba-p/272546)，我们现在可以看看新版本为 SharePoint 的内部世界带来了什么。自 [SharePoint 2019 预览版](https://dev.to/mlisidoro/sharepoint-server-gets-modern-sharepoint-2019-is-here-194f)发布以来，变化并不大，但这个新的 SharePoint Server 版本仍然带来了许多好东西。

# 简介

SharePoint 已经走过了漫长的道路，看到微软这些年来一直在平台上引入的不断创新，真是令人惊叹！

随着这一版本的发布，SharePoint Server 终于变得更加现代化，并且包含了 SharePoint Online 中已经提供的一组主要功能。早在 2013 年发布 SharePoint Online 之后，随着 SharePoint 2016 的发布，微软已经采用了[“云优先，移动优先”的愿景](https://blogs.microsoft.com/firehose/2016/05/04/new-cloud-first-mobile-first-vision-and-roadmap-for-sharepoint-plus-general-availability-of-sharepoint-server-2016-unveiled/)，所有新功能首先发布到 SharePoint Online，然后通过大约每 3 年一次的新主要版本以及 SharePoint 2016 中引入的功能包逐步发布到 SharePoint 内部，并允许将最新的云创新带到内部世界。SharePoint 2016 是第一个利用 Microsoft 管理 SharePoint Online 的丰富经验的主要 SharePoint 内部版本，第二个主要版本是最近刚刚发布的 SharePoint 2019。

之前的版本 SharePoint 2016 相比其前身(SharePoint 2013)进行了多项重大改进。以下是它的一些主要特性:

*   新的混合体验，如云混合搜索、混合团队网站和使用 Delve 作为用户档案体验的能力
*   主要限制增加(例如:搜索索引大小、网站集数量、列表视图阈值随着自动索引列创建而增加)
*   minrales
*   功能包
*   持久链接
*   零停机修补
*   集成新的 SharePoint 框架开发模型(SharePoint 2016 Feature Pack 2 提供)

三年过去了，SharePoint Online 发布了许多新功能，现在是时候看看微软在新的 SharePoint 2019 预览版中为我们带来了什么。

# SharePoint 2019 的新功能

让我们来看看新版本将带来的主要功能！

毫无疑问，新版本的主要重点是纳入 SharePoint Online 中已有的现代体验的主要部分，带来响应迅速的用户体验，并允许从任何设备(PC 和移动设备)访问 SharePoint 2019。以下是已经发布并将包含在 SharePoint 2019 中的主要新功能:

*   现代团队网站
*   现代通信网站
*   现代列表
*   现代图书馆
*   现代页面
*   现代 Web 部件
*   SharePoint 主页
*   SharePoint 移动应用程序
*   现代 SharePoint 开发平台
*   支持最新的 OneDrive 同步客户端
*   改进的混合支持
*   改进了对 PowerApps 和 Flow 的支持
*   对大文件支持、字符限制和文件/文件夹名称的改进
*   行政/基础设施改善

以下功能将不可用，可能在未来的功能包中提供:

*   枢纽站点
*   网站设计和网站脚本
*   网站集应用程序目录
*   Power BI web 部件
*   SharePoint Online 中提供的大多数现代 web 部件

正如预期的那样，Office 365 中的一些主要功能将不可用，仍将作为纯云产品提供:

*   Office 365 组
*   计划者
*   微软团队
*   Microsoft 表单
*   Delve(可能在混合场景中与 SharePoint 2019 集成)
*   PowerBI(可能在混合场景中与 SharePoint 2019 集成)

# 现代团队遗址

SharePoint 2019 与其前身的主要区别在于，它将包括 SharePoint Online 中已有的一些主要 UI 改进。其核心组成部分之一是现代团队网站。工作组网站一直是 SharePoint 中的主要协作工具，并将继续如此，但现在它提供了一种现代的、响应迅速的用户体验，允许用户在工作组和整个组织内轻松地协作和共享信息。这是一个新的现代团队网站的外观:

[![](img/63375d0841f05524b6224264be8b973e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6TBn-ill--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blogit.create.pt/wp-content/uploads/2018/08/TeamSites.jpg%3Fresize%3D696%252C464%26ssl%3D1)

SharePoint 2019 中的团队网站与 SharePoint Online 中的团队网站之间的一个主要区别是，它们在 Office 365 组中不受支持，因此无法利用 Office 365 中一些最酷的协作功能，例如:

*   计划者
*   Office 365 群组功能包括共享日历和与 Outlook 的集成
*   能够将团队网站与 Microsoft Teams 中的团队连接起来

# 现代交流场所

SharePoint 2019 将包含的现代体验的另一个主要部分是现代通信网站。虽然工作组网站都是关于协作，主要是人们在 SharePoint 列表和库上工作，并在他们之间共享信息，但通信网站都是关于用户参与，并有一种方法以一种参与的方式与整个部门或整个组织轻松共享相关信息。在团队站点中，大多数用户是活跃的贡献者，而在通信站点中，大多数用户将是消费者，将有一组有限的用户发布供部门或整个组织使用的信息。这是一个新的现代通讯网站的样子:

[![](img/8f57c1234fee1427c90214f30e966613.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cmqtuoSk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blogit.create.pt/wp-content/uploads/2018/08/CommunicationSites.jpg%3Fresize%3D696%252C464%26ssl%3D1)

# 现代列表

列表是 SharePoint 中的核心协作功能之一，允许用户以结构化的方式处理和共享信息，也允许来自外部系统的信息在 SharePoint 中呈现。现代列表将把 SharePoint Online 体验带入内部世界，允许用户以更丰富的用户体验与信息进行交互，可在多种设备和浏览器上使用。这是现代列表的样子:

[![](img/e8af1f731c2639821f24983936792713.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fe__TFuV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blogit.create.pt/wp-content/uploads/2018/08/ModernLists.jpg%3Fresize%3D696%252C464%26ssl%3D1)

其中一个主要优势是可以使用 SharePoint Online 中已有的[条件格式功能](https://docs.microsoft.com/en-us/sharepoint/dev/declarative-customization/column-formatting)轻松格式化列数据。将于今年晚些时候或 2019 年推出的其他主要改进:

*   **行格式化:**能够使用脚本为任何列表或库创建新的格式化体验
*   * *实时列表更新:** 无需刷新页面即可立即查看列表和库的更新
*   **创建列表的新方法:**基于 Excel、模板或其他列表创建列表的能力

# 现代图书馆

库是 SharePoint 中的另一个主要协作功能，允许用户处理文件(和添加元数据)并轻松地与组织中的其他同事共享。现代图书馆将带来 SharePoint Online 中已有的现代体验，并允许用户使用最新版本的 OneDrive for Business 轻松地将其文件与其计算机同步，从而允许用户离线工作，并在恢复在线后将文件同步到 SharePoint。这就是现代图书馆的样子:

[![](img/0c06fb8b74673ee5bfd2f9c80a5c8efd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GXZNIZ1v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blogit.create.pt/wp-content/uploads/2018/08/ModernLibraries.jpg%3Fresize%3D696%252C464%26ssl%3D1)

要更详细地阅读 SharePoint 2019 中包含的所有新功能，请单击[此处](https://blogit.create.pt/miguelisidoro/2018/11/01/meet-the-new-modern-sharepoint-server-sharepoint-2019-rtm-is-here/)。

分享快乐！

帖子[认识新的现代 SharePoint 服务器:SharePoint 2019 RTM 来了！](https://blogit.create.pt/miguelisidoro/2018/11/01/meet-the-new-modern-sharepoint-server-sharepoint-2019-rtm-is-here/)最早出现在[的博客上](https://blogit.create.pt)。
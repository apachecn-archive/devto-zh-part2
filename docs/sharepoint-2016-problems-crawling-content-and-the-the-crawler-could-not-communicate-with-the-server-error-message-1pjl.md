# SharePoint 2016:爬网内容和“爬网程序无法与服务器通信”的问题出错信息

> 原文：<https://dev.to/mlisidoro/sharepoint-2016-problems-crawling-content-and-the-the-crawler-could-not-communicate-with-the-server-error-message-1pjl>

你好，

最近，在安装了 SharePoint 2016 服务器场(要了解如何使用 PowerShell 以自动化方式安装 SharePoint 2016 服务器场的更多信息，请单击[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-1-1i04-temp-slug-1528380)和[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-2-4f44-temp-slug-6645265))后，我在一个客户的 SharePoint 2016 服务器场中配置搜索服务应用程序，并在尝试对一个配置为使用 FQDN URL 和 https 的 web 应用程序(例如【https://intranet.contoso.com】T5)的内容进行爬网时出现了爬网错误。此 web 应用程序正在使用从 SharePoint 2010 迁移到 SharePoint 2016 的内容数据库。

# 问题

在内容源的完全爬网过程中引发的错误是:

"爬网程序无法与服务器通信。请检查服务器是否可用，以及防火墙访问是否配置正确。如果存储库暂时不可用，增量爬网将修复此错误。

# 失败的尝试

当我看到这条消息时，我首先想到的是缺少默认内容访问帐户的权限。该帐户已正确配置为对 web 应用程序具有完全读取权限，这不是问题所在。

我的下一个想法是，这可能是防火墙的问题，搜索服务应用程序正确抓取 web 应用程序内容所需的一些端口没有在防火墙中打开(有关 SharePoint 2016 安装中所需端口的完整列表，请单击[此处](https://blogs.msdn.microsoft.com/uksharepoint/2013/01/21/sharepoint-2013-ports-proxies-and-protocols-an-overview-of-farm-communications/)或[此处](https://blog.blksthl.com/2017/01/24/tcpip-ports-of-sharepoint-2016/))。然而，正确配置防火墙后，错误仍然存在。

在互联网上搜索了一番后，我尝试了以下方法，但都没有成功:

*   禁用服务器上的[环回检查](https://blogs.technet.microsoft.com/sharepoint_foxhole/2010/06/21/disableloopbackcheck-lets-do-it-the-right-way/)
*   禁用 web 应用程序的 https
*   使用 FQDN URL 创建新的空 web 应用程序，并尝试对其内容进行爬网
*   重置搜索索引
*   删除和重新创建内容源

# 解

在所有失败的尝试之后，我能够通过将 web 应用程序扩展到一个非 FQDN URL(例如 [http://intranet](http://intranet) )并将这个 URL 配置为搜索服务应用程序中内容源的起始地址来解决这个问题。此 URL 是内部的(主机仅在服务器的 hosts 文件中配置，而不在 DNS 中配置),仅用于爬网目的。

**重要详细信息:**为了正确显示搜索结果，新添加的 URL 被配置在管理中心的备用访问映射(AAM)的默认区域中。

[![](../Images/0b63deaab55c36945e758d087ff68f1b.png)T2】](https://i0.wp.com/blogit.create.pt/wp-content/uploads/2018/08/Search_AlternateAccessMappings_DefaultZone.jpg)

这里的目标是允许用户使用 https://intranet.contoso.com 的 FQDN URL(例如:T0)访问 SharePoint 门户网站，并在搜索结果中包含相同的 URL。

如果我在 AAM 的非默认区域中配置了非 FQDN URL，搜索结果将始终包括非 FQDN URL，而不是用户用来访问 SharePoint 门户的 FQDN URL。要了解有关使用默认区域抓取内容和 AAM 的所有信息，请阅读以下两篇由 Brian Pendergrass 撰写的精彩文章:

*   [小心搜索 SharePoint 2013 Web 应用程序的非默认区域](https://blogs.msdn.microsoft.com/sharepoint_strategery/2013/02/20/beware-crawling-the-non-default-zone-for-a-sharepoint-2013-web-application/)
*   [备用访问映射(AAMs 解释](https://blogs.msdn.microsoft.com/sharepoint_strategery/2013/05/27/alternate-access-mappings-aams-explained/)

在我的一个开发环境中，我能够将 FQDN URL 配置为搜索服务应用程序中内容源的开始地址，而没有出现这个问题，所以我猜想这种行为一定是由于客户环境中的一些基础架构配置，而我还没有发现这些配置。

如果我找到一个解决方案，允许在搜索服务应用程序中使用 FQDN URL 作为内容源的起始地址，我将更新这篇文章。

希望这有所帮助！

PS:SharePoint 2019 中引入了新的搜索功能。要了解更多有关它们和 SharePoint 2019 中所有即将推出的功能的信息，请单击[此处](https://dev.to/mlisidoro/sharepoint-server-gets-modern-sharepoint-2019-is-here-111a-temp-slug-7171474)。

帖子 [SharePoint 2016:抓取内容的问题和“爬虫无法与服务器通信。”错误信息](https://blogit.create.pt/miguelisidoro/2018/08/10/sharepoint-2016-problems-crawling-content-and-the-the-crawler-could-not-communicate-with-the-server-error/)首先出现在[博客 IT](https://blogit.create.pt) 上。
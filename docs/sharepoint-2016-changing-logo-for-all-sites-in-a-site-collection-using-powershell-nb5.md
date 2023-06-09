# SharePoint 2016:使用 PowerShell 更改网站集中所有网站的徽标

> 原文：<https://dev.to/mlisidoro/sharepoint-2016-changing-logo-for-all-sites-in-a-site-collection-using-powershell-nb5>

帖子 [SharePoint 2016:使用 PowerShell](https://blogit.create.pt/miguelisidoro/2018/10/24/sharepoint-2016-changing-logo-for-all-sites-in-a-site-collection-using-powershell/) 更改网站集中所有网站的徽标首先出现在[博客 IT](https://blogit.create.pt) 上。

你好，

在我最近参与的 SharePoint 2010 到 SharePoint 2016 迁移项目中，我注意到在新的 SharePoint 2016 门户中，所有网站中的徽标 URL 都指向一个无效的 URL。

# 问题

这是因为徽标 URL 指向 SharePoint 配置单元(_layouts 虚拟目录)中的 URL，而 _layouts 虚拟目录 URL 已从 SharePoint 2010 更改为 SharePoint 2016:

*   SharePoint 2010 布局根虚拟目录 URL: /_layouts
*   SharePoint 2016 布局根虚拟目录 URL: /_layouts/15

# 解

由于网站集有许多子网站，并且我希望避免逐个手动更改徽标 URL，所以我构建了一个小的 PowerShell 脚本，该脚本将循环网站集中的所有子网站，并更改每个子网站的徽标 URL。以下是脚本:

$ site logo = "/_ layouts/img/logo . jpg "

$site = " [https://intranet](https://intranet) "

$sites =新对象 Microsoft。SharePoint.SPSite($site)

foreach ($web in $sites。所有网站){

$webUrl = $web.Url

"更改位于$webUrl 的网站徽标 url "

web 美元。SiteLogoUrl = $sitelogo

$web。更新()

}

$sites。处置()

如果您是 SharePoint 开发人员或管理员，并希望了解更多有关如何使用 PowerShell 以自动化方式安装 SharePoint 2016 服务器场的信息，请单击[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-1-54m5)和[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-2-5e36)。

如果你想了解 SharePoint Server 即将推出的版本 SharePoint 2019 的所有信息，请点击[此处](https://dev.to/mlisidoro/sharepoint-server-gets-modern-sharepoint-2019-is-here-194f)。

如果您是 SharePoint 和 Office 365 的新手，并且想要了解所有相关内容，请查看这些[学习资源](https://dev.to/mlisidoro/sharepoint-and-office-365-learning-resources-162e)。

**注意:**此脚本对于 SharePoint 2013 和 SharePoint 2019 也应该有效。

希望这有所帮助！

分享快乐！

帖子 [SharePoint 2016:使用 PowerShell](https://blogit.create.pt/miguelisidoro/2018/10/24/sharepoint-2016-changing-logo-for-all-sites-in-a-site-collection-using-powershell/) 更改网站集中所有网站的徽标首先出现在[博客 IT](https://blogit.create.pt) 上。
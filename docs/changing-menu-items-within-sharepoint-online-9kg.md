# 如何使用 PnP PowerShell 更改 SharePoint Online 中的菜单项

> 原文：<https://dev.to/uisce/changing-menu-items-within-sharepoint-online-9kg>

## 简介

更改 SharePoint 网站中的菜单导航项目是一个繁琐的手动步骤，可能需要很长时间来更新众多网站中的菜单项。这就是 PnP PowerShell 让您的工作/生活变得轻松的原因。

本教程将向您展示如何使用 PnP PowerShell cmdlets 的功能在多个 SharePoint 网站中删除/添加新的菜单项。

## PnP Powershell 概述

SharePoint 模式和实践(PnP)包含一个 PowerShell 命令库(PnP PowerShell ),允许您对 SharePoint 执行复杂的配置和工件管理操作。这些命令使用 CSOM，可以针对 SharePoint Online 和 SharePoint On-premise 工作。
[https://github . com/SharePoint/PnP-PowerShell/blob/master/Documentation/readme . MD](https://github.com/SharePoint/PnP-PowerShell/blob/master/Documentation/readme.md)

## 好戏开始了

首先，我们需要连接到您当前网站的租户

```
$tenantUrl  =  "https://xxx.sharepoint.com/" 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将使用 Get-Credential
获取您的登录凭证

```
$cred  =  Get-Credential 
```

Enter fullscreen mode Exit fullscreen mode

在下一行代码中，我们将把我们的站点页面名称放在一个名为$sites
的数组中

```
$sites  =  "volvo",  "ford",  "opel",  "lada", 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将创建一个 for each 循环，该循环将创建一个名为$name 的变量来遍历$sites
的数组

```
foreach($name  in  $sites)  { 
```

Enter fullscreen mode Exit fullscreen mode

这就是 SharePoint PnP PowerShell 的基本要素。
我们连接到 PnPOnline，使用之前创建的变量 tenantUrl 添加我们的-Url，然后将之前创建的＄name 变量连接到 sites/＄name，然后连接到我们的 Get-Credentials＄Cred 变量，这样我们就可以登录到 SharePoint Online。

```
Connect-PnPOnline  -Url  $tenantUrl/sites/$name  -Credentials  $cred 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们遍历所有的站点导航节点，找到一个名为“我的旧车”的“-Title ”,并将其从所有$sites 导航中完全删除。

```
Remove-PnPNavigationNode  -Location  QuickLaunch  -Title  "My old Cars"  -Force 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以将名为“我的新车”的新导航页面链接添加到导航菜单中。我们找到位置快速启动，然后添加一个新的标题称为“我的新车”的具体网址。

```
Add-PnPNavigationNode  -Location  QuickLaunch  -Title  “My  New  Cars”  -Url  'SitePages/MyNewCars.aspx'  } 
```

Enter fullscreen mode Exit fullscreen mode

运行后，您将看到旧的链接已被删除，一个新的链接节点已被添加到您的导航。

参见下面的完整代码。

```
$tenantUrl  =  https://YOURSITE.sharepoint.com  $cred  =  Get-Credential  $sites  =  "volvo",  "ford",  "opel",  "lada",  foreach($name  in  $sites)  {  Connect-PnPOnline  -Url  $tenantUrl/sites/mycar-$name  -Credentials  $cred  Remove-PnPNavigationNode  -Location  QuickLaunch  -Title  "My old Cars"  -Force  Add-PnPNavigationNode  -Location  QuickLaunch  -Title  “My  New  Cars”  -Url  'SitePages/MyNewCars.aspx'  } 
```

Enter fullscreen mode Exit fullscreen mode

希望这能帮助任何有抱负的 PowerShell 忍者:)
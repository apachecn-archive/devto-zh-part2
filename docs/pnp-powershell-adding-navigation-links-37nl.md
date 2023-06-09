# PnP PowerShell 添加导航链接

> 原文：<https://dev.to/uisce/pnp-powershell-adding-navigation-links-37nl>

如何使用 PnP PowerShell 为众多站点添加包含子链接的导航链接

```
$tenantUrl = "https://YOURTENENT.sharepoint.com/"

$cred = Get-Credential

$sites = "Site1", "Site2", "Site3", "Site4"

foreach($name in $sites) {  

    Connect-PnPOnline -Url "$tenantUrl/sites/$name/SitePages/" -Credentials $cred

    Add-PnPNavigationNode -Title "Parent Link" -Url "$tenantUrl/sites/mysite/SitePages/somesite1.aspx" -Location "QuickLaunch"
    Add-PnPNavigationNode -Title "Child one" -Url "$tenantUrl/sites/mysite/SitePages/somesite2.aspx" -Location "QuickLaunch" -Header "Parent Link" 
    Add-PnPNavigationNode -Title "Child two" -Url "$tenantUrl/sites/mysite/SitePages/somesite3.aspx" -Location "QuickLaunch" -Header "Parent Link" 
    Add-PnPNavigationNode -Title "Child three" -Url "$tenantUrl/sites/mysite/SitePages/somesite4.aspx" -Location "QuickLaunch" -Header "Parent Link" 
    Add-PnPNavigationNode -Title "Child four" -Url "$tenantUrl/sites/mysite/SitePages/somesite5.aspx" -Location "QuickLaunch" -Header "Parent Link" 
} 
```

Enter fullscreen mode Exit fullscreen mode

查看这些优秀的 PowerShell 资源

我的好朋友韦林·乔尔杰夫的博客是一个关于 SharePoint
[的极好资源](https://blog.velingeorgiev.com/provision-custom-list-sharepoint-pnp-powershell)

还发现了这个很棒的小抄
[http://compari.tech/powershell](http://compari.tech/powershell)
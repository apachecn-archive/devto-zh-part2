# 如何使用 PnP PowerShell 从 SharePoint Online 导出 xml 脚本

> 原文：<https://dev.to/uisce/how-to-export-xml-script-from-sharepoint-online-with-pnp-powershell-1mcc>

从 SharePoint 导出 xml 脚本

首先创建一个新的文件夹 mkdir，即 demo 来存放导出的 xml。

接下来连接到您的 SharePoint Online 网站

```
Connect-PnPOnline  -url  https://yourtenent.sharepoint.com/sites/mysite 
```

Enter fullscreen mode Exit fullscreen mode

然后运行下面的命令来检索页面 xml

```
Get-PnPProvisioningTemplate  -out  C:\demo\articles.xml 
```

Enter fullscreen mode Exit fullscreen mode

通过命令
导航查看新创建的 xml 文件

```
explorer  C:\demo\article_xml 
```

Enter fullscreen mode Exit fullscreen mode

当您将 xml 更新到您的规范后，运行以下命令将编辑后的 xml 应用到您选择的站点，即 mysite

```
Apply-PnPProvisioningTemplate  -Path  C:\demo\articles_xml\ 
```

Enter fullscreen mode Exit fullscreen mode
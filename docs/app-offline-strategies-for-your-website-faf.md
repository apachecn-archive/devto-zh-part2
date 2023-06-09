# 您网站的应用程序离线策略

> 原文：<https://dev.to/mozzydev/app-offline-strategies-for-your-website-faf>

这是不可取的，但有时你会遇到这样的情况，你可能需要让你的 sife 离线进行维护。这可能是对您选择的 CMS 的升级(例如 [Umbraco](https://umbraco.com/) ),在幕后处理更改或简单地限制高流量用户。

### app_offline.htm

最简单的方法就是在网站的根目录下放置一个 [app_offline.htm](https://docs.microsoft.com/en-gb/iis/publish/deploying-application-packages/taking-an-application-offline-before-publishing) 文件。这将指示。NET 将所有流量路由到该文件，并显示给用户。它需要是简单的 html，你需要内联任何你想要应用的 CSS。

如果你想完全限制流量，这是很棒的，但是如果你想在后台做一些事情，那么你就有点卡住了。

### IIS 重写

所以，你可以绕过它的一个方法是使用重写规则来否定某个 IP。这将允许任何人通过添加的 IP 访问您的网站，无障碍地浏览网站。其他任何人都会收到离线文件。下面是一个您可以用来实现这一点的转换示例。

```
<rule name="App-offline" stopProcessing="true" enabled="true" xdt:Transform="InsertIfMissing" xdt:Locator="Match(name)">
    <match url="^(.*)$" ignoreCase="false"/>
    <conditions logicalGrouping="MatchAll">
        <add input="{URL}" pattern="/offline.htm$" ignoreCase="false" negate="true"/>
        <add input="{REMOTE_ADDR}" pattern="^127.0.0.1$" ignoreCase="false" negate="true"/>            
    </conditions>
    <action type="Redirect" url="/offline.htm" redirectType="Found"/>
</rule> 
```

Enter fullscreen mode Exit fullscreen mode

### 维修经理

也许你没有一个特定的 IP 你想允许，并有兴趣能够关闭和打开。好消息是，如果你正在使用 Umbraco，那么 Kevin Jump 已经为此创建了一个包。这被称为 Umbraco 的维护模式。你可以登录到 Umbraco，然后你可以选择将限制应用到每个人，如果你愿意，也可以允许后台用户访问。

你可以在这里下载:[https://our . um braco . com/packages/back office-extensions/maintenance-manager/](https://our.umbraco.com/packages/backoffice-extensions/maintenance-manager/)

### 限定停机时间？

有几种方法可以实现这一点。最常见的一种是蓝绿色部署，在这种部署中，你可以在网站的活动和非活动版本之间切换，导致一个版本是活动的，另一个版本被更新和切换。通常这将需要某种负载均衡器，尽管使用 Azure 中的部署插槽可以实现类似的事情。

您可以使用诸如 [Octopus Deploy](https://octopus.com/) 这样的工具来设置各种部署策略，并且能够在整个部署过程中运行定制脚本和操作。您还可以将部署配置为在预定时间运行(例如，预计流量很少的凌晨 2 点)。
# 设置您的 Pi-hole

> 原文：<https://dev.to/thomasbnt/setup-pi-hole-3gkd>

## 目录

*   [简介](#introduction)
*   [Raspbian 安装](#raspbian)
*   [安装墩孔](#install)
*   [Web 面板和结束](#webpanel)
*   [链接](#links)

* * *

【**简介**T2】

这篇文章展示了如何在树莓 Pi 3 B 上安装 Pi-hole。

Pi-hole 用于阻止您不想在网络上加载的不需要的查询和 URL。它保护你免受跟踪，并优化您的网络流量。

***Raspbian 安装和更新/升级包**

首先，从[蚀刻机](https://etcher.io/)或其他应用程序刷新你的硬盘 SD。

[![Etcher](../Images/f3bf4e331bf5c959833d76870966b6f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4TDiTRzz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tzwcx8mxw8gpgigo3qzo.png)

*下面是拉斯边舒展 2017-09-07 建兴*
等待分钟..而且是完整的！

[![Etcher complete](../Images/f159687d3d2fcd71e86e3d82d20c7789.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JVOEjCJu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bzddfqqq98353whzdzfa.png)

连接您的安装程序以进行更新并安装新的软件包。

```
sudo apt update -y && sudo apt upgrade -y 
```

Enter fullscreen mode Exit fullscreen mode

等待好几分钟..在此期间喝一杯好的卡布奇诺。

安装**卷曲** :

```
sudo apt install curl 
```

Enter fullscreen mode Exit fullscreen mode

安装桩孔

你可以用
这个命令[从 Pi-hole](https://github.com/pi-hole/pi-hole) 的 repo 中克隆或者直接卷曲

```
curl -sSL https://install.pi-hole.net | bash 
```

Enter fullscreen mode Exit fullscreen mode

当问你是否想在你的树莓上安装静态 IP 时，选择是。

[![Pi-hole need Static IP](../Images/7e607bcfe13778eaec29ead731ab7505.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9krqhWFu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y8vycumsgj71q439r894.png) 
然后选择你首选的 DNS。*然后你可以改变它，放几个。*

[![Choice your prefered DNS](../Images/740ed6a17fb15bb355bbb0c783b03ab0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AcJv7Z5t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zqt86061tsssjs1kcgrd.png)

您可以选择阻止列表广告:

[![List of Block ads per default](../Images/cd8abeeb29139dd5ca668ab80f0785c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xTp-HHjy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7q0ca1x7g91ogowrghnp.png)

它还会问你是否想要一个 **web 面板**，我强烈**建议**你保持验证状态，这样你就可以在本地网络的任何地方用密码连接到 raspberry，管理你的 DNS/黑名单并获得你的统计数据。

**网页面板和结束**

一旦您安装完 Pi-hole，它就会给您本地主机地址和生成的密码。

用于编辑密码:

```
sudo pihole -a -p 
```

Enter fullscreen mode Exit fullscreen mode

这是连接后面板的预览🎉
黑暗主题由我编辑，你可以在[找到这个资源库](https://github.com/thomasbnt/Night_Pihole)。

[![Preview](../Images/34216ae78e73bf6ffda522a95823a159.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wE1VoR19--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1qv4wm9gsc2plp8209fx.png)

【**链接**

[桩孔库](https://github.com/pi-hole/pi-hole)

* * *

| ☕ | 查看我的[推特账号](https://twitter.com/thomasbnt_)。您可以看到许多项目和更新。你也可以[在给我买杯咖啡上支持我，Stripe 或者 GitHub 赞助商](https://thomasbnt.dev/donate)。感谢阅读我的帖子！🤩 |
| --- | --- |
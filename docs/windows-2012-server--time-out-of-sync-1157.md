# Windows 2012 Server 时间不同步

> 原文：<https://dev.to/catriname/windows-2012-server--time-out-of-sync-1157>

我们的服务台人员正在休假，服务器管理员在路上，我是我们小团队中的最后一个。所以，有这个尴尬的“文档”帖子。

我们的“时钟”服务器(所有服务器中！)变成了 5 分钟不同步。我使用了第三方时间服务器， [NTP Pool 项目](http://www.pool.ntp.org)来解决这个问题。方法如下:

以管理员身份打开 CMD 提示符，并使用以下命令将时间同步设置为第三方服务器:

```
w32tm /config /syncfromflags:manual /manualpeerlist:"0.pool.ntp.org 1.pool.ntp.org 2.pool.ntp.org 3.pool.ntp.org" 
```

Enter fullscreen mode Exit fullscreen mode

来源:http://www.pool.ntp.org/en/use.html

打开服务器管理器工具服务

选择【Windows 时间】

重启

返回 CMD 提示符，输入:

`w32tm /query /status`

一切都很好，又恢复了同步！…
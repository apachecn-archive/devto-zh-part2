# 使用 IIS 在 Ec2 上托管多个网站

> 原文：<https://dev.to/cizu64/hosting-multiple-websites-on-ec2-using-iis--3nad>

使用 IIS 在 Amazon Ec2 上托管多个网站很简单，您需要做的只是添加一个防火墙入站规则，在 IIS 中编辑站点绑定，在 Ec2 安全组中添加一个自定义 TCP 规则来支持您将要使用的端口。

比如你有两个网站，一个是 web app，一个是 Web API，你需要使用同一个 IIS 服务器，首先要做的是:

1)在“网络和安全”选项卡下的 ec2 仪表板中添加自定义 TCP 规则，并将端口设置为 81

2)配置 open IIS 并创建两个网站，创建网站后，
使用 API 上的端口 81 和 web app 上的端口 80 编辑两个网站的绑定。

3)添加防火墙入站规则以支持来自端口 81 的访问

欲了解详细信息，[请查看](http://techcerberus.blogspot.com.ng/2018/02/how-to-host-multiple-website-on-amazon.html)
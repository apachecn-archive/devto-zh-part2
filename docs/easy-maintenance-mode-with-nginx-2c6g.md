# nginx 的简易维护模式

> 原文：<https://dev.to/shano/easy-maintenance-mode-with-nginx-2c6g>

所以我结合了网上找到的几个解决方案，想出了一个用 nginx 设置维护模式的快捷方法。理想情况下，这不应该发生，但在紧急情况下，除了您自己的内部 ips 之外，为每个人创建一个快速维护页面可能会很好。

### Nginx 配置

这里是我为 nginx 使用的配置，它从在主 nginx 配置文件中设置贵公司的内部 ips 开始。然后在实际的 vhost 中，您可以设置它来观察 maintenance_on.html 的存在，当它存在时，抛出一个 503，并将维护页面用作您的 503 错误页面。
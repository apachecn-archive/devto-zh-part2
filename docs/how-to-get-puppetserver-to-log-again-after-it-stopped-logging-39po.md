# 如何让 puppetserver 在停止记录后再次记录

> 原文：<https://dev.to/c33s/how-to-get-puppetserver-to-log-again-after-it-stopped-logging-39po>

今天我注意到我在 debian stretch 上的 puppetserver 版本 2.7.2 不再记录到`/var/log/puppetlabs/puppetserver/puppetserver.log`和`/var/log/puppetlabs/puppetserver/puppetserver-access.log`:(

我不知道为什么。甚至重启服务也无济于事。

尝试了`/etc/init.d/puppetserver restart` & `service puppetserver restart`，同样没有新的日志条目。

对我来说，解决方案是先启动，然后停止 puppetmaster:

```
puppetserver stop
puppetserver start 
```

Enter fullscreen mode Exit fullscreen mode

封面图片由 xresch[https://pix abay . com/en/analytics-information-innovation-3088958/](https://pixabay.com/en/analytics-information-innovation-3088958/)
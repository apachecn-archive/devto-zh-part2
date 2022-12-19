# 三星手机如何秘密监视你的位置

> 原文：<https://dev.to/securestep9/how-samsung-phones-secretly-spy-on-your-location-4pdc>

在为一个客户开发移动应用安全项目时，我不得不调查一个应用程序使用“中间人”技术发出的所有 HTTPS 呼叫，这种技术实际上是通过拦截代理推送来自测试三星 Galaxy S5 智能手机的所有流量。我偶然发现了一些非常奇怪的东西。

手机会不时向以下 URL 发送 POST 请求:

[https://ew.disaster-device.ssp.samsung.com/quloc](https://ew.disaster-device.ssp.samsung.com/quloc)

有趣的是有效载荷。这不是一个普通的 POST 请求，它包含以下 45 字节的字符串:

{"lat":51.5xxxxx，" lon ":-0.1 xxxxx，" dv":"01"}

这是手机的准确位置！在上面的例子中，我用 xxxx 混淆了确切的数字。

因此，三星手机似乎会定期“呼叫总部”并向三星报告它们的位置。为什么要收集这些数据？我将继续调查，但希望公布这些信息，以防其他人发现这个神秘的“灾难设备”网址。

ThreatCrowd 显示了关于目标域和相关子域的更多信息:

[https://www.threatcrowd.org/domain.php?domain = ew . disaster-device . ssp . Samsung . com](https://www.threatcrowd.org/domain.php?domain=ew.disaster-device.ssp.samsung.com)
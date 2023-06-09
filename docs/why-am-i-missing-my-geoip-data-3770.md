# 为什么我的 GeoIP 数据丢失了？

> 原文：<https://dev.to/jermdavis/why-am-i-missing-my-geoip-data-3770>

**更新:**有[这篇文章的后续，指出了当前为 v8 时代的 Sitecore 设置记录 GeoIP 配置的一些额外问题，这些设置更改了](https://dev.to/jermdavis/why-am-i-still-missing-some-geoip-data-mg5)下面的“`Analytics.PerformLookup`”设置。如果你对这个设定感兴趣，也请阅读它！

* * *

本周，我花了一些时间查看一个客户网站，其分析数据缺少 GeoIP 信息。由于他们拥有 Sitecore 的 GeoIP 查找服务的有效许可证，这有点令人困惑。所以，继续我的战斗，写下所有意想不到的场景…

## 这个问题

我被要求查看的客户端站点运行的是 Sitecore 8.2，他们已经[使用应用中心为自己设置了一个 GeoIP 查找许可](https://doc.sitecore.net/sitecore_experience_platform/82/setting_up_and_maintaining/ip_geolocation/setting_up_sitecore_ip_geolocation)。编写直接调用查找管理器的代码似乎是可行的——并返回关于当前用户的有效数据。

但是当你查看 Sitecore UI 中可见的分析数据时，没有一个用户有任何与他们相关联的位置数据。

[![](img/a8182fa8c74206956336996cd15a24a5.png)T2】](https://jermdavis.files.wordpress.com/2018/04/nolocations.png)

看到这个问题后，我四处挖掘，寻找任何关于正在发生的事情的线索。我注意到 MongoDB 中的原始数据缺少与 GeoIP 相关的任何内容。“GeoIPs”集合不存在，并且“Interactions”集合中的记录都不包含“GeoData”元素。

[![](img/11461aa0c621bccb899e345f2c0401df.png)T2】](https://jermdavis.files.wordpress.com/2018/04/nogeoip.png)

## 修罗

这需要一些挠头，谷歌和配置文件差异，但最终我发现了什么。

客户站点运行一个 CM 服务器和两个 CD 服务器，它们共享相同的核心配置文件。这些配置文件包含一个将“`Analytics.PerformLookup`”设置为 false 的更改。

文档(找不到专门针对 V8 的设置——但旧版本中包含了该设置)指出，该设置对于任何部署中的一台服务器都应该是“真”的——以防止死锁，并避免过度消耗查找分配。(注意 [V9 的文档说你可以在多个服务器上设置它为真](https://doc.sitecore.net/developers/server-role-configuration-reference/core-roles/content-delivery.html)——所以在最近的版本中代码已经明显改变了一点)因为这个站点在所有地方都设置了假，看起来集群中没有一台机器实际上执行 xDB 记录的 IP 的异步查找。

我看到一篇博客文章,[建议应该用负载最小的服务器来做这些查询](https://horizontalintegration.blog/2016/08/28/sitecore-8-geo-ip-lookup-configuration-tips/)——这很有道理。所以我在 CM 服务器上部署了一个简单的补丁，让这个设置再次生效:

```
<configuration xmlns:patch="http://www.sitecore.net/xmlconfig/" xmlns:set="http://www.sitecore.net/xmlconfig/set/">
  <sitecore>
    <settings>
      <setting name="Analytics.PerformLookup" set:value="true"/>
    </settings>
  </sitecore>
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

部署完成后，丢失的 MongoDB 收集数据出现了:

[![](img/7a2480d57bd887be1ac6824b65d14d6f.png)T2】](https://jermdavis.files.wordpress.com/2018/04/validgeoipdata1.png)

体验浏览器开始显示新访客的位置:

[![](img/e77682437ddc62226d68cd917ef6e3c9.png)T2】](https://jermdavis.files.wordpress.com/2018/04/visitlocation.png)

成功！
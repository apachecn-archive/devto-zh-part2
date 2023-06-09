# 立陶宛🇱🇹维尔纽斯

> 原文：<https://dev.to/commonshost/vilnius-lithuania--4ph0>

Commons Host CDN 在立陶宛维尔纽斯设立了一个接入点(PoP ),这是它在欧洲的第一个足迹。这使得该网络超越了其东南亚根源。

[![Vilnius city view](img/56812dd02810295d42291a78ce751d8b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nzoDNrjX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/18tss8zq1c420eeq5roi.jpg)

照片:[谢尔盖古舍夫(Flickr)](https://www.flickr.com/photos/sergeigussev/37018865605)

PoP 是一个小羊羔 Mk I 微服务器，由早期支持 Commons Host 项目的 T2 zy GIS T3 赞助和托管。在过去的几个月里，他的专业建议非常令人鼓舞，并大大加快了推出。

[![Zygis in Vilnius wearing Commons Host swag tshirt](img/d7949fe06a8155fd9d64e17d0f178e1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JyhnXJwT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aoj5o9itwllnpqickqci.jpg)

图:[济吉斯](http://zx23.net)在维尔纽斯炫耀羊的战利品(自拍)

## 自动化用 Ansible

这种部署的主要挑战是服务器的物理不可访问性。新加坡和吉隆坡的初始 pop 是手动部署的:亲临现场安装和配置系统。但是，从新加坡到立陶宛部署一台服务器并不划算。

这是 Ansible 的工作，ansi ble 是一个配置管理(CM)自动化工具。在过去的两个月里，Kenny Shen 和我合作完成了 Commons 主机 PoP 部署的完全自动化。

[![Kenny and his custom keyboards](img/b6d5e56dffab62c93e0180db9dce738b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Upupzyoy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lm5d2gcdz125f9fdsp3t.JPG)

图:[肯尼·沈](https://machinesung.com)操作下一级黑客键盘

我们现在能够远程设置、保护和监控服务器。所有代码都可以在[commonshost/ansi ble](https://gitlab.com/commonshost/ansible)git lab 库中找到。欢迎投稿！

[![Code contributors graph](img/0bf71bda859bae80f457bdb8d4cb8deb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6Ol1voTB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8f3idwsj1crug1t20j7q.png)

## 更来了

这个新创建的工具将成为更多部署的基础，包括物理机和云服务器。

维尔纽斯 PoP 目前为欧洲的所有 Commons 主机流量提供服务，由 Geo DNS 负载平衡器管理。这真的没有听起来那么多。

在接下来的几个月里，我们将会更加努力地向开发者推广这项服务。为了支持不断增长的需求，将部署更多的持久性有机污染物。随着更多服务器加入网络，全球流量负载将得到更好的分散和优化，以实现低延迟。激动人心的时刻。
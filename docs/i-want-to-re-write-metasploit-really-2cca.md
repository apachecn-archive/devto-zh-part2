# 我想重写 metasploit？真的吗？

> 原文：<https://dev.to/bdmorin/i-want-to-re-write-metasploit-really-2cca>

最近，我有很多关于应用程序的想法，我想把它们写下来。我一直在努力提高我的 Python 技能。我正在为一些我想公开的 API 开发一些基于 scrapy 的 web scrapers，我正在学习 [Dart](https://www.dartlang.org/) / [Flutter](https://flutter.io/) ，因为我想编写一个移动应用程序来使用我正在开发的上述 API。Javascript 是一个持续的学习过程，NodeJS 似乎是几乎每个现代项目的需求或偏好；但是，我在了解了 Typescript - ARGH 之后，就不能同样看待 Javascript/NodeJS 了！

不过这都不是这篇文章的重点。我有过几次执行网络(IP/服务)发现和转储信息的业务需求，因此这些信息可能会以许多不同的方式被破坏和干扰。我还没有发现任何一个 [FOSS](https://en.wikipedia.org/wiki/Free_and_open-source_software) [IPAM](https://en.wikipedia.org/wiki/IP_address_management) 解决方案可以让我进行发现、随时间进行增量跟踪，并能够对发现的主机和服务进行注释。

数字海洋发布了一款名为 [Netbox](https://github.com/digitalocean/netbox) 的软件，非常棒，只是..没有发现和基本的服务管理。Netbox 确实有一个很棒的 [API](https://netbox.readthedocs.io/en/latest/api/overview/) ，我可能会用它来进行发现，只是无法管理和处理网络增量和服务确实限制了这个优秀软件的使用。

你知道什么是网络资产管理吗？ [Metasploit](https://www.metasploit.com/) 。Metasploit 是一个渗透测试工具，你知道它还有什么特别好的吗？资产管理。我有几个数据库，我扫描工作、家庭和其他东西...Metasploit 在处理我的扫描和更新发现方面做得很好。虽然社区版确实存在，但 Metasploit 是测试人员的工具。我需要的是一个管理工具。

因此..我正在考虑使用其他项目来拼凑一个解决方案来处理发现、增量和表示。像 [nmap](https://nmap.org/) 和 [masscan](https://github.com/robertdavidgraham/masscan) 这样的项目可以很好地处理 discovery *和*，并以我需要的几乎任何格式导出数据。我可以很容易地设置一个 0MQ/MQTT 系统来处理常规扫描、触发深度扫描和一般的发现管理。我图了一个图形数据库(neo4j？)处理关系，搭配一个文档数据库(我爱 Elasticsearch)做元数据，用 Django 或者 Meteor 做框架，我手上可能就有一个有趣的项目了。

现在我陷入了“我应该这样做吗？”-我有其他项目可以做，我想找一个真正有回报的项目。这个项目可能会很有趣，希望对其他管理员有所帮助。

我想我们会看到的。

更新:
以下是 https://alternativeto.net/software/netbox/? IPAM:
[的更多资源 license = opensource](https://alternativeto.net/software/netbox/?license=opensource)
[https://en.wikipedia.org/wiki/IP_address_management](https://en.wikipedia.org/wiki/IP_address_management)
[https://github.com/kahun/awesome-sysadmin#service-discovery](https://github.com/kahun/awesome-sysadmin#service-discovery)
[https://www.g2crowd.com/categories/service-discovery](https://www.g2crowd.com/categories/service-discovery)
[https://arcentry.com/api-docs/](https://arcentry.com/api-docs/)

<sub>图片由[马库斯阴谋](https://unsplash.com/photos/FXFz-sW0uwo)T3</sub>
# 为什么我仍然缺少一些 GeoIP 数据？

> 原文：<https://dev.to/jermdavis/why-am-i-still-missing-some-geoip-data-mg5>

最近，我写了一个我遇到的问题，其中[一个客户的网站完全丢失了其 GeoIP 数据(以及相关的后端分析数据)](https://dev.to/jermdavis/why-am-i-missing-my-geoip-data-3770)。虽然那篇文章中讨论的更改解决了根本没有用于 GeoIP 查找的 MongoDB 数据的问题，但我继续看到一些奇怪的问题，在进行这些修复后，许多用户没有被找到。对此进行分类似乎表明，关于配置 GeoIP 进行分析的一些“常识”是不正确的——因此，以下是我的最新发现:

## 这个问题

[![](img/e77682437ddc62226d68cd917ef6e3c9.png)](https://jermdavis.files.wordpress.com/2018/04/visitlocation.png) 在我的[上一个帖子](https://dev.to/jermdavis/why-am-i-missing-my-geoip-data-3kcl-temp-slug-357725)改变后，我开始看到一些 GeoIP 数据被记录。但是当我仔细查看存储的内容时，我注意到唯一被捕获的数据是加的夫和伦敦的地址。我意识到这两个位置恰好对应于我(在 [Kagool](http://www.bekagool.com) 的内部网络上)和客户作者的网络位置。这看起来很可疑，所以我花了更多的时间查看网站的配置和基础设施的状态。然而，我能找到的信息似乎都没有帮助或相关。

所以在用尽了我所知道的和谷歌的神奇力量后，我试着向 Sitecore 提出支持请求…

## 怎么没错

在我解释了站点的设置，提供了配置和日志文件，并经历了所有提出支持请求的常规步骤后，我得到的建议是，Sitecore 的负载平衡配置及其处理流量的方式可能有问题…

当一些负载平衡器将流量从互联网中继到您网络上的服务器时，它们可以更改服务器看到的网络数据包的源地址。它不是互联网用户计算机(最初发出请求的计算机)的 IP 地址，而是负载平衡器本身的 IP 地址。显然，这将破坏地理位置或基于 IP 地址的安全性。所以需要有一种方法来处理这个问题。向前一步[`X-Forwarded-For`HTTP 头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-For)。当负载平衡器更改源 IP 地址时，它会添加一个指定原始 IP 地址的 HTTP 报头。这允许接收 web 服务器处理该报头数据，以进行地理定位或其他基于源地址的处理。(注意:标题并不总是被称为`X-Forwarded-For`——一些套件使用不同的名称，但这可能是最常见的一个)

因此，支持人员最初的建议是，去往我的集群中的 CD 服务器的互联网流量通过 Azure 中的负载平衡设备，该设备正在重写源 IP 地址，但我的站点没有配置为理解这一点。他们认为我在客户端和 Kagool 用户的日志中看到了位置数据，因为我们访问网站的网络路由经过了一条不同的路由，这条路由没有利用负载平衡。但是他们认为公共流量没有位置设置，因为 Sitecore 不知道源地址是否会被重写。Sitecore 的 GeoIP 配置有一个名为“`Analytics.ForwardedRequestHttpHeader`”的设置，您可以在其中告诉分析处理代码，您的网络流量包括一个“源地址被重写”标头。在我的配置中这个设置是空的。

有了这个建议，我做了一些研究，试图找出负载平衡硬件可能会添加什么标题。我将一些测试代码放到了 CM 和 CD 服务器上，这样我就可以查看客户机请求到达的原始头。但是我看不到任何与本案相关的东西，不管我在网上浏览的时候在哪里。我的笔记本电脑(在公司网络上)和手机(在公共互联网上)都显示如下内容:

[![](img/a4532233d1f06847465958f4c8b89fc8.png)T2】](https://jermdavis.files.wordpress.com/2018/06/headerlist.png)

没有任何东西看起来可能是“`X-Forwarded-For`”或类似的标题…

用支持来解释这个结果，并与拥有 Azure 设置的基础设施人员进行讨论，看看他们是否能在这个问题上有所启发。最终我还是更改了支持的配置——因为这是最终证明这不是问题的最好方法。令人惊讶的是，这没什么区别…

## 最终进入正题

因此，在被这个问题搁置了一段时间后，支持人员回到配置文件中，提出了一个有趣的建议:他们指出站点的 CD 服务器将`Analytics.PerformLookup`设置为 false，但是 CM 服务器将它设置为 true。我很惊讶他们会这样评论，因为我认为这是正确配置的。毕竟，我读过的文档和博客帖子(比如这里的信息、这里的信息和这里的信息)都指出“只在一台服务器上设置”是正确的设置…

支持人员说，我应该在我的集群中所有需要解析位置的服务器上启用`Analytics.PerformLookup`，以便让一切正常运行—[,他们是对的](https://media.giphy.com/media/YEaQWIJRg6dTa/giphy.gif)。一旦在 CD 服务器上更改了此设置，网站的每个访问者都会被正确地查找到:

[![](img/6ea8d380f888e982b57674f180116124.png)T2】](https://jermdavis.files.wordpress.com/2018/06/geoipdata.png)

答对了…

## 结论

因此，如果你使用的是最新版本的 Sitecore(问题中的网站是 8.1 版——所以可能是那个版本或更新的版本)，当文档和博客帖子说“只在一台机器上设置”时，围绕`Analytics.PerformLookup`设置的文档和博客帖子似乎已经过时了。

支持人员表示，代码及其行为似乎在某个时候发生了变化，您现在确实需要在所有需要解析位置的服务器上设置它，[根据讨论 Sitecore v9](https://doc.sitecore.net/developers/server-role-configuration-reference/core-roles/content-delivery.html) 设置的文档。这可能意味着“所有的 CD 服务器”或者“所有的 CM 和 CD 服务器”,这取决于您的集群是如何设置的，以及您想要的结果。

文档中关于“在多台服务器上设置这个会导致问题”的警告似乎不再适用。

所以请记下这一点，避免像我这样花太多时间首次亮相！😉
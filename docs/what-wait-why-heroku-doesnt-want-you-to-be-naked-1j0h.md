# 什么等等为什么:赫罗库不想让你裸体

> 原文：<https://dev.to/flaque/what-wait-why-heroku-doesnt-want-you-to-be-naked-1j0h>

`www.`是子域。我花了很长时间才意识到。

当 reddit 切换到他们的新 UX 时，他们创建了一个`old.reddit.com`，如果你*真的*想要~~一个理智可用的体验~~这个顽固的老东西，你可以用它回到过去。

有很长一段时间，我以为它坏了。我会去我的地址栏，在`www`和`reddit.com`之间，我会加上`.old`，这样你就会得到:

```
https://www.old.reddit.com 
```

Enter fullscreen mode Exit fullscreen mode

这将导致我的浏览器死机，并显示一个 SSL 错误:

[![ssl error](img/9a13defdd4c055922cedeabb43e2a111.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LTdC0bOC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/v9xq6ya.png)

我心目中的`www`并不像`old.`或`blog.`那样是一个子域，而是“一开始就是垃圾的一部分”

每个站点都有`www`对吗？就像`https://`什么的对吧？

# 有些网站是裸体的😱

如果一个域名没有子域(例如:`dev.to`)，我们称之为*裸域名*。

很多网站选择赤身裸体，原因和我不知道如何去更好的网站一样。

大多数用户不明白 www 是如何工作的，也不知道它为什么会在那里。如今，浏览器会自动完成，甚至隐藏 www。许多使用 www 的网站会将他们的裸域名重定向到他们的 www 版本 anywhoo，所以大多数人忘记了它的存在。

从用户的角度来看，www 感觉像是某种 Web 1.0 爵士乐，带有 fire gifs 和点击计数器。

[![web1.0](img/82066fd75a170c892cfc7460634c5f60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kb-CWHf_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/7CKgQ2U.png)

# 我们需要的不仅仅是“A”记录

如果你读过我的上一篇文章，你会知道域名是用 T2 资源记录管理的。

当我们建立一个站点时，有两种常见的记录可供我们选择使用:`A`记录和`CNAME`记录。

对于一个`A`记录，我们为域的“顶点”或根指定一个硬编码的 IP。例如，`dev.to`(在撰写本文时)的 A 记录如下:

```
dev.to.         299 IN  A   151.101.2.217
dev.to.         299 IN  A   151.101.194.217
dev.to.         299 IN  A   151.101.66.217
dev.to.         299 IN  A   151.101.130.217 
```

Enter fullscreen mode Exit fullscreen mode

# 大网站需要大集群

在你最喜欢的提供猫图片的网站背后，奇迹正在发生。一台旋转的电脑离心机正在接收你的网络搜索。计算机一会儿出现，一会儿消失。哔哔声。世界在旋转。

大多数“大”网站运行在多台相互独立运行的计算机上。如果一个网站突然一下子获得巨大的流量(就像板球比赛期间的[片段](https://segment.com/)一样)，新的计算机会自动运转来处理负载。

这些计算机需要自己的 IP 地址，这样我们就可以开始共享流量。许多托管云提供商，如 Heroku，将为您完成这一切。

你给他们代码，他们监控你的流量，然后 3D 打印一个预连接的 macbook，贴上 Heroku 贴纸来处理你的尖峰。或者类似的东西；我不知道，我不是专家。

# A 变成 Cname

您可以在您的域中添加一大堆 A 记录来支持一大堆不同的服务器，但最终，A 记录不能支持动态 ips。

你不能只是添加一个新的 IP，然后期望它能起作用。 DNS 可能需要 24 小时才能完全传播！

当你的网站向全世界展示你的新机器时，你的高峰期已经过去了。

相反，我们可以在这里使用一个`CNAME`记录。CNAMEs 很特别，因为它们不需要指向一个 IP 地址，它们可以指向另一个域:

```
app.segment.com.    252 IN  CNAME   segment.com. 
```

Enter fullscreen mode Exit fullscreen mode

这让我们把新 IP 的问题推给我们的主机提供商(比如 Heroku)。事实上，这正是 Heroku 在其文档中告诉你要做的事情。

# CNAME 是个假正经

根据 DNS 规范，您不能在裸域上使用 CNAME。

所以我们需要突破我们的✨ **子域名。** ✨这个问题最常见的子域是什么？

[![www yaaaa](img/4de3d421b1d8b953062c828cfb248894.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ltcSaURM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/qCl7Dpz.png)

# 哇，哇，哇，但是德夫是裸体的

我知道，这很可怕。不过，这可能不是什么大问题。Dev 主要通过 CDN 发布静态内容。

我们以前见过的那个 IP？那些是快速的 T2 IP。如果让我猜的话，这四个 IP 中的每一个都是[Anycast IP](https://en.wikipedia.org/wiki/Anycast)。这是像 Fastly 这样的 CDN 服务提供的一种变通办法，但是根据 [Fastly 文档](https://www.fastly.com/)，**它比仅仅使用 www** 的 CNAME 更昂贵，性能更差。

# 所以等等，dev 该不该变？

大概不会。在这一点上，更改域可能会导致一大堆问题。另外，Dev 可能*应该*关心 url 的可用性。

随着时间的推移，计算机变得越来越快，但是可用性永远不会改变。

但是当你建立你的网站时，你应该仔细考虑一下`www`。可能会导致以后头疼。
# 一个 0 美元的幽灵博客

> 原文：<https://dev.to/m1guelpf/a-ghost-blog-for-0-pfi>

你可能已经注意到我的博客正在运行 [Ghost](https://ghost.org) ，这是世界上最好的博客平台之一。但是如果我告诉你你可以免费得到你自己的幽灵博客*会怎么样？*

# 托管

Ghost 有三个主要的托管选项:您可以使用 Ghost 自己的托管、使用 DigitalOcean droplet 或使用 Heroku。对于这个博客，我决定选择最便宜的:Heroku。

[![Deploy Ghost to Heroku](img/c5f2ec62efbee76fd41eb9eff7b5c1c0.png)T2】](https://heroku.com/deploy?template=https://github.com/m1guelpf/ghost-heroku)

将 Ghost 部署到 Heroku 非常简单:只需点击上面的按钮，填写名称和 URL，然后点击 Deploy。这里有一个快速的 GIF 来说明这个过程:

<figure>[![A Ghost blog with zero budget](img/2c9fb0dda79295243d184aa400e0ee90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xF5EfoPA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/WnQyKM3.gif) 

<figcaption>英雄预排</figcaption>

</figure>

# 配置您的博客

在你的应用完成第一次部署(大约需要两分钟)后，你将被重定向到你的博客的管理员，在那里你将被引导进行其余的设置(命名博客和创建管理员用户)。此时，我们已经建立并运行了我们的博客，但是有一个限制:我们使用了一个`herokuapp.com`子域。让我们看看如何修复它。

# 自定义域

Heroku 提供免费的自定义域名，只要你验证你的帐户。这意味着，如果你在你的 heroku 账户上添加了信用卡，你就可以为你的博客配置一个自定义域名。幸运的是，整个过程是免费的，不会向你收费，但是从现在开始，你必须对你所做的事情负责。

不管怎样，从[你的 Heroku 仪表盘](https://dashboard.heroku.com)进入你的博客，然后进入设置标签。如果向下滚动，您会发现一个名为“域和证书”的部分，您可以在其中添加自定义域。在[将你的 DNS 指向 Heroku](https://devcenter.heroku.com/articles/custom-domains) 之后，你还必须更新你的`PUBLIC_URL`变量。为此，向上滚动并单击 Reveal Config Vars 按钮，然后找到`PUBLIC_URL`变量并将其更改为指向您的新域。完成这些后，重启你所有的 dynos。下面是另一张 GIF:

<figure>[![A Ghost blog with zero budget](img/161d688eb65ea9db92cf3452e7ca95c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EFNp-cWT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/QwQDIH5.gif) 

<figcaption>又一个侠客行</figcaption>

</figure>

# 保持你的活力

在 Heroku free 计划中，dynos(为您的博客服务的程序)在没有人请求您的博客时被禁用，并在有新请求时再次启用。一个众所周知的破解方法是使用一些正常运行时间检查服务，每 10 分钟对你的站点进行一次 pings，以保持站点正常运行。

# 结论

在使用这种方法之前，检查一下 [Ghost 的官方托管](https://ghost.org/pricing)或 DigitalOcean 是否更适合你。毕竟每个设置都不一样。

* * *

*喜欢这篇文章？考虑[帮助我实现我的梦想](https://miguelpiedrafita.com/patreon) :)*
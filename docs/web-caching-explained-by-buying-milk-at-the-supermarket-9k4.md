# 用在超市买牛奶来解释 Web 缓存

> 原文：<https://dev.to/kbk0125/web-caching-explained-by-buying-milk-at-the-supermarket-9k4>

如果你曾经在超市买过牛奶，那么你可以理解服务器端和浏览器端的缓存。

如果你是一个狂热的互联网用户(你可能是)，你已经受益于一次又一次的缓存。但是，你可能不知道它何时或如何在幕后发挥它的魔力。

从开发人员的角度来看，缓存使得构建高性能的 web 应用程序和 web 服务器变得更加容易。开发人员不需要不断优化被成千上万的请求淹没的服务器，而是可以实现缓存协议，使生活变得更加轻松。

由于缓存可能会在 1 秒钟加载一个页面和 2 秒钟之间产生差异，所以这种影响可能会有点…不明显。但是，如果你想处理大量的用户，这是必要的。

在过去的 web 应用程序中使用缓存之后，我意识到必须有一种更好的方式来解释它，而不仅仅是浏览术语。我注意到它与牛奶从农场到冰箱的路径非常吻合，所以我认为这是一种更好的解释方式。

为了理解这个指南，你只需要知道关于 web 服务器的[基础知识。让我们开始吧！](https://blog.codeanalogies.com/2018/04/26/web-servers-explained-by-running-a-microbrewery/)

### 如果没有缓存，互联网会是什么样子？

在我们进入缓存之前，让我们想想如果没有缓存，互联网会是什么样子。想象一下，你生活在 18 世纪或 19 世纪的一个农村地区。你拥有一个农场，却没有冷藏设备。你的农场里有几头奶牛，但是它们的奶没有那么值钱，因为它们会很快变质。

快速中断:一些文化仍然没有获得冷藏。他们要么直接从奶牛的乳房里喝生牛奶，要么把牛奶和谷物混合，让它发酵。有意思。

不管怎样，你想把你的牛奶卖给你村子里的其他人。但是，他们喝牛奶的时间非常有限。比方说，你的一头奶牛每天能产出一加仑牛奶。但是，如果太多人出现在你的农场找牛奶，你需要送一些回家，让他们等到第二天。

[![](img/c69ec7457130120bdb20ea55f4ce8f68.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xpVDYfuR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AUqjXaIdDFuVLfE5k)

此外，你甚至不能考虑增加更多的奶牛和扩大经营规模，因为你的分销渠道如此有限。只有你村子里的其他人才能买你的牛奶。你有一些明确的限制。

[![](img/8a82b0b6df937ec64ac193f7b2891239.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MG_uTo8Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2ALaOWTms2NkZtDFj5)

如果没有缓存，您会受到服务器计算能力的限制。缓存用于加载静态资产，例如:

*   形象
*   半铸钢ˌ钢性铸铁(Cast Semi-Steel)
*   静态 HTML 文件
*   JavaScript 文件

默认情况下，服务器必须为每个传入的请求提交新的响应。但是，一个加载页面的请求实际上可能意味着 4 个独立的请求——分别来自上述类别。当您考虑更大的图像文件时，您的服务器可能会被世界各地的用户淹没。然后，用户在等待页面加载时会经历缓慢的加载时间。

[![](img/69ff61ef31cdfef7f61cca233033c42b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NT7NT6Hg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2A4ajis8HoFZeo7SRf)

理想情况下，您会希望通过存储对常见请求的响应来缓解对服务器的需求。您的服务器不需要处理每一个新的请求，相反，您的缓存可以提供即时响应。你总是可以支付更多的服务器，但这可能会导致无法控制的开支。

### 什么是服务器端缓存？

回到我们的农场场景。知道什么能让经营一个成功的奶牛场更容易吗？

有冷藏的超市！

这样的话，人们就不需要出现在你的农场，并立即消费牛奶。你可以一次安全地保存它几个星期。

超市消除了你农场的许多压力，因为你的奶牛不会被期望实时生产。超市会处理需求。你只需要让奶牛每天都有产出。更好的是，周围所有村庄的居民现在都可以从你的农场购买牛奶，因为冰箱里永远都有牛奶。

[![](img/2f90488ba86bcc3fc66116fa32445e04.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GufaH_YX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Akko1_aNf6X-cZzUy9IwF-A.jpeg)

[![](img/d7c9e604df79e9dd1c8619f3247dfc17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6lKG2Gks--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ajwi2U9sBznjtd_zZoszx6w.jpeg)

就像超市一样，[服务器端缓存](https://www.digitalocean.com/community/tutorials/web-caching-basics-terminology-http-headers-and-caching-strategies)将处理流行的请求，并更快更可靠地交付内容。

在上图中，我使用了术语**缓存代理**。缓存代理是一种服务器，它存储用于响应常见请求的静态文件。缓存代理将拦截常见的请求并快速传递响应。它可以防止这些请求对您的主 web 服务器造成压力。

你可能有一大堆问题，比如，

1.  是什么决定了一个“受欢迎”的请求？
2.  代理将缓存响应多长时间？

这需要一个更长的关于设置缓存的教程，但是现在，你应该知道一个叫做**新鲜度**的重要概念。缓存代理将拥有在不同时间缓存的不同文件，它需要决定是否仍然应该为这些文件提供服务。这将取决于您的**缓存策略**。

这就像超市里的牛奶一样。超市经理需要决定在扔掉你的牛奶之前，他们可以保存多长时间。缓存代理通过**缓存命中率来衡量他们的成功—** 可以通过缓存服务器提供的内容的百分比。

### 什么是 CDN？

到目前为止，有一家杂货店在卖你的牛奶。虽然这是一个很大的进步，但是你仍然没有办法把你的牛奶送到当地商店范围之外的人手中。如果你想扩大经营规模，你需要增加更多的商店。

所以，假设你开始向更多的超市分销你的牛奶。现在，您可以满足更大地理范围内的客户。这类似于内容交付网络或 CDN。CDN 是位于世界各地的一系列代理服务器(就像我们上面讨论的一样)。

作为一个最终用户，你可能会觉得高速互联网让大多数网站加载速度非常快。然而，这仅仅是因为他们使用 cdn 来快速传送静态文件！

如果你位于英格兰，并且你正试图加载一个缓存在弗吉尼亚州的服务器中的文件，你将经历一些延迟，因为原始信号只能沿着数千英里的电缆快速传播。在英国的本地缓存代理将允许网站加载速度更快。

[![](img/3dafb1f9a24b6e2a67d06cbcdcef4d1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zj1ORgnE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ARvgDAlDHt24dcvBImoxCZQ.jpeg)

[![](img/2957e306b78e54c732a644dbd1561c49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Mu8IjCho--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AH8xPw2BZp_bAwgaUKeI2eA.jpeg)

因此，您的服务器可以向 CDN 网络中的每个代理服务器发送静态资产的副本，并且它们可以处理本地请求，直到资产不再“新鲜”。一些常见的 CDN 提供商包括 Rackspace、Akamai 和 Amazon Web Services。

### 浏览器缓存呢？

现在，全国(或世界)的人们可以从你的农场把冷牛奶带回家。只有一个问题——他们没有办法把它存放在自己家里。你的顾客仍然需要在买了牛奶后很快喝完，然后再去杂货店买更多。所以，这个系统还是没有特别好的服务客户。

解决办法？一台冰箱！

有了冰箱，你可以把牛奶储存在本地，而不必再去超市。用缓存的术语来说，我们在讨论一个完全独立的位置来存储静态资产，因为它在客户端，或者和浏览器在同一台计算机上。我们的代理服务器位于一个远程位置。

这对于像脸书或亚马逊这样你可能经常访问的网站来说是非常好的。这对他们的服务器成本也很有利，因为他们可以减少需要处理的请求数量。

[![](img/ea7a92c49e238c1f2e2428232302d871.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4CkaQeSQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ARncfZ7NATKE9ciV-F54ODg.jpeg)

[![](img/889a58c7fba4835641875ee839c25bd5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wZNcCCNW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AWct_mY7Bbuvsd4pwyQJ5Dg.jpeg)

需要注意的一点是——我们并不是说牛奶会神奇地出现在你的冰箱里！您仍然需要发出到达服务器或代理服务器的初始请求。之后，您可以在本地缓存一些文件。

您的浏览器如何知道何时向服务器请求新文件？否则，您将永远不会体验到这些本地文件的更新版本。

就像牛奶生产商在牛奶包装上标注日期一样，服务器会在 HTTP 响应头中添加某种标识符。实际上有 4 个独立的系统用于 HTTP 缓存。上面显示的场景非常类似于“截止日期”方法。其他一些方法仍然要求您的浏览器在发送缓存文件之前检查服务器。

[![](img/cd47d1b10270df44740f67afcd77cfee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--INt3FqYX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2A7yAXQNZN-0XzIQHB)

### 何时开始使用缓存

假设您正在构建您的第一个 web 应用程序。在拥有成千上万的用户之前，您可能不需要担心缓存协议，因为服务器成本仍然很低。然而，随着规模的扩大，如果您希望应用程序快速加载，您将需要实现缓存。

例如，Heroku 是部署您的第一个 web 应用程序的绝佳工具。但是，它要求你使用一个[单独的服务来实现缓存](https://devcenter.heroku.com/articles/http-caching)，就像亚马逊的 CloudFront 或 CloudFlare。这需要更多的时间来学习。

在浏览器端，当您试图用新的静态资产重新加载页面时，您可能经历过缓存，但是页面就是不变。无论你刷新页面多少次，都不会改变。

这通常是因为浏览器端的一些缓存协议。要绕过浏览器的缓存，向服务器请求新的资源，你可以在 Mac 上使用 **Cmd+Shift+R** 或者在 PC 上使用 **Ctrl+Shift+R** 。

### 获取更多可视化教程

你喜欢这个教程吗？查看我在 [CodeAnalogies 博客](//codeanalogies.com)上对 web 开发主题的其他可视化解释。
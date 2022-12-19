# 媒体只是你的一群 CDN 的边缘服务器，你自己的博客才是源头

> 原文：<https://dev.to/nhoizey/medium-is-only-an-edge-server-of-your-posse-cdn-your-own-blog-is-the-origin-3aga>

显然，这篇文章最初是在我的博客上看到的[。](https://nicolas-hoizey.com/2017/11/medium-is-only-an-edge-server-of-your-posse-cdn-your-own-blog-is-the-origin.html)

人们倾向于懒惰(我经常这样)，选择最容易的工具来完成工作，即使它不是最好的。 [Medium](https://medium.com/) 是新的[博主](https://www.blogger.com/) <sup id="fnref1">[1](#fn1)</sup> ，但是有了[更好的内容编辑 UI](https://medium.com/@ev/writing-in-medium-df8eac9f4a5e) ，以及更清晰的内容呈现给读者 <sup id="fnref2">[2](#fn2)</sup> 。人们可以在几分钟内创建、记录并开始发布，这真的很简单。但是这一切怎么可能是免费的呢？

# 媒体可以对你的内容做任何事情

几年前，Andrea Phillips 在她的文章《媒体的问题》中已经说过:

> 你把你的作品免费交给一家公司，他们可以做任何他们想做的事情。永远。不用付钱给你。永远不会。

自 Andrea 在 2013 年发表文章以来，服务条款已经发生了变化，但仍需仔细考虑(强调我的观点):

> 通过向 Medium 发布内容，您给予我们在 Medium 服务上发布内容的非排他性许可，包括**与发布内容**合理相关的任何内容(如存储、显示、重新格式化和分发)。鉴于 Medium 授权您访问和使用服务，**您同意 Medium 可以在服务**上做广告，包括与您的内容或其他信息的显示相关的广告。**我们也可能使用您的内容来推广 Medium** ，包括其产品和内容。— [内容权利&责任](https://medium.com/policy/medium-terms-of-service-9db0094a1e0f#8c81)当前[中期服务条款](https://medium.com/policy/medium-terms-of-service-9db0094a1e0f)的一节

当然，Medium 是一个明显的害群之马，但是其他大多数集中式和封闭式服务也是如此。

# 坏事会发生

此外，Medium 是一种可以像以前的许多服务一样消失的服务，您的内容可能会丢失。我绝不会接受为我的内容冒这样的风险。

此外，内容的适度性有时也会有问题，就像几天前彼得·奥肖内西(Peter O'Shaughnessy)经历的那样:

> ![unknown tweet media content](../Images/96a14216eb3a7a1eb60470707534abf9.png)![Peter O'Shaughnessy profile image](../Images/3c89058c992c35895bb12f0a0e4f0150.png)彼得·奥肖内西@ poshaughnessy![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)没有什么比让你的新博文被 [@Medium](https://twitter.com/Medium) 暂停更能让你想起自我托管的美好时光了😳[twitter.com/poshaughnessy/…](https://t.co/TpSUEPZgQP)2017 年 11 月 07 日上午 09:51彼得·奥肖内西 @poshaughnessy@Medium 在@samsunginternet 博客上发表了新文章但显示不可用&网址显示“已暂停”。请帮忙？https://t.co/wzSRWIPJ5M[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=927836002915356672)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=927836002915356672)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=927836002915356672)

消失内容可能发生在任何平台上，就像最近在 Twitter 上发生的那样，但是当你不拥有这个平台时，你没有任何办法来解决这个问题，你只能等待平台所有者去做，如果这让他高兴的话。

# 波塞是最好的选择

POSSE 的意思是“在你的”、“在你的”、“在你的”、“在”、“在”、“在”、“在你的”、“在”表示“在别的地方”。

像 [Webmentions](///2017/07/so-long-disqus-hello-webmentions.html) 一样，POSSE 是 [IndieWeb](https://indieweb.org/) 的一个创造，它给出了这样的描述:

> POSSE 让你的朋友继续使用他们用来阅读你的东西的任何东西(例如脸书、Tumblr、Twitter 等筒仓聚合器)。).
> 
> 这是 IndieWeb 运动不同于“每个人都在自己的网站上写博客”，也不同于“每个人只需安装并运行(YourFavoriteSocialSoftware)”等的关键部分。单一栽培解决方案。

你真正拥有你的内容，在你自己的(酷的)网址上，并且由你来[使它持续](https://remysharp.com/2016/12/22/cool-uris-dont-change)。

但是你也可以在其他可能有更大影响的平台(大多数是集中的和封闭的)上联合它。理想情况下，这些平台上的内容副本应该链接到您的规范 URL。甚至[介质也允许它](https://help.medium.com/hc/en-us/articles/217991468-SEO-and-duplicate-content)。

IndieWeb 列出了一些获得流行服务和社交网络的方法。

多亏了[的 Atom feed](https://nicolas-hoizey.com/atom.xml) 、 [IFTTT](https://ifttt.com/) 和 [Buffer](https://buffer.com/) ，这篇博客已经出现在 [Twitter](https://twitter.com/nhoizey) 和[脸书](https://www.facebook.com/nhoizey)上。

# 内容传递网络

你可以把这些平台想象成内容交付网络中的边缘服务器。

目标是“相对于最终用户在空间上分布服务，以提供高可用性和高性能”。向用户提供 HTML 页面或静态资源的边缘服务器通常是最近的服务器。这些边缘服务器从由内容所有者操作的源服务器获取内容。最终用户可能永远不会直接联系源，但最重要的是她得到了页面和相关的资源。

**Medium、Twitter、脸书和其他网站是你内容的边缘服务**，在这些平台上，你会发现——不幸的是——比你自己的网址上有更多的读者。

**你的平台就是原点**。

# 让我们把自己的内容复制到介质上

你可以[手动将任何帖子导入到媒体](https://help.medium.com/hc/en-us/articles/214550207-Import-post)中，就像我最近的帖子中的[大表情符号没有被 Chrome 渲染](///2017/10/chrome-fails-showing-big-emojis.html)一样[。但是有很少的格式化选项](https://medium.com/@nhoizey/chrome-fails-showing-big-emojis-8b274d98f17b)，它真的不适合这种带有代码块的技术写作。

还有一个 API ，可以让你满足于中等程度。Jeremy Keith [写了关于这个](https://adactio.com/journal/9694)的博客，并分享了[他的 PHP 代码](https://gist.github.com/adactio/c174a4a68498e30babfd)。

如果你和我一样使用 Jekyll，亚伦·古斯塔夫森，我在网上使用的插件[的作者](///2017/07/so-long-disqus-hello-webmentions.html)创造了另一个从 Jekyll 交叉发布到媒体的[插件，从 Jeremy Keith 的 PHP 代码中获得灵感。我可能很快会尝试它。](https://www.aaron-gustafson.com/notebook/crossposting-to-medium-from-jekyll/)

# 但是设置自己的平台就没那么容易了

我知道对大多数人来说，创建和托管自己的平台并不容易，就像 [Nicolas 在 Twitter 上告诉我的](https://twitter.com/nsteinmetz/status/928183482882428928):

> ![Nicolas Steinmetz profile image](../Images/75a3aecd4901e68e8f9f41034fdc35c4.png)

Remouk 还补充说，我们自己的平台几乎不存在，他对社交分享的评论多于实际内容本身:

> ![J'ai migré sur @danstonchat profile image](../Images/8632ee9b3d32d5855133156788007b21.png)【我迁移到了@ dantonchat】@ remouk】![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)[@ nhizey】我对文章分享的评论多于对文章本身的评论——甚至(* t18)2017 年 11 月 9 日:16 AM - 08 日](https://twitter.com/nhoizey)[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=928189534264754177)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=928189534264754177)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=928189534264754177)

我也有同样的经历，这就是为什么我真的很高兴[web references、web reference . io 和 Bridgy](///2017/07/so-long-disqus-hello-webmentions.html) 允许我将 Twitter 评论返回到我的帖子。

所以，是的，很明显不是每个想要发布内容的人都能够建立和托管自己的平台。即使是精通技术的人也很难做到这一点。

# 您的“自己的”平台是最接近您可以(获得帮助)设置的平台

如果你现在做不到，让我们假设你的长期目标是拥有自己的平台。

你可以从选择一个更好的平台来创建你的内容开始，这个平台将服务于你想要的所有边缘平台，以获得更大的影响力。

这样一个平台可以是 Github，有 Github Pages，在这里你的内容将比在媒体上更属于你。你也可以使用 Wordpress.com 主办的。或者任何你有足够信心不会失去你的内容和所有权的地方。

但是**首先，选择你自己的域名**，并使用它作为你的规范 URL。如果您希望以后更改平台，您可以更改您的域指向的位置，您的规范 URL 将是安全的。

它在 Github 上是免费的，在 Wordpress.com 上每月只需 4 欧元。

这是 IndieMark 指标集中的第一项，用于“衡量一个网站的 indieweb 特性，以及在你的网站上逐步采用 indieweb 特性的方法”。

真的很值得。

我不是唯一推荐这个的人:

> ![Nicolas Hoizey profile image](../Images/99b288cc2adcce81dd8b89a43f3d697b.png)尼古拉斯·霍伊泽[@ nhoizey](https://dev.to/nhoizey)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)>如果你还没有博客，我劝你，这个周末就开始写吧。你自己的博客，有你自己的内容，在你自己的领域。—[@ CSS wizardy](https://twitter.com/csswizardry)[csswizardry.com/2017/11/ten-ye…](https://t.co/8pOMUdRJtI)2017 年 11 月 19 日下午 16:58[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=932291964434898944)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=932291964434898944)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=932291964434898944)

* * *

1.  Ev Williams 是 Medium 的首席执行官，是 Twitter 的联合创始人之一，但之前是博客的负责人。 [↩](#fnref1)

2.  我必须说，我真的不喜欢他们处理图像的方式，用这种丑陋的模糊。 [↩](#fnref2)
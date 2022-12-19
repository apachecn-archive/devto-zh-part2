# 关于处理停机:案例研究

> 原文：<https://dev.to/david_ojeda/on-handling-outages-a-case-study-59cb>

如果你碰巧使用 Basecamp 3 来管理你的项目，你可能已经注意到 2018 年 11 月 8 日的一次巨大停电；持续了将近 5 个小时。

问题是他们没有对表的主键使用 bigint，所以他们用完了 id。 *TLDR* 解决方案，来自 David Heinemeier- DHH，Ruby on Rails 的创始人和 Basecamp 的创始人兼首席技术官:

> 我们让一半的副本离线，进行了 3h 迁移，将它们恢复在线，现在将转换另一半的设备。

我写这些不是为了揭露和/或抨击他们。

我写这封信给**赞扬他们关于整个停电的沟通和公开**。

我写这篇文章是为了揭露过度沟通、诚实、谦逊和清晰是如何产生影响的，尤其是在困难的情况下。

* * *

给你一些背景信息，他们 Twitter 账户上第一条关于出错的通知是在 11 月 8 日早上 5:40:

> ![Basecamp profile image](img/cbcb1bfa700c82c906f4b6472724970c.png)base camp@ base camp![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)base camp 3 现在有麻烦了。很抱歉。我们正在修复，并将随时向您更新。2018 年 11 月 08 日下午 13:40[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1060527469361537024)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1060527469361537024)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1060527469361537024)

从那条推文开始，直到一切恢复正常，还有 15 条推文不断更新！最后一封是 11 月 8 日上午 10 点 47 分，由 DHH 本人签名:

> ![Basecamp profile image](img/cbcb1bfa700c82c906f4b6472724970c.png)base camp@ base camp![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)base camp 3 此刻正在备份。我们不得不切换到一组备份的缓存服务器，它们目前还在运行。很明显情况危急，所以还没有脱离危险。在这样一个艰难的日子里，我们请求更多的耐心。很抱歉😢^dhh2018 年 11 月 08 日下午 18:47[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1060604787819827201)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1060604787819827201)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1060604787819827201)

所有这些信息都是非常重要的。您知道他们非常努力地让一切正常运行，您可能也知道停机会变得非常糟糕。

尽管所有的混乱可能正在发生，他们继续在他们的 Twitter 账户、状态页面和博客上发布关于原因和解决方案的具体细节的更新。不仅如此，DHH 还发布了一些关于停机的更多技术细节，在这里**他链接到了可以保存一切的拉请求**:

> ![DHH profile image](img/6a8b92a001ba3bd0d023062c0c039b9e.png)@ dhh![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我并不经常为我们在 [@basecamp](https://twitter.com/basecamp) 的工作感到羞耻。但是今天就是这样的一天。由于不能在每个表上使用 bigint 作为主键而陷入只读模式几个小时是令人尴尬的。从 5.1 开始，这就是 Rails 的默认设置🙈[github.com/rails/rails/pu…](https://t.co/FaGYDBrROU)2018 年 11 月 08 日 16:10 点[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1060565296048562177)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1060565296048562177)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1060565296048562177)

* * *

我发现所有这些**都非常有价值，令人宽慰**。尽管这是一次很长的停机，他们还是优雅地处理了每一次客户互动。我不会因为他们提供了这么多关于问题/解决方案的信息而感到不安。

见鬼，那天早上**我甚至更有效率，因为 Basecamp 仍然是只读的**；我可以检查我这边有什么事情要做，然后毫不分心地去做。

我是公司停机的一部分，也是原因之一，压力真的很大。我们甚至不能处理 Basecamp 3 的流量。

因此，正如 DHH 所说，**这是一个保持谦逊的提醒。我们可能是下一个卷入这种情况的人。**我们都会犯错，这是不可避免的**，但从长远来看，知道如何恰当地沟通才是最重要的。**

* * *

希望你喜欢这个简短的❤️漫步
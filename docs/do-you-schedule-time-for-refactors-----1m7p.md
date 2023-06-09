# 你会为重构者安排时间和任务吗？

> 原文：<https://dev.to/chrisvasqm/do-you-schedule-time-for-refactors-----1m7p>

今天晚上，我坐下来读了鲍勃叔叔的一些推文，它们非常有趣，但其中一条让我印象深刻:

> ![Uncle Bob Martin profile image](img/0af8ac848dd824a081a1ca360b623177.png)【鲍伯·马丁大叔】@ unclebomartin![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)“重构”这个词绝对不应该出现在一个时间表中。重构不是一个故事或待办事项。重构不是一项预定的任务。重构是即时和持续的。就像在浴室洗手一样。你总是这么做。2018 年 7 月 31 日上午 11:23[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1024254121338126336)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1024254121338126336)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1024254121338126336)

我完全能理解他的意思，但是我最近开始做一个项目，这个项目非常复杂，实际上拖慢了我们的进度。不管你的资历有多高，你都会发现自己在处理看似基本的任务时不断增加“每分钟的 WTF”计数器。

如果你想知道“每分钟 WTF”指的是什么，下面的图片可以帮助你:

[![Wtf per minute](img/5732a8e6924f061e1f4ad87f8a913011.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9aadooW6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7k5q0aq8nt0mmgruaj4d.png)

这让我们与经理交谈，试图说服他们，如果我们继续走这条路，每走一步，我们的生活都会变得更糟。

因此，这导致了一堆必须被估计的任务，被划分到多个开发中，以减少总时间(只要它们可以并行工作)等等。

这意味着我们将不得不花费至少 1-2 个月的时间来搁置一切，直到我们可以重构我们的代码，使之更易于管理，对于有经验和没有经验的开发人员都是如此。

个人觉得鲍勃大叔的观点*应该*是每个人都应该有的思考方式，但不应该是一成不变的。根据重构的关键程度或规模，您可能需要跟踪这些重构任务。

在这条推文中发现的其他观点:

> ![Joseph Short profile image](img/90cf3254899bdbc0b2e3df0ce931e27d.png)约瑟夫肖特@约瑟夫肖特![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ phill _ Steele](https://twitter.com/phill_steele)[@ unclebomartin](https://twitter.com/unclebobmartin)正是这个原因它应该是一个单独的任务；产品负责人应该批准所有工作。这种方法允许工程团队把钱花在其他地方可能更好的项目上。我同意最初的前提，如果你在做 TDD 的话。不是每个商店都这样。2018 年 7 月 31 日 22 点 56 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1024428546851307530)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1024428546851307530)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1024428546851307530)

得到的回答是:

> ![Monadic Paradox profile image](img/b1a0830b982665c5bf2420505f497e17.png)一元悖论@ therealtrunerd![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ JosephPShort](https://twitter.com/JosephPShort)[@ phill _ Steele](https://twitter.com/phill_steele)[@ unclebomartin](https://twitter.com/unclebobmartin)我不同意这个说法。病人不告诉医生诊断或治疗。产品负责人不会告诉程序员如何做她的工作。做事情不需要批准对吧(我不会自讨苦吃)2018 年 8 月 01 日下午 12:45[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1024637347856572416)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1024637347856572416)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1024637347856572416)

然后鲍勃叔叔补充道:

> ![Uncle Bob Martin profile image](img/0af8ac848dd824a081a1ca360b623177.png)鲍伯·马丁叔叔@ unclebomartin![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ therealtrunerd](https://twitter.com/therealtruenerd)[@ JosephPShort](https://twitter.com/JosephPShort)[@ phill _ Steele](https://twitter.com/phill_steele)对。产品负责人不批准所有工作。这样的想法是荒谬的。“产品负责人先生，我现在需要打一个分号，您同意吗？”
> 产品负责人决定特性的优先级。不批准工作。2018 年 8 月 01 日下午 12:57[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1024640337854324738)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1024640337854324738)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1024640337854324738)

<center>**Now I would like to read your opinions about this topic :)**</center>
# 我们能从关于 Ember.js 的+50000 条推文中学到什么

> 原文：<https://dev.to/kennethlarsen/what-can-we-learn-from-50000-tweets-about-emberjs-2k3c>

<figure>[![](img/9b1ee1a77a0aa7307ba1c9d9180437b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eBFVvxPo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdiKuoCb0vjKUjXMrrKixXQ.jpeg) 

<figcaption>照片由[freestocks.org](https://unsplash.com/photos/HAIPJ8PyeL8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[下](https://unsplash.com/search/photos/twitter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

#### 你做过什么？！

我有个想法。我想对开源社区及其活动有一个总体的了解。第一个自然的地方是 Twitter，因为很多开发者在这个平台上分享和讨论。我想开一个我认为特别有趣的地方:Ember.js。

所以，我决定收集每一条包含#EmberJS 标签的推文。这里面什么都有(嗯，除了已经删除的)。然后我在 Kibana 上使用 Elasticsearch，开始寻找趋势和有趣的数据。

数据库中的第一条推文——似乎也是关于 Ember.js 的第一条推文是:

液体错误:内部

现在，可能有更早的推文，但它们已经被删除了。数据库中最新的推文是这样的:

液体错误:内部

给出的时间范围只有 7 年左右。

这篇文章的目的是通过观察 7 年来的 tweet 活动来获得 Ember.js 社区的概况。总共有 51866 条推文。这是本系列的第一篇文章，但我打算继续关注其他开源社区。

#### 最活跃用户

了解一个社区的一个方法是调查这个社区中最活跃的人。所以，我创建了十大使用 Ember.js 标签的 Twitter 用户。

**十大推文数量**

1.  [@ tarasm](https://dev.to/tarasm)(16280 条推文)
2.  @ michaelnorth(10720 条推文)
3.  @ ember watch(8163 条推文)
4.  @ ember a2 d(8021 条推文)
5.  [@ lady leet](https://dev.to/ladyleet)(6542 条推文)
6.  @ JavaScript flux(4817 条推文)
7.  @ dr _ Vitus _ zato(4442 条推文)
8.  @ ember weekly(3848 条推文)
9.  @ tech wars _ io(3486 条推文)
10.  @ JavaScript kicks(3270 条推文)

这个列表中有趣的一点是，我们看到像@JavaScriptKicks 和@Techwars_io 这样的账户。如果我们看一下他们写的东西，很快就会发现他们只是试图利用 Ember.js 浪潮的垃圾邮件机器。例如，@Techwars_io 已经写了下面这条推文 2.265 次:

> 我们比较了#batmanjs 和# ember js——见结果:[http://www.techwars.io/fight/batmanjs/emberjs/](http://www.techwars.io/fight/batmanjs/emberjs/)

这显然是垃圾邮件，它们在 Ember.js 社区的垃圾邮件列表中赢得了第一名。

值得庆幸的是，其他 Twitter 用户并不像这样可怕。最活跃的用户似乎也是像@EmberWatch 和@EmberWeekly 这样的社区个人资料，尽管最活跃的用户是实际的个人资料。

有趣的是，官方的 Ember.js 帐户没有出现在这个有意义的列表中。此外，这前 10 名似乎表明大多数核心团队主要活跃在#EmberJS 社区之外的其他地方。这可能是论坛或懈怠。也许他们的时间花在那里更好？

#### 是什么让一条余烬推文流行起来？

点赞和转发是衡量成功的普遍标准。尽管我同意它能告诉我们一些关于流行的东西，但它并不总是能告诉我们什么对一个社区有价值。话虽如此，我仍然认为看看是什么让 Ember.js 的推文流行起来很有趣。

**top 5 retweet**

1.  @ baaz:[https://twitter.com/baaz/status/649587166247579648](https://twitter.com/baaz/status/649587166247579648)(202 转)
2.  [@ pocztarski](https://dev.to/pocztarski):【https://twitter.com/pocztarski/status/537078929916694528】T2(177 条转发)
3.  @ michaelnorth:[https://twitter.com/michaellnorth/status/593564323865174016](https://twitter.com/michaellnorth/status/593564323865174016)(165 条转发)
4.  @ baaz:[https://twitter.com/baaz/status/568136988093767681](https://twitter.com/baaz/status/568136988093767681)(132 条转发)
5.  @ Evontech:[https://twitter.com/Evontech/status/824130617776504832](https://twitter.com/Evontech/status/824130617776504832)(118 条转发)

这个列表有一些有趣的地方。首先，似乎最受欢迎的 Ember 推文的转发量仍然很低。与#reactjs 之类的东西相比，这确实是低端的。这是为什么呢？

有可能是 Ember.js 更小，因此转发量也更小。Ember.js 也有其他活跃的交流渠道，如 Slack，那里有更多的活动，可能会导致 Twitter 不那么活跃。

另一个原因可能是 Ember.js 不太擅长炒作自己。如果你看一下 Dan Abramov 的这条推文，他说你可以在没有 npm 的情况下使用 React.js，你会看到它的转发量是排名第一的 Ember.js 推文的两倍。如此简单，如此炒作的东西。

> ![unknown tweet media content](img/76e5ca2ee1d70cbfe97466b2f89027e8.png)![Dan Abramov profile image](img/8410abe414a98a3109390b1a1f7377ee.png)丹阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)你知道吗:不用 npm 就可以用 React 作为<脚本>标签！🤔这里有个方便的指南:[reactjs.org/docs/add-react…](https://t.co/gJwqp3ADJf)2018 年 6 月 27 日下午 15:32[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1011995700748521477)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1011995700748521477)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1011995700748521477)

为什么 Ember.js 不能这样自我炒作？首先，Ember.js 已经有很多令人惊叹的功能，但它们并不都是新的。他们就在那里。他们工作了。他们在那里已经有一段时间了。当有新的和伟大的东西出现时，它就从宣传系统中溜走了。

这意味着 Ember.js *可以*开始重塑现有功能，以获得 React.js 从中受益的宣传。这也意味着来自 Ember.js 的**未来**通信可以用类似 React.js 的方式表达

但也许根本不重要？炒作到底值不值？Ember.js 的一大好处就是它的一致性、常规性和稳定性。这也是我在阅读社区推文时的感受。当我阅读 React.js 的推文时，我有一种完全不同的感觉，也许这两个框架在 Twitter 上找到了它们不同的家，这一切都很好。我所知道的是，当我和开发人员谈论 Ember.js 时，他们认为它已经死了。这主要是因为缺乏在线存在。

如果我们回头看看转发量，同样值得注意的是，前五条推文中有两条是@baaz 关于同一主题的。他确实在 Ember.js 上写了一本**很棒的**书，这本书获得最多转发的事实可能暗示了关于 Ember.js 社区的一件重要事情。

one book 资源获得所有转发的事实可能表明 Ember.js 缺乏好的学习资源，因此当最终有一个好的资源时，它会被大量分享。

**最受欢迎的五条推文**

1.  @推手[https://twitter.com/pusher/status/839052252417560576](https://twitter.com/pusher/status/839052252417560576)(354 赞)
2.  @ Luke deniston[https://twitter.com/lukedeniston/status/876873369970290688](https://twitter.com/lukedeniston/status/876873369970290688)(154 赞)
3.  [@ Jeffrey biles](https://dev.to/jeffreybiles)【https://twitter.com/JeffreyBiles/status/791664130490937344】T2(135 个赞)
4.  [@ remi](https://dev.to/remi)【https://twitter.com/remi/status/984851429906178048】T2(131 赞)
5.  @ rwj blue[https://twitter.com/rwjblue/status/799722020745441281](https://twitter.com/rwjblue/status/799722020745441281)(123 赞)

在这里，最热门的推文是关于社区感兴趣的功能和谈话。但是这里最热门的推文仍然只有 354 个赞，这与 React.js 社区中的推文相比不算什么。原因可能与转发相同。

#### 忙于发微博

到目前为止，在 Twitter 上分享关于 Ember.js 的新闻似乎还不多。那么讨论呢？如果我们看看回复最多的推文，我们可能会有点失望:

**回复数前 5 名**

1.  @约阿希姆:[https://twitter.com/joachimhs/status/376046831660068864](https://twitter.com/joachimhs/status/376046831660068864)(34 条回复)
2.  @ ryantoweets:[https://twitter.com/ryantotweets/status/948228827230228480](https://twitter.com/ryantotweets/status/948228827230228480)(22 条回复)
3.  @ michaeldegli:[https://twitter.com/michaeldegli/status/458365260009705472](https://twitter.com/michaeldegli/status/458365260009705472)(20 条回复)
4.  [@塔拉斯姆](https://dev.to/tarasm):【https://twitter.com/tarasm/status/906935258909630464】T2(18 条回复)
5.  @ ebryn:[https://twitter.com/ebryn/status/463778352021508096](https://twitter.com/ebryn/status/463778352021508096)(16 条回复)

这些数字也偏低，这表明关于 Ember.js 的实际讨论可能正在其他地方进行。回复最多的推文只有 34 个回复，而且是关于获得免费电子书的，这个事实说明了一切。

#### 那么……它死了吗？

当然不是。尽管 Twitter 活动并不能说明一个社区的一切，但我确实觉得它提供了一个很好的视角来理解为什么很多人认为 Ember.js 已经死了。大多数活动和参与都发生在特定的 Ember.js 社区，如论坛或非常活跃的 Slack 频道。但这意味着，只有当你已经是社区的一员时，你才能看到它。

如果我是 React.js 的开发者，我很可能不会在 Twitter 上看到 Ember.js 的转发、赞或讨论。所以我为什么要认为它还活着？我个人不使用 React.js，但我在我的时间线中一直看到 React.js 的推文。

在 Twitter 上创建一个更活跃的 Ember.js 社区可能不值得，但它至少解释了其他社区是如何看待 Ember.js 的

想看更多这样的帖子？在 [Twitter](https://twitter.com/kennethlarsen) 上关注我。
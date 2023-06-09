# O11yCon 2018:说明和观察

> 原文：<https://dev.to/dangolant/o11ycon-2018-notes-and-observations-4nbf>

# O11yCon 2018:笔记与观察

两周前，我有幸参加了由[蜂巢](https://www.honeycomb.io/)举办的首届 [o11ycon](http://o11ycon.io) ，这是一个聚焦于“可观察性”的会议。在网上，你会发现“可观察性”实际上是什么的各种定义，有些人本质上称之为监控的下一步，o11ycon 的大多数演讲者都选择了[控制理论定义](https://en.wikipedia.org/wiki/Observability)，但本质上所有的定义都在某种程度上有助于对系统的运行状态提出新的问题，而无需部署更多的代码。当讨论可观测性时，重要的是要记住可观测性是一个相对的质量，系统或多或少是可观测的，而不是正确与不正确的二元状态。在 ops 领域，可观察性是一个相对较新的概念，所以当我看到有机会查看 o11ycon 并向一些领导者学习这一新兴学科时，我跳了起来。

<figure>

[![some of the stickers at o11ycon](img/6c74c224737234ccf72e3c494f330a78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y3feBR1W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oosyob1kkhrtlb8v6zhn.jpg)

<figcaption>Come for the talks, stay for the stickers</figcaption>

</figure>

以下是(非常粗略的)按时间顺序排列的当天事件，以及我对所举行的会谈和讨论的观察和思考。我尽了最大努力为我当天的笔记提供一些结构，但我不想为如何适应拥挤的一天而苦恼，这一天涵盖了从现场调试 AWS 成本高峰的谈话到建立信任文化的小组讨论，我想我会让这篇文章作为对所涵盖主题的调查，一旦谈话记录发布，读者可以通过视频进行补充。虽然我尽最大努力记录了我认为当天有见地的部分，但 o11ycon 的一个关键亮点是自由形式的“开放空间风格讨论”分组讨论，这些讨论没有记录，也很难转录。我无法用文字很好地表达这些团体的动态，所以我想说的是，如果你有机会参加下一届 o11ycon，你一定要去，特别是 OSSD 团体。

## **慈善专业**

[![charity speaking](img/20d4ac9363fbf6d75cc28bcb1d658567.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_ix_opdm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4oa55fjzabmc9cg4cm0s.jpg)

我们与 Honeycomb 的联合创始人兼首席执行官 Charity Majors 一起开始了这一天。如果你熟悉 Twitter 上的 [Charity](https://www.twitter.com/mipsytipsy) ，你就会知道，我说她(可能)在舞台上的 15 分钟充满了智慧和笑声，一点也不夸张。

她谈到了如何向前发展，可观察性必须成为软件工程师的目标，以便保持他们自己的理智。工程师们不断被要求做各种各样的工作，身兼数职，无论是前端、后端、运营还是设计，但他们的工资和工作时间都是一样的。随着我们的系统变得更加分布式和更加复杂，开发人员花费在调试一次性生产问题上的时间可能会增长到超过实际的生产性开发时间，除非我们提升我们的工具和仪器来迎接挑战。此外，由于系统变得越来越复杂，我们面临的故障类型将主要存在于错误分布的狭长尾部，这意味着依靠测试和 TDD 无法拯救我们。测试这些类型的 bug*的成本已经*盖过了这些问题中任何一个单独的成本，并且没有测试可以足够抽象以有效地捕捉它们。

Charity 还提到了这样一个事实，即可观察性，甚至在某些情况下对生产的初步可见性，仍然是一个难以置信的新概念。她的希望是，新将使它成为生产软件操作中新思想的一个很好的切入点，摆脱现有方法随着时间的推移而积累的正统观念。第一个演示的关键“棍子”是，为了让开发团队在系统变得更加分散和复杂时保持领先，开发人员必须学会构建可操作的软件。另一方面，关键的“胡萝卜”是，可观测系统将工程师从盲目开发的苦差事中解放出来，甚至使我们能够基于我们可以获得的更深层次的洞察力提出商业目标。在 2018 年，当你甚至对自己的业务都没有可见性的时候，假装有“远见”，是一个失败的赌注。

最喜欢的名言:

> 现在是 2018 年，如果你挤进一个盒子里，你已经把自己搞砸了。

# **克里斯蒂娜·斯潘，《为什么 O11y 很重要》**

<figure>

[![Christine Spang speaking](img/204b82bbc17d06203dd68162cb285040.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1dBJ8vxU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ie8oajb19f5qb5b0kl3p.jpg)

<figcaption>Some of Nylas' Honeycomb graphs</figcaption>

</figure>

接下来是[克里斯汀·斯潘](http://twitter.com/@spang),[尼拉斯](https://www.nylas.com/)的创始人和最近的[发展到](http://dev.to) [AMA ***明星***](https://dev.to/spang/hi-im-christine-and-i-started-contributing-to-debian-when-i-was-15-now-im-the-cto-of-nylas-ask-me-anything-2hh0/comments) 。Spang 很快从架构的角度回顾了行业的现状，向观众简要介绍了从单片、灯堆栈式应用程序到今天更加去耦、面向服务的事务的转变。这一快速历史回顾中最有趣的一点是 Spang 快速强调的，尽管术语*微服务*已经主导了行业*，*我们中的许多人并没有运行真正的“微服务”，并且*分布式*系统并不一定是基于*微服务的系统*。这种区分的目的是澄清，即使你的服务可能比你承认的要稍微厚一点，即使你在描述你的架构时引用了“微服务”，改进的可观察性在生产中运行时可以改变游戏规则，因为它是你的开发团队的反孤岛和包容性的工具。这是因为更高水平的可观测性:

1.  让所有开发人员获得与团队中资历最老的人相似的洞察力
2.  减轻这些老兵的记忆和文档负担
3.  向更大的组织开放对用户行为的现场提问

斯潘演讲的下一部分集中在实现更多可观测系统的实际步骤上。她建议的第一步是 **[转向结构化日志方法](https://www.kartar.net/2015/12/structured-logging/)** ，鉴于在整个组织中强制实施一种日志风格的挑战，这是那天剩下的一些辩论的主题。我喜欢 O11ycon 的每一次演讲，因为他们强调了一种合理的、渐进的、数据驱动的方法来改进系统。用这种新的方式构建它，试着运行它，然后*测量你比你的旧代码*快乐多少。在这种情况下，Spang 的第二个低层次建议是将给定代码路径中的所有事件附加到给定请求的上下文属性；这一方法得到了当天大多数发言者的赞同。如果您像 Nylas 一样使用 Flask，并且可以访问全局请求对象，那么这种方法非常有效。那天晚些时候，我提到，在我的 Rails 代码库中，我没有访问这样一个全局变量的权限(据我所知)，对此 Honeycomb 的[本·哈特向](https://twitter.com/maplebed)有一个致命的技巧，使用 Ruby 的线程本地变量(可通过`Thread.current[:var]`访问)作为事件存储。每当一个请求越过某个自定义的阈值时，您可以将该执行的整个上下文存储在这些记录存储中，然后在以后轻松地转储和采样这些记录。采用这种更容易观察到的方法，Nylas 可以开始查看他们遇到的一些失败的请求，基于哪个表达式会中断，然后当他们注意到收件箱同步出问题时，可以一直深入到有问题的数据库碎片。从 Nylas 的可观察性方法中一个很好的收获是，他们现在正试图使用它来推动技术上的入门和架构知识，因为他们的新开发人员在审查架构选择时能够很容易地询问什么系统实际上在做什么。

最喜欢的名言:

> 默认情况下，软件是不透明的

 *# 分组会议

可能 O11ycon 最酷的部分是我之前提到的“开放空间风格的讨论”。从本质上讲，这些是围绕与会者众包主题的分组讨论。与大多数分组会议的关键区别在于，我们被鼓励随意加入和离开小组，这是基于我们是否认为我们正在从中获得*或者为正在进行的对话贡献*价值*。这基本上是 20 分钟的头脑风暴问题，并将其分组为主题，然后是一个小时的讨论，如果你对会议不感兴趣，你可以随时离开，找到一个你更喜欢的。*

<figure>

[![postit session](img/c6264f502386f8b1f05e78f89706db4c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--phXmVQrH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d0mw88zblwbh4nj864xh.png)

<figcaption>Attendees, including myself, submitting questions for discussion</figcaption>

</figure>

## 分组会议#1，无服务器和 O11y

我决定加入一个讨论组，讨论如何在重度或完全无服务器系统中实现可观测性。在我加入“从可观察性开始”的小组之前，SLS 人群中的大部分讨论都集中在无服务器方法固有的不透明性上。看起来这种不透明主要是由于供应商方面的决策，但我也有一种感觉，这种类型的问题是平台所固有的，其关键目标是尽可能少地了解执行环境。对我来说，在选择无服务器平台时，开放资源利用率的可见性和嘈杂邻居的存在似乎是供应商的附加值，但我可以理解为什么亚马逊和谷歌等公司不想提供这样的东西，因为这也可能导致更严格的 SLA 执行和更高的运营成本。有趣的是，就在我加入第一批的第二个讨论组时，“亚马逊的 SQS(简单队列服务)几乎是它能得到的最黑的盒子，他们可能不会打开它。”是我听到的第一件事，这让我怀疑在不久的将来我们是否会在无服务器平台上看到更多的工具。

# 彼得·阿尔瓦罗博士《专家的黄昏》

<figure>

[![Dr. Alvaro](img/6241920ae6d195837f92d6c66dedeeaa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h5acUKAM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p1v9ir8r6v4fkwwp33rc.jpg)

<figcaption>Dr. Alvaro dropping knowledge</figcaption>

</figure>

我真的不觉得我能做好接下来的演讲，所以我决定把它单独贴出来，让这篇文章更短一些。你可以在这里找到我的文章，当它出来的时候，我会用一个链接来更新这篇文章。

最喜欢的名言:

> 如果你从今天的演讲中有任何收获，前三个应该是:
> 
> *   可组合性是最后一个难题
> *   牧师也是人
> *   我们可以自动化专家的特殊天赋

# 艾米莉·中岛，仪表化浏览器

Emily 的演讲，我将再次鼓励你们在 o11ycon.io 上收听，在我看来，最好的总结是可观察性最终实现了前端的“分析承诺”。我认为，当大多数新开发人员听说前端分析时，他们会假设数据可以操作到很少实现的粒度级别，而没有意识到大多数时候它对业务利益相关者比任何构建产品的人都更有用。Emily 作为蜂巢同事 Rachel Fong 的嘉宾，带领观众通过蜂巢前端的仪器，并描述了他们解决的几个问题。他们没有过多地探究细节，而是强调了传统仪器应用缺乏遥测技术。因为这篇文章本质上是我笔记的总结，所以我将只详细说明我的要点:

*   如果你不使用工具，你就不能设定目标，因为除了“坏”之外，你对当前状态一无所知，也不知道“好”是什么样子
*   如果你不把基础设施速度传递给你的用户，它就没有意义了
*   即使您不使用 Honeycomb，您也可以通过在发送日志的任何地方发送事件来开始事件发射，这至少应该能够处理某种级别的多维查询，或者您的错误或监控服务。
*   Rachel 分享了一个她正在推动的解决方案的故事，只有在意识到她没有事先建立问题和成功指标后，她才意识到这个解决方案被过度设计了

最喜欢的名言:

> “如果我们对问题没有足够的了解，我们能系统地构建解决方案吗？”

# 慈善 Pt。2 英尺。Joe Beda，首席技术官@ Heptio

[![Charity & Joe](img/fd315bec19641ef49a1f015b6ef213c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WQ9j8Lcd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c5mbrauiszmfw22ckoh7.jpg) 
至此我放弃了:D 的任何一种叙事结构

这些是我从这次演讲中得到的主要收获:

*   提高效率的关键不仅仅是改变的力量，还有对错误和组织现状的洞察力。你可以拥有世界上所有的力量，却不知道如何有效地带来改变。
*   这些工具不是万灵药，让传统上不随叫随到的工程师参与进来会有伤害，但是你的寻呼率应该会下降并稳定下来
*   您无法预测产量，这意味着您无法主动监控或测试一切
*   Kubernetes 在无服务器和运营您自己的基础设施等解决方案之间提供了一座坚实的桥梁，这与从 Heroku 过渡到 VM 的旧解决方案相反，在旧解决方案中，运营应用程序所需的知识差异要大得多
*   添加监控和其他种类的反应管道是编写不移动底线的软件，而编写不移动底线的软件在小范围内是糟糕的业务
*   作为一家初创公司，你最多可以获得 2 个“创新令牌”，在那里你可以发明一些新的东西来推动你的业务，除此之外，还有可持续的执行
*   “永远不要尝试构建或设计超过现在 10 倍的负载”

最喜欢的名言:

> 消灭苦差事，
> 释放人的潜能，
> 尽量减少“不得不做”的事情

# 分组会议#2，将文化转化为可观察性

老实说，在我的第二次分组会议上，我没有做笔记，但这是一次令人惊叹的讨论，产生了以下引文:

> ![hideous kojima profile image](img/d68e67054d0b1989d8d6101c5b3b102e.png)暗害小岛[@ dango lant](https://dev.to/dangolant)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)“可观察性使科学-方法驱动开发成为可能”[@ o11 ycon](https://twitter.com/o11ycon)2018 年 8 月 22 日 22 点 31 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1025147082120650752)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1025147082120650752)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1025147082120650752)

本质上，我们谈到了开发和运营如何对管理层不愿意投资或允许投资工具感到失望，但这种感觉也以另一种方式运行，如果他们看不到商业价值，管理层就不愿投资，因此开发人员需要平衡自下而上的改进与游说自上而下的支持，并向业务角色推销可观察性工具的价值。在我看来，这归结为开发人员需要像 Tableau 这样的商业智能工具对于产品经理、分析师和销售人员一样强大的工具。这几乎没有捕捉到小组在一个美丽的阳光明媚的屋顶上的谈话，所以....明年去 O11ycon:)。

# 分组会议回顾演示

为了结束这一天，几位演讲者走上前来回顾他们分组讨论的内容。以下是我的主要收获:

## 第 1 组:生产中的测试

*   发布和部署只是测试你的产品的另一个步骤，除了生产，没有什么能正确地反映生产条件
*   从发布中分离部署

## 第二组:让观察变得更容易

*   经验法则:观察“服务”边界

## 第三组:将可观察性与业务目标联系起来

*   使用与推动技术目标相同的数据来推动业务目标，展示更精细数据的价值
*   围绕您自己的(开发/运营)用例以及业务合作伙伴的用例创建客户案例

## 第四组:可观察性驱动开发

*   可观察性减少了开发过程中的痛苦和不确定的焦虑，因为足够水平的可观察性减少了对生产环境的未知
*   是否应该有一个可观察性等级的尺度或指数？
*   dev 如何平衡数据和查询的及时性与数据量，我们以什么样的速率采样？
*   可观察系统建立了一个抵御“警报疲劳”的壁垒，当你包含长尾事件的背景时，你可以监控的更少

> ![hideous kojima profile image](img/d68e67054d0b1989d8d6101c5b3b102e.png)暗害小岛[@ dango lant](https://dev.to/dangolant)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)大外卖来自 [@o11ycon](https://twitter.com/o11ycon) :可观察系统缩小“神经编码”00:09AM-03 2018 年 8 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1025171643117780992)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1025171643117780992)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1025171643117780992)

就这些了，这些是我在 O11ycon 的笔记和心得，有点充实，有点笨拙的解释，但至少写在纸上了。我会试着写得更频繁(我每次都这么说，但也许这次我是认真的)，所以如果你对我写的一些更仔细的东西感兴趣，请随意点击那个`Follow`按钮，在 [Twitter](https://twitter.com/dangolant) 上关注我。*
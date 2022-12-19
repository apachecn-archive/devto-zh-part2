# 2018 年中期选举纽约国会选区的选民登记

> 原文：<https://dev.to/thedatasleuth/voter-registration-by-new-york-congressional-districts-for-2018-midterm-elections-990>

*这篇文章最初发表在[数据侦探](https://thedatasleuth.github.io/python/2018/09/12/2018-Midterm-Election-New-York-Congressional-Districts.html)上。*

为了迎接 2018 年中期选举， [Enigma Public](https://public.enigma.com/) 正在举办一场数据科学竞赛。使用他们新的 [SDK 包](https://pypi.org/project/enigma-sdk/)，可以方便 API 调用他们的公共数据库，参赛者被鼓励探索对中期选举有一定解释力的话题。比赛于 2018 年 9 月 21 日星期五结束。

在他们的[选举](https://public.enigma.com/browse/tag/elections/34)集合下，有六个伞式数据集:国会候选人支出、联邦选举委员会、纽约州选举委员会、PAC 摘要、总统竞选支出和 Tafra。虽然这些数据集中肯定有一些值得挖掘的东西，但我选择探索纽约州选举委员会的数据集，其中包含了按国会选区划分的 5 年选民登记统计数据。

我最初开始在 Matplotlib 中绘制这个分析图，但中途决定挑战自己，用 Dash 构建我的图。我想这样做有几个原因:首先，我已经在[之前的项目](https://thedatasleuth.github.io/predictive-modeling/2018/05/21/Predicting-Titanic-Survivability-Odds-Using-Machine-Learning.html)中使用了 [Plotly](https://plot.ly/#/) ，并且对交互式悬停功能和色彩的清晰印象深刻。使用 [Plotly-Dash](https://plot.ly/products/dash/) ，我能够通过包含下拉菜单来促进更集中的分析，从而将用户交互提升到一个新的水平。第二，我觉得建立像下面这样的互动图鼓励参与数据，而不是仅仅阅读某人的发现。我想不出比选举季节更好的话题来让人们参与数据并思考他们的投票权。还有，我内心的孩子真的很喜欢按按钮。

点击下面的图表，在评论区分享你的发现。

### 2014-2018 年各政党选民登记情况

下图显示了纽约所有 27 个区的选民登记号码，可以使用下拉菜单按年份进行筛选。单击图例中方框的任意组合，比较不同政党的选民。这里有几个问题让你开始你的分析:民主党有没有输给共和党任何选区？如果有，在哪里，什么时候？除了民主党和共和党，最大的党派是什么？这种政治倾向代表机会还是冷漠？独立党在哪里登记的选民最多？你还能看到什么其他趋势？

[https://ny-districts-by-year.herokuapp.com/](https://ny-districts-by-year.herokuapp.com/)

### 选民登记百分比变化

有时仅仅看数字本身是不够的，还需要实际计算这些数字每年是如何变化的。使用 Pandas 中的`.pct_change()`,我能够识别出各政党选民登记的百分比变化。这项分析产生了一些有趣的结果，特别是当它涉及到纽约的边缘政党，即妇女平等党和改革党时。

女性平等党(WEP)成立于 2014 年，由现任纽约州州长安德鲁·科莫(Andrew Cuomo)创建。在 2014 年的州长选举中，该党获得了所需的 50 000 张选票，成为正式政党。虽然科莫在理论上不再控制该党，但该党仍然深深支持现任州长，支持他最近在 2018 年竞选连任。

改革党(REF)是另一个成立于 2014 年的边缘政党，是由[柯蒂斯·斯利瓦](https://www.nyreformparty.com/endorsements)创建的有投票权的政党。Silwa 最初于 1979 年成立了另一个名为“守护天使”的组织，并定期为包括 Fox 5 在内的几个电台和电视脱口秀节目撰稿。

点击下面的图表，查看每个地区对这些新政党的介绍的反应。你有没有看到民主党和共和党代表的选区之间出现了什么模式？

[https://ny-congressional-districts.herokuapp.com/](https://ny-congressional-districts.herokuapp.com/)

### 2018 年活跃与不活跃选民

然而，统计登记选民的人数并不等于实际投票的人数。以 2016 年总统选举为例，[合格选民](https://en.wikipedia.org/wiki/Voter_turnout)的 [58%](https://www.pbs.org/newshour/politics/voter-turnout-2016-elections) 投了票。那么你怎么知道有人会不会投票呢？确定潜在投票率的一个方法是看一看活跃和[不活跃的选民登记](http://vote.nyc.ny.us/html/voters/faq.shtml) <sup id="fnref1">[1](#fn1)</sup> 。活跃的选民比不活跃的选民更有可能参加投票。

点击下面的图表，查看 2018 年各政党在所有地区的活跃和不活跃选民的细分情况。有没有一个政党在所有地区都很活跃？哪个党最不活跃？民主党和共和党谁更活跃？回想一下边缘政党的百分比变化图，你认为新成立的边缘政党拥有不活跃的选民意味着什么？

[https://active-inactive-ny-voters2.herokuapp.com/](https://active-inactive-ny-voters2.herokuapp.com/)

### 最后的想法

2018 年中期选举意义重大，原因有很多。共和党人保持对参议院和众议院的控制意味着他们将能够继续推动更保守的议程，可能包括试图再次废除奥巴马医改，削减福利、医疗保险和社会保障等社会服务的预算，以及限制堕胎。此外，由于许多州长和州议会选举决定了州选区的划分，无论谁当选都将有助于在即将到来的 2020 年人口普查中划定国会选区的边界，而这些边界将在接下来的十年中有效。这里有一张由华盛顿邮报制作的互动地图，详细说明了谁来决定每个州国会选区的划分。

2018 年的许多中期选举将在 2018 年 11 月 6 日星期二**举行。美国众议院的所有 435 个席位和美国参议院 100 个席位中的 35 个席位将进行角逐。 <sup id="fnref2">[2](#fn2)</sup> 早投常投。**

* * *

1.  在连续两次联邦选举中没有投票的处于非活跃状态的选民，在第五年将被从选民名册中除名。选民必须重新登记才能投票。 [↩](#fnref1)

2.  [https://en.wikipedia.org/wiki/United_States_elections,_2018](https://en.wikipedia.org/wiki/United_States_elections,_2018)T2】↩
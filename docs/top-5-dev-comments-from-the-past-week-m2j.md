# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-m2j>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

为什么 git 上的分支是错误的文章跨越了 154 条评论，是真正的谈话和辩论的闪电。 [@nezteb](https://dev.to/nezteb) 以一个似乎被许多人分享的观点获得了第一名:

[![nezteb profile image](img/dd0832ff5b853cb3a5390c914b338006.png) ](/nezteb) [ Noah Betzen ](/nezteb) • [<time datetime="2018-10-27T16:45:29Z" class="date-short-year"> Oct 27 '18 </time> • Edited on <time datetime="2018-10-27T16:46:17Z" class="hidden m:inline-block date-no-year">Oct 27</time>](https://dev.to/nezteb/comment/6d31) 

我不同意这篇文章的大部分内容，但尊重它对你有用。

我曾经工作过的任何一个项目，如果多人都致力于同一个通用的“开发”分支，就会导致大量的合并冲突，除非人们非常小心地总是提取最新的变更并经常合并主项目。这对于将来的审计来说也很困难，因为每次 PR 合并都会同时添加许多功能。

如果有人决定尝试这样做，我强烈建议您保留一个变更日志，并遵循某种提交消息指南。

*   [keepachangelog.com/](https://keepachangelog.com/)
*   [chris.beams.io/posts/git-commit/#s...](https://chris.beams.io/posts/git-commit/#seven-rules)

根据我的经验，适当地使用 Git 流、CI/CD 工具、PR 状态检查以及必要的合并冲突解决方案，应该可以解决您在特性分支方面遇到的大部分问题。

一些有用的 Git 工具:

*   [github.com/github/hub](https://github.com/github/hub)
*   [github.com/tj/git-extras](https://github.com/tj/git-extras)
*   [github.com/Originate/git-town](https://github.com/Originate/git-town)

[@sergio](https://dev.to/sergio) 在**分享一个故事[你想要多少年的经验？！](https://dev.to/jsrn/you-want-how-many-years-experience-4jl4)** 跟帖，谈一个咨询师面试的有趣转折:

[![sergio profile image](img/faa19b25655a930432e55bb71b7a3029.png) ](/sergio) [ deleteme deleteme ](/sergio) • [<time datetime="2018-10-25T14:57:54Z" class="date-short-year"> Oct 25 '18 </time>](https://dev.to/sergio/comment/6bn4) 

让我想起一个同事告诉我的有趣故事。他在面试长生不老药咨询公司的职位；基本上，我们需要有人来审核我们将要做出的某些选择。

我的同事说，他在用长生不老药拷问这个人，他很好。会议结束后，当天晚些时候，他告诉团队所有的事情。

原来他是在拷问长生不老药的创造者何塞·瓦里姆哈哈哈哈哈哈

这是一个我永远不会忘记的故事，我至今还在笑它。

上面的评论是一个名为 **[的](https://dev.to/ben/turns-out-language-creators-are-pretty-good-at-their-language-4ham) [#bestofdev](https://dev.to/t/bestofdev) 帖子的基础。事实证明，语言创造者非常擅长他们的语言** 。这给了 [@tux0r](https://dev.to/tux0r) 一个机会来讲述一个类似的故事:

[![tux0r profile image](img/a4497c77ca9c9507af4e323a1e815599.png) ](/tux0r) [ tux0r ](/tux0r) • [<time datetime="2018-10-26T14:56:14Z" class="date-short-year"> Oct 26 '18 </time>](https://dev.to/tux0r/comment/6ceg) 

这让我想起了一个更古老的故事:

> 1969 年至 1973 年间，肯·汤普森和丹尼斯·里奇一起创建了 Unix。同时他还开发了 C 语言。（...)谷歌雇佣汤普森创造了一种新语言，Go。但谷歌也要求所有新员工通过语言测试。据 Thompson 说，他还没有抽出时间来做这件事，所以不能提交代码。

从语言创造者那里转换话题， [@simonhaisz](https://dev.to/simonhaisz) 进入了 **[为什么你应该重新发明轮子](https://dev.to/quantumsheep/why-you-should-reinvent-the-wheel-4ha2)** 的讨论，围绕经常重复的建议添加一些上下文:

[![simonhaisz profile image](img/1b8cece937611a1961c532c87281d088.png) ](/simonhaisz) [ simonhaisz ](/simonhaisz) • [<time datetime="2018-10-28T03:38:45Z" class="date-short-year"> Oct 28 '18 </time>](https://dev.to/simonhaisz/comment/6d95) 

也许我的经历与你的不同，但通常当我听到/说“不要重新发明轮子”时，不是“不要做 X，它已经完成了”，而是更多的“你试图建立 X，它需要 Y，已经有足够好的 Y 了，为什么不使用它，并把更多的注意力放在让你的 X 变得很棒上”。

如果你想开发一个应用程序，为什么要创建自己的数据库呢？这会分散你构建应用程序的注意力。当然，如果你想建立自己的数据库来学习，或者因为你认为你可以建立一个更好的，或者因为现有的没有一个符合你的确切要求。但是如果你的目标是开发一个应用程序，为什么要专注于此呢？

最后， **[一个组织者的代词按钮指南](https://dev.to/sublimemarch/an-organizers-guide-to-pronoun-buttons-afb)** 提供了宝贵的资源和重要的讨论。 [@bintlopez](https://dev.to/bintlopez) 很好地描述了为所有与会者提供适当住宿的重要性:

[![bintlopez profile image](img/6f0e11738ba2f9e1ef6466c7ea183e2e.png) ](/bintlopez) [ Nicole ](/bintlopez) • [<time datetime="2018-10-25T15:32:58Z" class="date-short-year"> Oct 25 '18 </time>](https://dev.to/bintlopez/comment/6bno) 

会议和活动中的名字和个人关系很重要。名牌和徽章会分散注意力吗？贴纸和赠品会分散注意力吗？

如果说有什么不同的话，那就是在一个活动中不断地被性别或其他人误解会让人超级分心。想象一下，在一个人们一次又一次把你的名字弄错的活动中，会有多让人分心。或者想象一下，如果你报名参加一个活动，而组织者并不关心你是否有饮食限制——如果你发现午餐没有什么可以吃的，然后为了参加活动而抢着吃或饿着肚子吃，那会非常分散注意力。为你接待的人提供便利是策划一个活动的关键部分，在这个活动中，人们可以专注于手头的主题。

还有，如果你不在乎一个人的代词是什么，为什么要抵制使用它们呢？

下周见，更多精彩评论，✌
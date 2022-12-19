# 掌握 git rebase 这个难以捉摸的概念的四个有用指南

> 原文：<https://dev.to/ben/four-helpful-guides-for-grasping-the-ever-elusive-concept-of-git-rebase-36d6>

Git 可能是目前软件开发人员最通用的工具，理解它的细微差别是开发生涯的关键。

> ![unknown tweet media content](img/0fd04679a3cdcac44a6173413f3bc145.png)![The Practical Dev profile image](img/fbf25e6c8920b0aa54c6073846fc627c.png)实用 Dev[@ thepracticaldev](https://dev.to/thepracticaldev)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我知道 Git，就看你说的“知道”是什么意思了。2016 年 8 月 08 日下午 17:58[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=762709515133485056)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=762709515133485056)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=762709515133485056)

你可以通过有限的掌握，但你会想要随着时间的推移而提高。是那些导致许多持续困惑的命令之一，至少可以说这是一个永恒的话题。

我不是一个能恰当地向你解释它的人，但是我想分享一些很棒的[dev to](https://dev.to/)资源，这些资源已经创建了一个非常好的学习图书馆。

[![maxwell_dev](img/e584c820fe4a86bb236ed5cdf1a3fe68.png)](/maxwell_dev) [## 我希望我有 Git Rebase 介绍

### max Anton ucci Sep 24 ' 177min read

#git #beginners #tutorial](/maxwell_dev/the-git-rebase-introduction-i-wish-id-had)[![neshaz](img/12d52d09759dafd5571f20d344bd4cfc.png)](/neshaz) [## Git Merge vs Git Rebase

### 内沙·佐利克 5 月 28 日 18 时 30 分阅读

#git #webdev](/neshaz/git-merge-vs-git-rebase-5134)[![theodesp](img/55f62fc8b323734f2864fcb3a0b1da74.png)](/theodesp) [## 解释 Git Rebase 就像我五岁一样

### theofanis Despoudis 9 月 28 日 171 分钟阅读

#git #explainlikeimfive #discuss](/theodesp/explain-git-rebase-just-like-im-five-7l2)[![gonedark](img/8ac292f3c39b3c830c7a02e5403fff5e.png)](/gonedark) [## git rebase 的详细介绍

### Jason mccrea ry 2 月 21 日 173 分钟阅读

#git](/gonedark/a-closer-look-at-git-rebase)

关于这个主题的内容相当深入，不仅仅是这些帖子，但它们是一个很好的来源。杰森去年在他的 AMA 中聊了一点关于 git rebase [的事情。那篇文章是 git 知识的宝库。](https://dev.to/gonedark/im-a-git-master-ask-me-anything)

[![gonedark profile image](img/f63d026749ea74ffdae82ca60db1bb24.png) ](/gonedark) [ Jason McCreary ](/gonedark) • [<time datetime="2017-09-13T18:57:46Z" class="date-short-year"> Sep 13 '17 </time> • Edited on <time datetime="2017-09-13T23:19:44Z" class="hidden m:inline-block date-no-year">Sep 13</time>](https://dev.to/gonedark/comment/n5g) 

人们回避`rebase`的原因是因为它重写了历史——意味着它改变了承诺。所以如果你和别人分享你的工作，这可能是个问题。

这位顾问很可能是*强迫*的受害者，并且做出了一个彻底的声明，不再使用`rebase`。我们都经历过。令人沮丧。

注意哪些命令，比如`rebase`，可以重写历史，这解决了*问题*。那你就可以适当的使用了。比如我经常用`rebase`。但是我在最后——就在合并之前——这样做，所以我限制了它影响其他开发人员的机会。
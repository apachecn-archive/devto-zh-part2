# 旧的单元测试有用吗？

> 原文：<https://dev.to/cathodion/are-old-unit-tests-useful-1nd4>

不久前，我在 Twitter 上发布了一些关于旧单元测试的想法。与其重述它们，这里是推文:

> ![dustin king 👽🗽 profile image](img/16d3850b4406c65ec1d55f218a72019c.png)达斯汀国王👽🗽[@ catodion](https://dev.to/cathodion)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)如果
> (1)你只能相信你自己写的测试，而
> (2)任何超过 6 个月的代码也可能是别人写的，
> 
> ..现有的测试真的有用吗，或者它们只是使每个功能单元的遗留代码量加倍了吗？2018 年 8 月 27 日下午 16:48[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1034120380007555078)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1034120380007555078)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1034120380007555078)

> ![dustin king 👽🗽 profile image](img/16d3850b4406c65ec1d55f218a72019c.png)达斯汀国王👽🗽[@ catodion](https://dev.to/cathodion)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我确实发现编写单元测试对于编写新代码或者稳定旧代码很有用。但是测试通常是冗长的、重复的，并且比应用程序代码可读性差。2018 年 8 月 27 日下午 16:48[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1034120389318856704)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1034120389318856704)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1034120389318856704)

> ![dustin king 👽🗽 profile image](img/16d3850b4406c65ec1d55f218a72019c.png)达斯汀国王👽🗽[@ cathodion](https://dev.to/cathodion)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)所以也许要么
> (a)所有的单元测试应该在一个月左右之后被抛弃，要么
> (b)应该像对待应用程序本身一样关注测试的可用性和可维护性下午 16:57-2018 年 8 月 27 日[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1034122657078362112)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1034122657078362112)

> ![dustin king 👽🗽 profile image](img/16d3850b4406c65ec1d55f218a72019c.png)达斯汀国王👽🗽[@ catodion](https://dev.to/cathodion)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)或
> (c)这个帖子的作者有信任问题这大概说明了很多2018 年 8 月 27 日下午 16:57[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1034122658097582080)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1034122658097582080)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1034122658097582080)

声明:我不是要你扔掉所有旧的单元测试！

然而，根据我的主观经验，预先存在的测试套件给我带来的麻烦和它们给我带来的麻烦一样多。另一方面，也许他们阻止了我没有意识到的更糟糕的问题。然而，这就是遗留代码的问题:你永远不知道什么是可以改变的。

单元测试经常被吹捧为可执行文档的一种形式。只有当它们是为读者写的(或者至少在事后为他们清理/重构)时才是真的。

您对现有测试套件的体验如何？你遇到过一个真正写得很好的测试套件，实现了“可执行文档”的承诺吗？有更好的方法来处理测试和现有的测试吗？
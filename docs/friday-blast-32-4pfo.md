# 周五爆炸#32

> 原文：<https://dev.to/horia141/friday-blast-32-4pfo>

[微服务谬误(2016)](https://www.infoq.com/news/2016/10/microservices-please-dont?utm_source=infoqEmail&utm_medium=SpecialNL_EditorialContent&utm_campaign=02092017_SpecialNL&forceSponsorshipId=1343)——平常的东西——只有在你确定自己需要的时候才拿来。在项目开始时通常是*而不是*。还有其他方法可以获得模块化的好处。

[多项式和样条的程序员指南(2018)](http://wordsandbuttons.online/programmers_guide_to_polynomials_and_splines.html) -这主要是关于插值和为各种插值任务计算出好的多项式。在统计学/ML 课程中，这种材料有时会与其他回归任务混在一起，所以从更“工程”的角度来看它们，而不考虑概率论，是很有趣的。

[SaaS 创业公司安全 101(2017)](https://github.com/forter/security-101-for-saas-startups/blob/master/readme.md)-给 SaaS 公司的一些安全建议。除了关于加密数据和使用密码散列等的标准建议之外，它还关注公司范围内的问题，如对员工帐户使用 2FA，这很好。

[智能端点，哑管道(2017)](https://bravenewgeek.com/smart-endpoints-dumb-pipes/)——Tyler Treat 的另一篇精彩文章。它是关于分布式系统中关于可靠性和一次性交付的问题。要强调的一点是，这些东西是整个应用程序的属性，而不是特定的基础设施组件。你想要控制的点通常是“端点”，也就是利用你的基础设施的服务。相反，将智能嵌入“中间件”——消息队列、分布式数据库等。将导致(1)糟糕的性能，因为中间件将需要“胖”并处理大量的复杂性。即使这不是必须的。其次，这会给你一种虚假的安全感。因为这些属性是应用程序级别的，所以您真的无法在较低的级别进行保护，也就是说，如果没有应用程序的一些上下文来判断哪些是重复的，哪些不是，就无法对事件进行适当的重复数据消除。构建分布式系统仍然很难。
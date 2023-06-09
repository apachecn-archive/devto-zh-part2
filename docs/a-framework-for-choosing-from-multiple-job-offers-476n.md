# 从多份工作中进行选择的框架。

> 原文：<https://dev.to/imichael/a-framework-for-choosing-from-multiple-job-offers-476n>

“我应该接受哪份工作”是我在各种 Slack 小组中经常看到和听到的问题。答案当然总是个人的和主观的，所以我认为编写软件来帮助以结构化的方式组织我们的想法并消除与多个好选项相关的[决策疲劳](https://en.wikipedia.org/wiki/Decision_fatigue)会很有帮助。

我用 Python 3.7 @[https://github.com/iMerica/job-offer-selector](https://github.com/iMerica/job-offer-selector)发布了一个非常 alpha 化的版本。随意叉或者用它做灵感。

这里的核心思想是:

*   排列对你来说最重要的类别。
*   对这些类别中的公司进行排名。
*   使用加权平均值选择表现最佳的公司。

P.S. Python 3.7 的新数据类模块相当牛逼。这个项目非常依赖他们。
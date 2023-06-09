# 他承诺卖方！😱

> 原文：<https://dev.to/biros/he-commits-vendor--2o62>

昨天，我们公司不同团队的一些开发人员进行了一次有趣的讨论。主题是:*乞讨的意义是什么？*。

> 出售意味着将项目的依赖项与代码一起提交。

事情是这样的，一些开发人员(包括我自己)发现其他一些开发人员提交了 git 存储库中的 vendor 文件夹。我的第一反应是相当轻蔑的，因为我认为这是另一个时代的肮脏做法，当时我们没有依赖管理器。开发者向我们解释说它有很多好处:

*   首先，它允许在 CI 中更快地构建您的应用程序
*   然后，它确保您拥有依赖项的准确版本
*   那么它们中的任何一个都不可能被某种恶意软件依赖注入
*   最后，在构建过程中，您不依赖于网络(或远程依赖库)

这些论点没有一个令我满意，不是说它们不正确，而是我认为它们都可以用一种更干净的方式来解决，例如通过使用缓存、带有审计依赖性的自定义存储库，以及通过直接解决网络问题。

而你，你怎么看？
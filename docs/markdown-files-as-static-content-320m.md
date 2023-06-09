# 将文件标记为静态内容

> 原文：<https://dev.to/nisevi/markdown-files-as-static-content-320m>

如何在你的网站中使用 GitHub 库中的 markdown 文件作为静态内容？

[![](img/69f756fbae480f757e2c6025cfbb175f.png)T2】](https://cdn-images-1.medium.com/max/1024/1*Trx5kyF44KU7WqS8kDRCTA.jpeg)

### 上下文

我们将讨论在 [Agile Ventures](https://www.agileventures.org) 上实现的一个特性，它允许我们使用 markdown 文件作为网站的静态内容。这个想法是在思考如何让开发者在利用 GitHub 生态系统的同时，以更简单的方式为网站内容做出贡献时产生的。

我们用来编辑网站上静态内容的是 [mercury](https://github.com/jejacks0n/mercury) 编辑器。但是这个编辑器的问题是已经不再维护了。我对那个宝石做了几个[改动](https://github.com/AgileVentures/mercury/commits/master)，以便它能和 rails 5 一起工作。我们现在在一个[分叉库](https://github.com/AgileVentures/mercury)中使用定制版本，但那是另一个故事了；).

作为主要目标，我们希望从我们的系统中消除这个编辑器。所以，我们已经开始移除涉及静态页面的功能，稍后，也从 T2 项目中移除。为了实现这个功能，我所做的所有相关代码都可以在[这里](https://github.com/AgileVentures/WebsiteOne/pull/2252)找到。

### 制剂

我们需要从其中提取内容的存储库。目前，我们的静态页面位于这个[存储库](https://github.com/AgileVentures/AgileVentures)中，我们从根路径中提取数据，并且只处理降价文件。

应用程序通过使用 [octokit](https://github.com/octokit/octokit.rb) gem 向 [GitHub API](https://developer.github.com/v3/) 发出请求。因此，我们需要将 *octokit* 宝石放在我们的宝石档案中:

`gem 'octokit', '~\> 4.8'`

并且 [kramdown](https://github.com/gettalong/kramdown) gem 被用于将降价内容转换成 HTML。为了将*克拉姆丹*宝石添加到宝石文件中:

`gem 'kramdown', '~\> 1.16', '\>= 1.16.2'`

### 创建服务

因为我们将使用 *kramdown* gem 将 markdown 文件中的内容转换成 HTML，所以我们需要隔离 gem 行为。为此，我们将创建一个类，作为 *kramdown* 功能的包装器。
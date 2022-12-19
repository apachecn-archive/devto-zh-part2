# 开发到回购摘要

> 原文：<https://dev.to/devteam/devto-repo-recap-heg>

这是一个新的每周帖子，在这里我们回顾了过去一周我们的库的开源活动。有点新，因为我们认为这种类型的帖子比之前的[版本](https://dev.to/devteam/notable-devto-issues-up-for-grabs-4h64)更好。我们将回顾上周，从 10 月 29 日星期一到 11 月 4 日星期日。

# 特性

*   我们现在自动给开源贡献者他们的开发者贡献者徽章！这是一项定期运行的慢性作业。感谢 [@fredplante](https://dev.to/fredplante) 的贡献！[点击此处查看他们的拉动请求。](https://github.com/thepracticaldev/dev.to/pull/1016)

*   我们添加了另一个 Liquid 标签，允许您嵌入 CodeSandbox。这一次归功于 [@ben](https://dev.to/ben) 。[此处拉请求和详情。](https://github.com/thepracticaldev/dev.to/pull/1052)

*   你现在可以为文章创建系列了！从技术上来说，我们已经支持这个功能有一段时间了，但是以前必须由管理员来设置。查看本在下面的更新日志，了解所有细节。您还可以查看相关拉式请求中的所有技术说明[。](https://github.com/thepracticaldev/dev.to/pull/1034)

[![ben](../Images/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## Changelog:创建一系列帖子

### 本哈尔彭 10 月 29 日 182 分钟阅读

#changelog #meta](/ben/changelog-create-series-of-posts-4o63)

# Bug 修复

*   感谢 [@florinionce](https://dev.to/florinionce) 用我们的[播客播放器](https://dev.to/pod)修复了一个 bug。现在，您应该能够正确关闭播放器了。[拉请求此处](https://github.com/thepracticaldev/dev.to/pull/1039)

*   我们之前有一个问题，设置中的[集成页面偶尔会超时。当某人的账户上有大量回购时，这种情况通常会发生。我们现在异步加载请求，小部件现在是一个 Preact 组件。](https://dev.to/settings/integrations)[由我这里拉请求](https://github.com/thepracticaldev/dev.to/pull/986)

*   再次感谢 [@fredplante](https://dev.to/fredplante) 修复了下一个错误！现在，当您尝试访问不匹配的设置选项卡时，我们会正确地发送 404 响应。[在这里找到他们的拉动请求](https://github.com/thepracticaldev/dev.to/pull/1011)

*   对[dependent bot](https://dependabot.com)通过我们的依赖关系处理三个安全漏洞的荣誉奖！

# 新问题/讨论

*   [@flexdinesh](https://dev.to/flexdinesh) 打开了一个为评论添加书签的功能请求。[查看详情，在此插话。](https://github.com/thepracticaldev/dev.to/issues/1038)

这星期到此为止！下周我们会再做一次总结。
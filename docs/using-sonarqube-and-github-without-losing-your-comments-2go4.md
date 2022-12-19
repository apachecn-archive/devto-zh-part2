# 使用 SonarQube 和 GitHub 而不丢失您的评论

> 原文：<https://dev.to/committedsw/using-sonarqube-and-github-without-losing-your-comments-2go4>

[![github stickers cover](../Images/028a6bbc447164f6c0bd73278a480f78.png)T2】](///static/github-stickers-cover-0282251214cc1183d9920d1b0d567d70-e17b4.jpg)

如果你碰巧使用官方的 sonar-github 插件将 sonar 集成到你的 GitHub pull 请求中，你可能会发现该插件倾向于删除任何它认为过时的评论。当用于集成的 Github 帐户也是您的团队成员时，这尤其令人讨厌，导致许多有价值的评论消失！

虽然有一个现有的 PR 来解决这个问题，但对 Github Sonar 插件的支持已经枯竭(Sonar 正在为其付费产品开发[新解决方案)。出于这个原因，我们决定分叉 sonar-github 项目，应用对这个问题的修复。](https://github.com/SonarSource/sonar-github/pull/30#issuecomment-349640582)[我们 fork 的发布在这里](https://github.com/commitd/sonar-github/releases)。
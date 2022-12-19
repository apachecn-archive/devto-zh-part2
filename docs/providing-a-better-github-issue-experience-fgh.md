# 提供更好的 GitHub 问题体验

> 原文：<https://dev.to/dance2die/providing-a-better-github-issue-experience-fgh>

我最近开始参与一个开源项目， [qit](https://qit.cloud/) ，它“[让程序员通过主题](https://github.com/codingblocks/podcast-app#qit-listen-to-programming-podcasts-by-topic)找到并收听播客。

当我着手处理一些问题时，我发现许多问题只有标题，却没有描述如何重现它或者网站应该如何正常运行。

幸运的是，GitHub 提供了一种鼓励用户提交更好的问题报告的方式。

## 😱背景不明的问题

[![issue with unclear description](../Images/115035b47ebb41f173b939ff9da6fb6b.png)T2】](https://github.com/codingblocks/podcast-app/issues/120)

上面的[Joe Zack](https://twitter.com/thejoezack)报告的问题没有提供关于所报告问题的上下文。

我所说的“背景”是指

1.  如何重现错误
2.  预期的行为
3.  环境，如浏览器、版本和
4.  其他(可选)上下文，如截图/视频。

## 😎一个有更好背景的问题

以下是在上述背景下产生的问题。当其他开发人员看到问题时，他们可以重现错误以及如何更容易地修复它。

*无需来回询问记者更多信息。*

[![Issue with better context](../Images/bef6c10993957d2aeff1418a3425c031.png)T2】](https://github.com/codingblocks/podcast-app/issues/146)

*我必须承认，我忘了添加如何重现错误的描述，只有一些网址…*

我不够聪明，无法记住每次创建问题时应该提供什么信息。谢天谢地，GitHub 简化了这个过程。

## 🔧如何创建问题模板

GitHub 为以下问题提供了创建降价模板的方法

1.  错误报告
2.  功能请求
3.  自定义模板

由于 GitHub 文档很容易理解，我就不赘述了。

您可以在模板中添加上下文，以便当用户创建新问题时，他们可以获得有关如何报告问题的预填充信息。

[![Pre-filled issue template](../Images/8cdf016675d03092caeebe38bf4a9f05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FhpA2G-U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/06/bug-report.jpg%3Fresize%3D778%252C697%26ssl%3D1)

在设置了为问题报告者和开发者提供更好体验的模板之后，我报告了这个问题。

*您可以在[关于发货和拉式请求模板](https://help.github.com/articles/about-issue-and-pull-request-templates/)上了解更多关于发货模板的信息。*

## 💪实际问题模板

查看[该视频](https://youtu.be/dT5en5V3B-w)，了解设置模板后问题模板的工作方式。
*由于某种原因，嵌入的 youtube 视频无法工作……*🤔

## 👋离别赠言

我从 [dotnet/cli](https://github.com/dotnet/cli) 项目中找到了问题模板，这是一个巨大的项目(有 200 个贡献者& 9k 个提交)。

令人惊讶的是，大多数问题记者[都遵循模板](https://github.com/dotnet/cli/issues)为项目贡献者提供更好的背景。

[Joe Zack](https://github.com/THEjoezack)已经用模板提供的更好的上下文创建了一个特性请求。

*和大家的开心*😎

[![joes feature request](../Images/9bdfe731897d0a444af8053875deacc3.png)T2】](https://github.com/codingblocks/podcast-app/issues/149)

乔氏窝特征请求

* * *

[qit](https://qit.cloud/) 是一个使用 [ReactJs](https://reactjs.org/) 的[渐进式网络应用](https://developers.google.com/web/progressive-web-apps/) (PWA)，它使用[风格的组件](https://www.styled-components.com/docs/basics)。

而且是相当活跃的项目。

在这个过程中，我们尝试了许多有趣的事情和失败的事情。

如果你想享受开发 PWA 的乐趣，请加入我们的[编码块 Slack 社区](https://www.codingblocks.net/slack/) (#qit)。

如果你有任何问题，请留下评论(或者请在 [Twitter](https://twitter.com/slightedgecoder) 上给我打电话)。

提供更好的 GitHub 问题体验的帖子首先出现在[轻微边缘编码器](https://www.slightedgecoder.com)上。
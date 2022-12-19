# 我们有一个新的测试版编辑器，你可以试试

> 原文：<https://dev.to/devteam/changelog-we-have-a-new-beta-editor-you-can-try-4hl9>

嘿伙计们！我们有一个新的编辑器，你可以开始使用！我们没有计划删除旧的编辑器，但是你现在可以开始使用新的编辑器了！

新的编辑器更容易使用。它保持了我们写文章的精彩的降价+液体标签风格，但删除了“前沿问题”部分，这是令人困惑的(并且以令人沮丧的方式存在固有的错误！)

[![](../Images/8e62dae0bd5268323b3c302d37003b3c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CdDEihjb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/9305d8256596/Image%25202018-10-09%2520at%25201.07.52%2520PM.png)

V2 仍然处于未完成的测试模式，但它很好，应该为大多数用例工作得很好！要切换到新的编辑器，请转到[https://dev.to/settings/misc](https://dev.to/settings/misc)并切换到“v2”。

感谢 [Derek Hopper](https://dev.toderekjhopper) 大力支持这项功能的推出。这部作品可以在[这部 PR](https://github.com/thepracticaldev/dev.to/pull/522) 中体现出来。

如果你对任何代码质量的改进感兴趣，请随意查看这里的代码。

如果你有兴趣以任何方式参与这项功能的开发工作，请在评论中告诉我，我会和你联系的。它主要是预先建造的。

如果你想尝试一个我认为很酷的拉伸功能，请看本期:

# [![GitHub logo](../Images/75095a8afc1e0f207cda715962e75c8d.png) 新编辑器中的 Typeahead 液体标签下拉菜单 #872](https://github.com/thepracticaldev/dev.to/issues/872) 

[![benhalpern avatar](../Images/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2018-10-09T17:23:52Z">Oct 09, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/872)

**您的功能请求是否与某个问题相关？请描述一下。**作为一个用户，可能很难记住编辑器中液体标签的命令到底是什么。我可能会在代码中使用这个特性，我想键入`{%` 并获得一个自动完成选项列表。

编辑器内置在 Preact 中，理想情况下，这可以在没有新的依赖关系的情况下完成，但是如果我们需要使用 [preact-compat](https://github.com/developit/preact-compat) ，我们可以。

我相信编辑器需要被写成一个内容可编辑的 div，我们会监听输入等。

最终，它应该与我在 VSCode 等中使用 typeahead 的方式类似。<g-emoji class="g-emoji" alias="slightly_smiling_face" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f642.png">🙂</g-emoji>

大概是这样的: [![](../Images/a73b46d1dd95a7b0cc24a51d4d4cdf04.png)](https://camo.githubusercontent.com/0a45a52e969406c12196d572fb0de76bc984bd2d/68747470733a2f2f74686570726163746963616c6465762e73332e616d617a6f6e6177732e636f6d2f692f6b6674757933676b3672767439703674676c37302e706e67)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/872)

编码快乐！
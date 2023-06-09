# web Maker——从香草 JS 到 Preact

> 原文：<https://dev.to/chinchang/web-maker---from-vanilla-js-to-preact-16na>

你好。

我带着一个大的 Web Maker 发布回来了。但这一次，它不是关于任何新的功能或修复。它是关于对任何最终用户都不可见但对他们有同等影响的东西——代码库**。在 [93 次提交并添加了 17913 行新代码](https://github.com/chinchang/web-maker/pull/303)、**之后，我已经将 Web Maker 代码从普通 JavaScript 完全迁移到了 [Preact](https://preactjs.com/)** ！🔥**

## 为什么要一个框架，为什么要 Preact？

我开始用普通的 JavaScript 编写 Web Maker，并不是因为我不能使用任何框架，也不是因为我没有时间建立一个框架。我有意识地选择编写普通的 JavaScript，因为我想看看在没有任何外部框架的情况下我能走多远。我很高兴这种方法已经使用了将近两年！[阅读更多关于我最初如何创建 Web Maker](https://medium.freecodecamp.org/web-maker-how-i-built-a-fast-offline-front-end-playground-9fe3629bc86f) 的信息。

但是最近我开始觉得在构建特性的时候，我更关注于编写 UI 粘合代码并维护它。现在我已经有了很多&很多我想要构建的特性，我不想在构建它们的过程中有任何摩擦，这样我就可以非常迅速地将它们发布给用户。因此，有了一个框架。

而选择 Preact 只是因为我和 Angular，Vue，react 合作过。Preact 说它在如此小的文件中工作起来就像 react 一样！我想尝试一下。这是一种很好的感觉，能够用这么小的占地面积如此方便地构建一个库:)和 JSX 一起工作的最好的部分是[更漂亮的](https://prettier.io/)为我格式化它！我很高兴我选择了它。

## 重构

在重构的第一阶段，我已经将所有东西移植到 Preact 组件中。但是仍然有一些手动 DOM 操作发生在可以被移除的组件内部。此外，许多组件可以进一步分解成更小的组件。所有这些都在重构的下一阶段。

我使用简单的 prop 传递和根组件存储来管理数据。我认为我可以用状态管理做得更好，而不用为此添加任何库。让我们看看。

对于 CSS，HTML 中包含了相同的 style.css 文件。没有变化。但我计划将大多数值转换成 CSS 变量，以允许定制和主题化。

与我在开始重构之前的估计相反，代码的总长度并没有下降。经过简单的检查，似乎从框架中移除的 UI 代码受到了库大小 Preact 为 HTML 模板生成的代码(呈现函数)的影响。但是我会在更多的重构之后重新评估。

## 接下来呢？

重构是如此有趣！我意识到比起写代码，我更喜欢重构。😅现在它已经完成了，我非常兴奋，并充满了在未来几周内建立一些真正酷的功能。如果你有任何功能需求或者想问我任何关于移植一个普通 JS 应用到 Preact(或者 react，它是类似的)的问题， [tweet me](https://twitter.com/chinchang457) 或者在这里发表你的评论。

*   Web Maker 是开源的—[https://github.com/chinchang/web-maker](https://github.com/chinchang/web-maker)
*   网络制造商 Slack group—[https://web-maker.slack.com/messages/CA4A8TE3V/](https://web-maker.slack.com/messages/CA4A8TE3V/)
*   报告错误或请求功能—[https://github.com/chinchang/web-maker/issues](https://github.com/chinchang/web-maker/issues)
*   在 twitter 上关注更新和更多信息—[https://twitter.com/webmakerapp](https://twitter.com/webmakerapp)
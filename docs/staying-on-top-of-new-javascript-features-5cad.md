# 掌握新的 Javascript 特性

> 原文：<https://dev.to/vcarl/staying-on-top-of-new-javascript-features-5cad>

(封面照片由安德鲁·查尼拍摄)

借助现代 JS 工具，我们有机会在浏览器或节点中实现 Javascript 特性之前使用它们。然而，并不是所有的功能都是相同的。有些人比其他人更有可能融入语言。有些在被添加之前将经历显著的变化，而有些将被完全删除。如果你在你的应用程序中使用这个特性，这两者都会引入大的重构。但是你如何判断一个特性是否是你应该使用的呢？

TC39 引入了新特性，TC39 是一个负责维护 Javascript 底层规范的委员会。GitHub 上的 [TC39 提案报告](https://github.com/tc39/proposals/)中有一份完整的规范补充提案列表。这是新特性的主要来源*标准位置。提案必须经过一个明确定义的流程，了解更多关于该流程的信息将有助于您保持领先地位。*

 *[TC39 流程文件](https://tc39.github.io/process-document/)定义了提案必须采取的步骤。本文档是每个特性所经历的各个阶段的来源，您可能从 Babel 的各种 stage-X 预设中了解得最清楚。这些阶段被称为“成熟阶段”定义阶段的表格是一个快速阅读的表格，它将为您提供一个给定提案的大量背景信息。

但是“第一阶段”意味着什么呢？如果有一个巴别塔插件，为什么我们不应该使用一个功能？要回答这个问题，让我们来看看不同阶段的含义。

# TC39 成熟阶段

## 阶段 0

全新的提案被称为“稻草人”或“零阶段”提案。阶段 0 的重要性*非常小*。这意味着某人已经正式向委员会提出了一个想法——就是这样。流程文档指出，这个阶段没有限制，也不需要尝试指定行为。TC39 维护着[一个阶段 0 提案](https://github.com/tc39/proposals/blob/master/stage-0-proposals.md)的列表，其中一些已经 4 年没有向标准化进展，还有几个已经[正式放弃](https://github.com/tc39/proposals/blob/master/inactive-proposals.md)。

从长远来看，在生产应用中依赖阶段 0 功能是一场赌博。那么，为什么开发者在过去的一周里下载了 740，000 次 0 阶段巴别塔预设(在撰写本文时)？

[![A download chart for babel-preset-stage-0 showing 740,000 downloads this week](../Images/38fb6ecdeb7f69a0c01b93f4c615f7a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tmUB5DYV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dxggi3a8zemh2y4av34d.PNG)

你在用吗？...你应该害怕吗？

## 阶段 1-3

这些阶段出现在 TC39 提案报告的自述文件中。这些是在整个过程中积极发挥作用的功能。

第 1 和第 2 阶段需要尝试指定所提议的特性的行为，但是实现是探索性的。提案越复杂，从第一阶段到第二阶段再到第三阶段，就越有可能发生变化。如果你依赖一个行为改变的提议，如果你想更新你的构建工具的话，你可能会被一个重要的重写所困扰。

这种变化的一个完美例子是向 Javascript 添加“装饰者”的提议。当一项提案在 2016 年进入第二阶段时，他们获得了很大的人气，但尚未进展到第三阶段(截至 2018 年年中)。提案发生了实质性的变化，一些原始用例在更新的提案中不再受支持。

熟悉第三阶段建议的列表是跟踪 Javascript 中即将发生的事情的最好方法。一旦指定的评审者和 ECMAScript 编辑批准了完整的规范，提案就进入第 3 阶段。它可能已经可以在浏览器中使用，并可能有一个巴别塔变换。所有的实现都必须遵守规范，并且不太可能改变。

这并不是说第三阶段的提案一定会获得通过。处于早期阶段的特性不太可能进入规范，因为完全指定行为并在浏览器中实现它需要大量的工作。第 3 阶段的功能可能会被拒绝，因为在实施时发现了不可调和的问题，或者在`Object.observe`的情况下，因为该功能被认为是不必要的。

## 第四阶段

被认为已经完成并准备好正式包含在规范中的提案进入第 4 阶段。这些移至 TC39 方案回购的[已完成方案](https://github.com/tc39/proposals/blob/master/finished-proposals.md)部分。从阶段 3 推进到阶段 4 需要大量的工作。必须有验收测试，必须在 2 个独立的虚拟机实施。

# 一些历史

TC39 和发布过程是最近才创建的。如果感觉 Javascript 在过去几年里变化很大，你没有错。

## Ecma 国际和 ECMAScript

Javascript 是一种叫做“ECMAScript”的语言规范的实现，ECMA-262。 [Ecma International](https://www.ecma-international.org/) 是一个类似 ISO 的欧洲标准组织。ECMAScript 就是为什么新版本的缩写是 ES5，ES6 等。2015 年，他们将该流程改为年度发布周期，与 ES6 的发布时间一致——这也是它也被称为 ES2015 的原因。值得注意的是 ECMAScript 新版本之间的差异。(我还想指出，这些是正确的大写字母；Ecma 在 1994 年取消了其名称的首字母缩写，但是 ECMAScript 在规范中仍然是这种风格)

*   1999 年是 3
*   2009，ES5，最初为 ES3.1
*   2015，ES2015，此后每年发布。

## 是 6，是 2015 年，还是和谐

ES2015 是 Javascript 的一个转折点，无论是在技术上还是在未来的扩展方面。它引入了一系列新功能，在旧的巴别塔文档中一一列举，并且是新流程下的第一个版本。

这些特性彻底改造了 Javascript。新功能突然激增背后的原因很大程度上是政治性的，我不会试图深入重述它们。简而言之，ES5 的发展是有争议的。它最初打算作为 ES3.1 发布，但最终版本包含了不成功的 ES4 修订版的一些功能。如果你感兴趣，可以浏览一下 ES4 的草稿。

继 ES5 之后，ES2015 是由 TC39([Ecma](https://www.ecma-international.org/memento/tc39-rf-tg.htm)技术委员会 39)领导的新开发流程下的第一个版本。这是不同派别和解并开始再次合作后的第一次发布，因此代号为“和谐”

由于这一新流程一直运行得如此顺利，ECMAScript 的最新版本并没有像 ES2015 那样进行重大改革。Axel Rauschmayer 博士的博客对每个 [2016](http://2ality.com/2016/01/ECMAscript-2016.html) [2017](http://2ality.com/2016/02/ECMAscript-2017.html) 和 [2018](http://2ality.com/2017/02/ECMAscript-2018.html) 中引入的新功能进行了出色的总结。

## 现代 Javascript

这些官方发布已经变得不那么重要了。Babel 使我们能够在功能正式发布前几个月或几年使用它们，由于遗留浏览器的原因，我们可能在几年后才使用原生实现。巴别塔用 [`babel-preset-env`](https://babeljs.io/docs/en/babel-preset-env/) 让我们更容易忘记这个过程。通过配置您需要支持的浏览器，它将提供最少的转换和聚合填充，以尽可能使用本机实现。

巴别塔也开始反对`stage-x`预设，原因在[的博客帖子中宣布了这一变化](https://babeljs.io/blog/2018/07/27/removing-babels-stage-presets)。`babel-preset-env`为同一问题提供了一个更加以开发人员为中心的解决方案，建议不要手动设置预设。

* * *

感谢阅读！我在 Twitter 上的名字是 [@cvitullo](https://twitter.com/cvitullo) (但在其他大多数地方我都是 vcarl)。我主持了 React 开发者聊天室[React flux](http://join.reactiflux.com/)和 Node 聊天室 [Nodeiflux](https://discordapp.com/invite/vUsrbjd) 。JS 开发者。如果您有任何问题或建议，请联系我们！*
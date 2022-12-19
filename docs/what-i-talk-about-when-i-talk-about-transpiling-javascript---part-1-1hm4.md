# 当我谈到翻译 JavaScript 时，我要说的是——第 1 部分

> 原文：<https://dev.to/ardennl/what-i-talk-about-when-i-talk-about-transpiling-javascript---part-1-1hm4>

这篇姗姗来迟的博文是关于翻译 JavaScript 的。因为我已经拖延了几个星期，而*仍然*没有完成整篇文章，所以我决定分两部分发布。第一部分重点介绍了 JavaScript 的历史、ECMA 阶段和用 Repl 探索巴别塔。第二部分向您展示了如何在您的项目中包含 babel，使用或不使用 Webpack。

现在，让我们开始讨论如何翻译 JavaScript 吧！

把 JavaScript 转换成什么？良好的...JavaScript！到目前为止，大多数 web 开发人员都会遇到各种形式的巴别塔。Babel 是一个 *transpiler* ，它做了一些神奇的事情(transpiling 部分)来将最前沿的 JavaScript 编译成当前/旧浏览器或节点版本可以理解的东西。如今，似乎没有哪一种 JavaScript 是没有巴别塔的。但是我们到底为什么需要运输工具呢？我们传送什么？transpilers 将来会被淘汰吗？并非完全不重要:我们如何设置和配置我们自己代码的传输？

这些都是我希望通过这篇博客来回答的问题，但是要理解这些问题，我们首先需要回顾一些非常混乱的历史和术语。

你会注意到我将同时使用 transpiler 和 compiler 来描述 Babel。我交替使用它们，因为我认为区别并不重要。关于此事更详细的讨论，请阅读下面的 [stackoverflow 回答](https://stackoverflow.com/a/43969036)

## JavaScript 和 Ecmascript

当我们谈论 JavaScript 时，我们谈论 ECMAscript，反之亦然。我听到的一种解释是“ECMASCript 是语言，JavaScript 是方言”。我还听说 JavaScript 被称为实现 ECMAScript 规范的脚本语言。我肯定这是有争议的，但老实说，这真的很令人困惑，实际上*并不*重要。

有趣的是，ECMAScript 的发布至少是不规则的(原因很快就会变得明显，请不要抛弃我)。第三版于 1999 年发布，第四版本应于 2007 年登陆，但由于政治或其他原因没有实现。ECMAScript 5 是自 v3 以来的第一次正式更新，直到 2009 年才发布。

然而，ES5 是一件大事，它给我们带来了如今我们很多人都很熟悉的 JavaScript。大多数浏览器都实现了新标准，JavaScript 也越来越流行。

一个新的规范仍然花了六年时间才能落地，但在 2015 年，ES6 (ECMAScript 2015)终于落地了，这是一个巨大的更新。除了带给我们许多新的功能和改进，ES6 似乎还以每年一次的发布周期开启了一个新时代。ES7 (ECMAScript 2016)在 2016 年发布并最终完成，而不是再休眠七年，ES8 (ECMAScript 2017)在 2017 年发布，目前正在开发中。

我们应该从这个故事中学到的是:

1.  3-4 年以来，JavaScript 世界发生了很多变化
2.  版本控制真的很混乱。

> ES6 是 EcmaScript 6 的缩写，EcmaScript 6 是 EcmaScript 2015 语言规范的废弃名称。ES2015 是 EcmaScript 的第 6 个版本，因此之前被称为 ES6。出于他们自己最清楚的原因，那些负责定义语言标准的人用 v6 规范的最终版本将其重命名为 ES2015。-*[ES6 和 Javascript 在 StackExchange 上的区别](https://softwareengineering.stackexchange.com/questions/306846/different-between-es6-and-javascript/306847#306847)*

耶稣哭了。

先说清楚，我要谈谈 ES5 (ECMAScript 5)、ES6 (ECMAScript 2015)、ES7 (ECMAScript 2016)、ES8(ECMAScript 2017)以及接下来的一切。

*   如果你想了解更多关于 JavaScript 和 ECMAscript 的区别，请阅读迈克尔·阿兰达的这篇精彩文章
*   我在 stackoverflow 上找到了一篇关于 ECMAScript / JavaScript 历史的精彩总结，你应该读一下。

## TC39 和阶段-0-4

现在有一个每年的发布周期，了解为什么以及如何在语言中接受新的提议和特性也很有趣。这是由 [TC39](https://www.ecma-international.org/memento/TC39.htm) 完成的，代表技术委员会 39(这样的诗)。他们主要负责维护和更新 ECMAScript，他们也评估和考虑新的提议来发展这种语言。

对语言的更改是在一个包含几个阶段的过程中完成的:

阶段 0:稻草人
阶段 1:提案
阶段 2:草稿
阶段 3:候选人
阶段 4:完成
了解这一点很重要，因为 transpilers 允许我们在阶段 0 到 3 中使用功能。

*   [TC39](https://www.ecma-international.org/memento/TC39.htm)
*   [github.com 上的 TC39](https://github.com/tc39)
*   [TC39 流程](https://tc39.github.io/process-document/)

## 将 Javascript 转换成 JavaScript

现在我们已经经历了历史和阶段，我们终于可以开始谈论真正的运输了！当我们谈论转换 JavaScript 时，我们实际上是在谈论将一个 JS 版本编译成另一个 JS 版本。当 ES6 在 2015 年推出时，我们有一大堆新功能，但还没有环境会运行这些功能。将 ES6 转换成 ES5 意味着我们可以编写现代的 ES6 javascript，编译成 ES5 来支持旧的浏览器

### 传输与聚合填充

你可能会问:“等等，这不就是 polyfills 做的吗？”。是的，但不完全是。根据经验，transpilers 关心的是语法。将 ES6 转换成 ES5 会将`let`和`const`转换成`var`，并将你的 arrow 函数转换成一个常规的等价函数。(我说等价是因为[箭头函数和常规函数不完全相等](https://babeljs.io/faq#why-are-this-and-arguments-being-remapped-in-arrow-functions))。

Polyfills 处理与旧浏览器不兼容的新对象和方法。想想`Promise`对象，`Intersection Observer`或者你是否真的需要支持 IE9: `document.querySelector`。这些方法和对象都有 polyfills 来支持旧的浏览器，并且不会被 transpiler 处理。

如果没有整个灰色区域，它就不是 JavaScript。如果你想了解更多细节，我建议你阅读大卫·吉尔伯特的这篇精彩文章: [Polyfills:你想知道的一切，或者少一点点](https://hackernoon.com/polyfills-everything-you-ever-wanted-to-know-or-maybe-a-bit-less-7c8de164e423)

对我们来说幸运的是，Polyfills 和 Transpiling 就像羊肉和金枪鱼一样(我将继续使用它，直到有人得到它)，这是 Babel 的创造者和贡献者在发展 transpiler 时牢记在心的。

## 巴别塔——使用下一代 JavaScript，今天。

因为实际上，当我谈到将 JavaScript 移植到 JavaScript 时，我指的是巴别塔。Babel 是将你现代的、功能丰富的 JavaScript 编译成旧环境也能理解的东西的工具。曾经有过像特雷西这样的替代方案，但是特雷西似乎很短命地支持巴别塔

巴别塔是从 ES6 到 ES5 的传送者。ES6 和 ES5 之间的差异是巨大的，特别是在发布的时候，有很多浏览器还没有实现 ES6 的很多特性。更不用说永远不会更新的浏览器了。

但如果你记得，ES6 是在 2015 年发布的，到目前为止，大多数浏览器都是常青树，对 ES6 功能的支持也非常好。如果你不需要支持旧的浏览器，或者如果你使用 node，可以更新到最新版本，你可能不需要转换你的 ES6 JavaScript。

这是否意味着 Babel 在未来会变得过时，因为每个环境都支持所有 ES6 特性？大概不会。有一个相当稳定的 ES 发布周期，因为 ES6 和 Babel 允许你移植 ES7、ES8 和仍处于提议阶段的功能。这样我们就可以尝试最新的功能，而不必先登陆浏览器或节点。

### REPL

Babel 没有绑定到 Webpack 这样的特定构建工具。使用 Babel 有很多不同的方式，包括在浏览器中，使用命令行界面(CLI)和一系列框架和构建工具。但是尝试巴别塔最简单的方法是访问[巴别塔回复](https://babeljs.io/repl)

如果你导航到[https://babeljs.io/repl](https://babeljs.io/repl)，你可以立即开始写 ES6，看看你的代码被编译成什么样。在左侧的面板中，您会看到可用的预设。

#### 预设

Babel 有一个庞大的插件列表，用于将特定的 ES6 特性转换成 ES5。比如[这里是转换箭头函数的插件](https://github.com/babel/babel/tree/master/packages/babel-plugin-transform-arrow-functions)。单独安装所有这些插件会有点痛苦，所以 Babel 提供了不同的预置，你可以用它们来转换你需要的东西。

ES2015 (ES6) -将你的 ES6 编译成 ES5
ES2015 -松散模式-松散模式产生一个更简单和更可读的 ES5。在 [2ality 博客](http://2ality.com/2015/12/babel6-loose-mode.html)
es 2016(ES7)
es 2017(ES8)
阶段 0 - 3 -尚未正式纳入标准的功能上了解更多信息。小心使用这些，因为它们在到达最后阶段之前可能会被杀死或大量修改。
反应——反应？是的，这有点奇怪，但是 React 可以与需要传输的特性一起使用，其中之一是 JSX。JSX 并不是 ECMAScript 的一部分，而是它的一种扩展。

#### 环境预设

环境预设非常棒，因为它们允许您指定您的目标环境。不用再考虑哪个 ES 版本是哪个 ECMAScript 年份，您只需定义您希望代码工作的环境，预置将从那里开始。

请注意:当您想要使用阶段 0 - 3 或反应预设时，您必须显式激活它们，而不管您可能已经激活的其他预设。

#### 试试看

我强烈建议你试着自己移植一些你的 ES6 代码，因为看看 Babel 如何处理新的*语法糖*像 ES6 `class`是非常有趣的。

下周我们将深入探讨在您的项目中使用 Babel，在 Webpack 中使用它等等。如果你想了解最新消息，你可以随时在 twitter 上关注我！
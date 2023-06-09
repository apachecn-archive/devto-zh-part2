# 优秀代码评审的艺术

> 原文：<https://dev.to/joshghent/the-art-of-good-code-review-36cj>

[![](img/afe8aef95993a9d6c63500b500204803.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oH1Xhz9R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ACCruBV4o2oowbKvI.)

代码审查是任何软件开发过程的关键部分。理论上，它被设计来拓宽团队中的系统知识，并确保代码可维护且易于阅读。完善代码审查可以说是一门艺术，它需要在挑剔和不为小事伤脑筋之间取得平衡。

在我们深入探讨好的代码审查的原则之前，我们应该首先定义它**不是什么。许多人认为代码评审的一个目标是解决任何样式问题。随着[beautiful](https://prettier.io/)和 [ESLint](https://eslint.org/) 的崛起，关于代码格式的争论已经没有实际意义了。使用工具自动格式化消除了任何个人的个人意见和影响。此外，这也有利于在代码库的所有区域更加一致地格式化代码。**

其他人认为代码审查应该是为了捕捉错误。虽然如果你发现一些显而易见的东西，这可能是真的，但在更广泛的背景下理解代码通常是具有挑战性的。Bug 捕获通常应该留给一组严密的单元和集成测试，以及您的顶级 QA 部门。

现在我们知道了什么代码评审**不是**，什么才是好的代码评审？

### 知道什么时候说“够好了”

尽管所有软件公司的目标都应该是交付一流的代码，但也有过于吹毛求疵的时候。没有必要对代码的每个细节都吹毛求疵。这只会打击开发人员的士气。这是抓住细节和不过分之间的微妙平衡。只有你能判断这一点，时间磨练你的感知。我经常发现过分吹毛求疵的评论可以归结为评论者的个人偏好。应该避免这些类型的注释，而应该将你的注意力集中在代码中的实际问题上。

### 保持积极

代码审查经常看起来像是审查者走进来，把你用数字雕刻的无价艺术品撕成碎片。不要只关注问题，通常最好退后一步，思考合并请求的积极方面。也许有人使用了一个你不知道的很棒的语言功能。或者他们可能只是写了一个信息函数头。赞扬他们的这种工作。它提升了开发者的精神，意味着他们更容易接受你发现的问题。

### 看出去输出不拢

代码审查的一个关键部分是分析输出，而不是他们所采用的方法。解决一个问题有许多不同的方法，重要的是避免将一种方法强加于另一种方法。

在某些情况下，开发人员可能不知道另一种方法。例如，假设他们已经编写了一个方法，该方法循环遍历一个数组来查找该数组中的匹配元素。他们可能不知道 [Array.includes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes) (在 Javascript 中)可以解决他们的问题！在这种情况下，最好亲自去和开发商谈。他们可能无法使用 Array.includes。您很可能是以局外人的身份处理这个代码库，所以最好假设作者是专家。

### 富有建设性

当您在合并请求中发现问题时，您认为哪个注释会更好？

> 不，不要这样。

***或***

> 我们能不能把它改成不包括 X，因为这可能会导致 Z，也许会导致 Y？你怎么想呢?

在第一种方法中，评论是没有建设性的，不仅没有告诉作者问题的原因，也没有建议一个替代方案。另一方面，第二种方法建议使用 Y 而不是 X，因为 Z 可能发生。你可能对代码库有更多的经验，知道作者可能不知道的一些细节，你的评论意味着开发者将从你的智慧中受益。此外，第二种方法询问作者的想法。它没有铁腕统治，而是展开了一场讨论。

对于所有有经验的开发人员来说，代码审查是一个极好的方法，可以让他们的头脑陷入公司代码库的不同部分。此外，这是开发人员获得解决问题的新方法的经验的好方法。如果你还没有，试着将代码评审包含到你的过程中，并让整个团队参与进来。

* * *
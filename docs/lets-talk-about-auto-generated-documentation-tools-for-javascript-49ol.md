# 让我们讨论一下 JavaScript 自动生成文档工具的状态

> 原文：<https://dev.to/joelnet/lets-talk-about-auto-generated-documentation-tools-for-javascript-49ol>

在我写完将近一千行文档之后，我发誓再也不这么做了。因此，我开始寻找所有最好的工具来为我完成这项乏味的任务。

这些工具肯定已经存在了，对吗？`npm install`有事，运行一个命令，就搞定了，对吧？也许不是...

我试图记录函数式 JavaScript 代码，这显然比它应该的要复杂得多。随着函数式 javascript 的兴起，我发现这竟然如此复杂。

我只想为这样的东西创建文档:

```
// how do I document you, little function?
const add = x => y => x + y 
```

我希望 VSCODE 的 intellisense 能够理解这些文档，我还想生成一个 Markdown 文件或 HTML。

# [JSDoc 3](http://usejsdoc.org/about-getting-started.html)

显而易见的第一个开始是 JSDoc。我相信他们正在引领这个领域。但是对于一个关于文档的网站来说，他们自己的文档真的很恐怖。我没有找到关于如何正确记录我的`add`功能的信息。

我找到了一个未解决的问题[对 curried 函数的支持](https://github.com/jsdoc3/jsdoc/issues/1286)并检查了日期。2016 年 10 月。

[![rage face](img/f0ec1eaa7d76437c8a5816f28b0eecd6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7ccp9q3D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gwr7j43nhreztq77lbfb.png)

行...RIP JSDoc。

# [美并](https://github.com/nijikokun/minami)

[![Minami screenshot](img/85b02e8228339913485c241058380d2a.png)T2】](https://camo.githubusercontent.com/03e6d3687da4f4e05c2d4f9bd2b711f5f113a8c0/687474703a2f2f692e696d6775722e636f6d2f725043494671542e706e67)

这正是我要找的。截图看起来很棒...啊哦。JSDoc 3。该项目最后一次更新是在 2017 年 4 月 18 日。

撕裂美并。

# [doxdox](https://github.com/neogeek/doxdox)

[![doxdox screenshot](img/e0434907a08ee2f73f610c0ac4ab8474.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--emwIXIHC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/neogeek/doxdox/raw/master/screenshot.jpg)

这看起来很结实。上次提交时间是 2018 年 2 月 19 日。还是很久，不过不到一年。

所以我联系他们[如何记录可定制的功能？](https://github.com/neogeek/doxdox/issues/73)。

[![Please stand by TV screen](img/c4c219ee49295045ef6ffe6054b2f1cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ry9QUTcA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lsaw8wtk02trwgwio0rp.jpeg)

无线电静默

里普多克斯。

# [石板](https://github.com/lord/slate)

[![Slate screenshot](img/8d748dff93ec2174f156847b1e084768.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FNe6Qf1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/lord/img/master/screenshot-slate.png)

这是迄今为止我最喜欢的截图。回购中的伟大主题和最近的活动。

但是我怎么用呢？乍一看，网站上的文档似乎很全面，但是我该如何写文档呢？

好像是我创建了 markdown，HTML 就是由此生成的。我把降价放在哪里，我给降价取什么名字？我只能用`vagrant`做文件吗？

我先把这个放一放。

# [T1](#hmdoc)[hm-doc](https://github.com/JesterXL/hm-doc)

> **What** :从 JavaScript 代码中的 [Hindley-Milner](https://en.wikipedia.org/wiki/Hindley%E2%80%93Milner_type_system) 单行//注释和可选/* */块注释生成简单的 markdown 文档。

这似乎很有趣。定制代码的降价生成器。

VSCODE 可能不太懂，但至少我会把我的 API 文档写出来。

不过是 6 星。还是要试一试。我已经提交了一个问题。

# 优秀奖

[es doc](https://esdoc.org/)——类似于 JSDoc。

[YUIDoc](http://yui.github.io/yuidoc/)——类似于 JSDoc。

Autodoc——在 JavaScript 函数上方的注释中编写测试

React Styleguidist——隔离的 React 组件开发环境，提供生活方式指南

[故事书](https://github.com/storybooks/storybook) -交互 UI 组件开发&测试:反应，反应原生，Vue，Angular

# 那么接下来呢？

老实说，我对这些解决方案都不满意，我就要推出自己的解决方案了。

你使用什么解决方案来为你的代码生成文档？你感觉如何？

我的文章是非常实用的 JavaScript，如果你需要更多，请在这里关注我，或者在 Twitter 上关注我！

**更多文章**...一起
[问我关于函数式编程的傻问题](https://dev.to/joelnet/ask-me-dumb-questions-about-functional-programming-bho)

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)
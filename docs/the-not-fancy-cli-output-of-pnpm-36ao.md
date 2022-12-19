# pnpm 的不漂亮的 CLI 输出

> 原文：<https://dev.to/zkochan/the-not-fancy-cli-output-of-pnpm-36ao>

[pnpm](https://github.com/pnpm/pnpm) 是一个 JavaScript 包管理器，在很多方面与 npm 和 Yarn 不同。如果你还没听说过，我推荐你去看看。在这篇文章中，我很想写一下我们在安装过程中用来报告的设计系统。

当我第一次开始为 pnpm 做贡献时(大约在`v0.15`)，安装是这样报告的:

[![](../Images/9bf6d09db0c1c63eddf4f743ed3cf31b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0-WOVKg2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/pnpm/pnpm/v0.15.0/docs/images/screencast.gif)

它不是很有用，但是一些 pnpm 用户喜欢它。他们认为它很美。但是当我们开始添加更多的功能时，我们意识到以一种易读的格式打印适量的信息是非常重要的。

因此，让我们看看 pnpm 是如何发展的，以及它如何在不同的场景中进行报告。

## 申报安装在单个项目中

当您第一次安装 pnpm 并在项目中运行`pnpm install`时，您会看到如下输出:

[![](../Images/9ac17ca1166f39c7a64008d7d1ca0a75.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gLoK765H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/JccLTtM.png)

与旧的输出不同，这个输出非常静态和简洁，但是它包含了更多有用的信息。

我们看到:

*   其中一个已安装的软件包已被否决
*   117 个新包被添加到`node_modules`
*   安装速度慢了一点，因为正在下载巨大的 typescript tarball
*   商店中有 0 个可用的包，所以所有 117 个包都被下载了(pnpm 在磁盘上只保存一个版本的包一次，所以当商店中有可用的包时，它只是硬链接到`node_modules`)
*   `express@4.16.0`被添加为生产依赖项
*   注册表中有较新版本的 express
*   `babel-preset-es2015@6.24.1`和`typescript@3.0.1`被添加为开发依赖项

现在，让我们将 express 更新到最新版本，看看我们会得到什么:

[![](../Images/9874297346dd6b45a2b4a72f63cae5ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t_CEjgIM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/VemTmr6.png)

*   从 node_modules 中删除了 5 个软件包
*   向 node_modules 添加了 5 个包
*   所有 5 个软件包都是从注册表下载的
*   最新的快递被添加到项目中

## 报告多软件包存储库中的安装

pnpm 有一组用于处理多包存储库(MPR)的命令。在 MPR 中安装依赖项时，要处理的信息量非常大，以至于打印所有的信息会造成一片混乱，无法阅读。为了提供一些基本信息，我们提出了缩小报告的概念。缩小的报告只包含最重要的信息。

MPR 中的每个包都印有添加/删除包的数量(受 Git 启发):

[![](../Images/c3635e7cf6abc7ed151487e8d1d45af4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dNnMO5Gf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/dsnTBJK.png)

缩小报告还打印警告(只有警告，没有信息消息):

[![](../Images/50afc27d6245bca4e3697ee66609073d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EYntcrt3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/D8gH7Kz.png)

当我们为[递归命令](https://pnpm.js.org/docs/en/pnpm-recursive.html)提出缩小报告的概念时，我们意识到它们在其他场景中也很有用。例如，当包被链接进来时，它应该是缩小和放大报告的混合。应简要报告链接的包，当前工作目录中的包应成为焦点:

[![](../Images/e896d7354e5e487230affac6ed06537f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VfomBKva--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/aIFlBep.png)

## 实现细节

虽然输出看起来很少很简单，但它是由一个非常复杂的系统产生的。pnpm 由许多组件组成，许多操作可能以随机顺序发生(这是 pnpm[如此之快](https://github.com/pnpm/node-package-manager-benchmark#readme)的原因之一)。这就是为什么报告是由 pnpm 的一个称为“报告者”([代码](https://github.com/pnpm/pnpm/tree/master/packages/default-reporter))的专门部分执行的。

reporter 是一个包，它监听日志、过滤日志、组合日志并从中形成输出。pnpm 使用 [bole](https://github.com/rvagg/bole) 将日志从记录器传递给报告器。这种模块化非常好，因为我们可以模拟日志，并用测试覆盖报告！

为了将输出打印到控制台，我们使用 [ansi-diff](https://github.com/mafintosh/ansi-diff) 。`ansi-diff`很棒，因为它接受输出的“帧”,并且只更新控制台中发生变化的部分(而且速度很快)。在我们切换到`ansi-diff`之前，我们使用了另一个流行的库来更新控制台输出，但是它在更新时有明显的闪烁。

## P.S

实现良好的 CLI 报告非常困难。但是好的报告可以让开发人员专注于重要的事情，并可能更早地注意到问题。

当然，pnpm 的报告可以改进很多，我们在这方面还有很多未解决的问题。试试 pnpm，如果有可以进一步改进的地方，请不要犹豫让我们知道。

*最初发布于 [pnpm 博客](https://medium.com/pnpm/the-not-fancy-cli-output-of-pnpm-5bd4398716ce)T3】*
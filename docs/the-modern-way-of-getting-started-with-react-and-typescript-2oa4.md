# React 和 TypeScript 的现代入门方式

> 原文：<https://dev.to/brightdevs/the-modern-way-of-getting-started-with-react-and-typescript-2oa4>

健康和可持续的技术栈降低我们的项目快速过时的风险的先决条件之一是围绕日常琐事的工具化和自动化。几个月前，当构建和配置自动化问题的全新解决方案每天出现时，JavaScript 世界遭受了工具疲劳，每个解决方案都有自己的学习曲线。

但是在这些失败的尝试中，新一代的解决方案在脸书 React 诞生了。 [`create-react-app`](https://github.com/facebook/create-react-app) (通常简称为 CRA)是一个一体化的集成开发工具，预配置了用于捆绑和打包的 webpack、用于 ES6 支持的 Babel、具有所有现代便利的强大开发服务器、具有覆盖工具的测试运行器等。甚至还有一名服务人员准备好[启动你的 PWA 之旅](https://www.linkedin.com/pulse/10-reasons-why-you-should-consider-progressive-web-apps-eriksen/)。

所有这些都打包在一个可更新的 NPM 依赖项中。它从开发人员那里抽象出除实际应用程序代码之外的所有东西，不需要深入挖掘——没有可见的构建过程，不需要开发工具配置，不需要测试连线，在我们实际开始编写应用程序代码之前，不需要生成样板文件。CRA 是大多数中小型 React 项目避免整个构建配置麻烦的方法。

```
npx create-react-app my-app
cd my-app
npm start 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们开始查看从本地开发服务器提供的“Hello World”页面所需的全部内容，每当发生源代码更改时，页面会自动刷新。

[![Hello World from create-react-app](../Images/fe032966248d39336867d6d63cd8c777.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FRBfMj32--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d69q5xk83hkyz16juov3.png)

这很酷，已经帮我们省去了一周的头痛。但是在 create-react-app 中没有默认的 TypeScript 支持。TypeScript 真的很棒，在一起工作了一段时间后，我现在觉得没有 TypeScript 的 JavaScript 就像一块没有巧克力的蛋糕——你可能会吃，但那种快乐在哪里呢？

幸运的是，我不是唯一一个这样想的人，开源不喜欢 void。有一个用 TypeScript 代替 Babel 的 create-react-app 的分叉，出人意料地被命名为 [`create-react-app-typescript`](https://github.com/wmonk/create-react-app-typescript) 。让我们倒回去从头开始，这次用打字稿:

```
npx create-react-app my-app --scripts-version=react-scripts-ts
cd my-app
npm start 
```

Enter fullscreen mode Exit fullscreen mode

注意一些重要的事情——我们没有调用`create-react-app-typescript` fork 自己的二进制文件。CRA 是以这样一种方式设计的，它对定制开放，没有绑定到可能停止维护的分支的麻烦。我们实际上仍然在直接使用`create-react-app`，我们只是用一个支持打字稿的(`react-scripts-ts`)替换了它的内部机制(`react-scripts`)。这意味着，无论何时在`create-react-app`有针对我们的开发、捆绑或测试环境的更新，我们——作为一等公民——仍然能够只需`npm update`就能得到所有的好东西。

这就是我们得到的——几乎和以前一样，但是用 TSX(打字稿风格的 JSX)代替了！

[![Hello World from create-react-app-typescript](../Images/57a9a0785fb0f949b1135bf0a12443bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pcw-jehA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1e5meucg4rfahdgpxk5j.png)

但是，如果我们已经超越了`create-react-app`的产品，我们需要挖掘 webpack bundler 或 jest test runner 的诡计，那该怎么办呢？这是可能的，但不幸的是，在这种情况下，我们需要告别漂亮而干净的抽象，这种抽象使我们不必处理大约 30 个直接依赖项和大约 12 个配置文件。当我们运行`npm run eject`命令时，我们的项目被重写以直接包含所有这些，我们不再能够将我们的开发环境`npm update`为一个单独的依赖项。在决定做之前，我们应该明确地知道我们在做什么。在大多数情况下，直到我们开始对构建过程有了非标准的需求，我们才应该需要这样做。

在下一篇文章中，我们将看到 React 应用程序中的类型脚本支持会给我们带来怎样的好处。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

由亚当吧、网络佬@光明发明
[个人博客](https://whatwebcando.today/) [推特](https://twitter.com/NOtherDev) [邮箱](//adam.bar@brightinventions.pl) [Github](https://github.com/NOtherDev)
# 从反作用风扇的角度看有角度-第 1 部分

> 原文：<https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-1-188p>

### 角级数

*   [**第一部分——自举和打字稿**](https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-1-188p)
*   [第 2 部分-测试](https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-2-7bg)
*   [第 3 部分-状态管理](https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-3-5dnj)

我已经在个人和工作项目中使用 [React](https://reactjs.org/) 大约两年了。我非常喜欢它，以至于我想把我的中间名改成*反应*，但是后来我意识到我没有中间名。

总之，我最近开始了一个新项目，他们用… [Angular](https://angular.io/) 构建了前端。仅仅因为新来的人真的喜欢别的东西而从头重写它，会被那些喜欢向客户交付软件之类的东西的人所不喜欢，所以我发现自己除了最终钻研它之外别无选择。

我完全没有资格对*棱角分明的*发表意见，但这不会阻止我这样做。我想写下我迄今为止的印象，并将其与我在*反应*世界中习惯做的事情进行比较。请注意，我是直接从 *Angular 4* 开始的，我没有计划在短期内检查 *AngularJS* ，所以我可能会遗漏很多关于为什么事情会像在`ng`中那样的上下文。

我将从基础开始，引导，默认等等。

## 自举

在*反应*的世界里，自举是一个肮脏的词汇。坦白地说，建立一个新项目是一件痛苦的事情。我个人在某个时候决定，跟上 [Webpack](https://webpack.js.org/) 是一项全职工作，所以我把我的配置外包给了 [Create React App](https://github.com/facebook/create-react-app) 。即便如此，我仍然在等待类似 *CSS 模块*的东西开箱。不能说这是完美的。

另一方面， *Angular* 更像是一种包含电池的**方法。一旦你有了这个包，你只要运行
就可以得到一个非常全面的初始设置**

```
ng new awesome-app 
```

Enter fullscreen mode Exit fullscreen mode

它安装了一堆东西。让我们看看什么是`awesome-app`目录

```
-rw-r--r-- 1 mfernandez 1.3K Mar 25 22:36 .angular-cli.json
-rw-r--r-- 1 mfernandez 245 Mar 25 22:36 .editorconfig
drwxr-xr-x 12 mfernandez 384 Mar 25 22:39 .git
-rw-r--r-- 1 mfernandez 544 Mar 25 22:36 .gitignore
-rw-r--r-- 1 mfernandez 1.1K Mar 25 22:36 README.md
drwxr-xr-x 5 mfernandez 160 Mar 25 22:36 e2e
-rw-r--r-- 1 mfernandez 923 Mar 25 22:36 karma.conf.js
drwxr-xr-x 894 mfernandez 28K Mar 25 22:37 node_modules
-rw-r--r-- 1 mfernandez 415K Mar 25 22:37 package-lock.json
-rw-r--r-- 1 mfernandez 1.3K Mar 25 22:36 package.json
-rw-r--r-- 1 mfernandez 722 Mar 25 22:36 protractor.conf.js
drwxr-xr-x 14 mfernandez 448 Mar 25 22:36 src
-rw-r--r-- 1 mfernandez 363 Mar 25 22:36 tsconfig.json
-rw-r--r-- 1 mfernandez 3.0K Mar 25 22:36 tslint.json 
```

Enter fullscreen mode Exit fullscreen mode

从一开始就有很多好东西！我喜欢免费的东西。您已经配置了 [tslint](https://palantir.github.io/tslint/) ，用于单元测试的 [karma](https://karma-runner.github.io/2.0/index.html) ，甚至使用[量角器](https://www.protractortest.org/#/)的端到端测试。相当令人印象深刻。要是在 *React* 也能这么容易上手就好了。

不过，T4 非常固执己见。看起来社区中对使用的工具有很强的共识。`npm`比`yarn`更受青睐，`TypeScript`比`JavaScript`更受青睐，以此类推。

## 打字稿

我记得在我以前的公司，有一些关于用 JS 打字的内部讨论。一个阵营青睐[打字稿](http://www.typescriptlang.org/)，另一个 *JavaScript* 和 [Flow](https://flow.org/) 。我最终选择了 *flow* ，但我总觉得它没有捕捉到很多错误，而且我花了很多时间让它工作，就像 [flow-typed](https://github.com/flowtype/flow-typed) 一样。我还没有完全理解 *TypeScript* 提供的整个类型系统，但是你可以看到与 flow 有一个重要的区别。它与核心语言本身的集成程度要高得多，这在我的印象中让它可靠了很多。

从某种意义上来说，TypeScript 让人耳目一新，因为事情似乎变得更加自然。工具似乎也非常成熟，从我从其他使用 *Visual Studio 代码*或 *IntelliJ* 的人那里看到的(虽然我对此一无所知，但我仍然忠于 *Emacs* )。无论如何，到目前为止，我觉得和它一起工作很愉快。

从某种意义上说，这感觉就像是让 *JavaScript* 更接近 *Java* 。这听起来像是背叛，但是它可以让你构建更有弹性的软件。像[接口](https://www.typescriptlang.org/docs/handbook/interfaces.html)或[类成员的私有修饰符](https://www.typescriptlang.org/docs/handbook/classes.html#public-private-and-protected-modifiers)这样的东西有助于避免意外，并构建更加独立的组件。对于多个开发人员在同一个代码库上工作的项目，我可以看到这会带来更好的整体架构。

*TypeScript* 正受到*微软*的大力推动。他们自己也在积极开发功能，但也在移植[的提议](https://github.com/tc39/proposals)，这些提议进入 *EcmasScript* 。我在开始的时候对此有点困惑，但是你只需要记住，只有第三阶段或以上的提议才得到支持。我发现[这个](http://kangax.github.io/compat-table/es2016plus/#typescript2_8)网站可以让你检查到底支持哪些功能。

## 林挺

我是林挺的忠实粉丝，`ng`提供了一个开箱即用的 [tslint](https://palantir.github.io/tslint/) 配置。你只需运行`ng lint`就能获得即时反馈。

然而，最近我一直在思考棉绒的作用。在看到 [gofmt](https://golang.org/cmd/gofmt/) 工作得如此之好之后，我开始认为 linter 不应该关心代码的格式，只是避免不好的做法或可能的错误。在使用 *JavaScript* 的情况下，我用[beautiful](https://github.com/prettier/prettier)做了一点试验来自动格式化我的代码，尽管我目前只在个人项目中使用过它。这是我想在未来更详细检查的事情。

## 接下来

第一印象很好，但其他事情并没有那么好。下一部分将是关于测试。
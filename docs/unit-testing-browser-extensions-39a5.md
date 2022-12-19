# 单元测试浏览器扩展

> 原文：<https://dev.to/pureooze/unit-testing-browser-extensions-39a5>

四月，我成为了萨卡人的维护者，这是一个浏览器扩展，允许用户搜索他们的标签、书签和历史记录。萨卡人最初的目标是提供一个优雅的标签页搜索，但是当最初的维护者 [eejdoowad](https://github.com/eejdoowad) 认识到用户搜索标签页的方式与搜索书签和历史相同时，这个目标很快发展到包括最近关闭的标签页、书签和历史。这是一个重要的见解，它帮助萨卡人成为一个有价值的生产力工具。

当我成为维护者时，我对项目中缺少测试感到惊讶。有几个组件具有复杂的逻辑，但在任何地方都找不到测试。作为一名开发人员，我学到的最重要的事情之一是，测试是编写可靠、易于重构的代码的最简单的方法。老维护者只是懒惰吗？他仅仅是不关心他的代码的质量吗？不，恰恰相反，他很在乎。

[![Saka used to switch between tabs](../Images/ecb8660ca9afea5b078f8890d3e3d1ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5j7qp_oB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uuripy5nf6vyxfoonwna.gif)

问题是缺少关于这个主题的文档意味着几乎没有人能够测试它们的扩展。我对自己在不破坏代码的情况下做出改变的能力没有信心，这是一个大问题。但是在尝试了十几种不同的方法后，我最终找到了一个解决方案。

### 我们为什么要考试

作为开发人员，我们希望确保我们今天编写的代码不会成为应用程序生命周期后期维护的负担。避免产生这些负担的一个方法是编写测试。测试的伟大之处在于，除了验证函数的行为，测试还允许我们为未来的开发人员提供文档。例如，通过创建单元测试，我们声明给定函数的有效输入和输出。这使得重构代码变得更加容易，因为当我们所有的测试都通过时，我们可以确信我们的代码工作正常。

### 测试方法

这篇文章将关注于设置环境和编写一些基本的单元测试。我没有对浏览器扩展执行集成测试的解决方案，因为在 Chrome 中运行时，试图从网页访问 WebExtension APIs 会出现问题。

## 解

在寻找测试萨卡人的解决方案时，我浏览了几个不同的测试库，比如 Jest、Mocha 和 Jasmine。对我来说，最大的挑战之一是萨卡人是使用 Preact 编写的，这导致了与其他库的兼容性问题。但是在网上看了几个例子之后，我终于能够用 Karma 和 Jasmine 提出一个解决方案。

### 先决条件

为了使用这个解决方案，您的项目应该使用 Webpack。该示例使用版本 4，但这可能仍然适用于旧版本。虽然我没有尝试过，但在进行一些配置以确保所有东西都被正确捆绑后，应该可以用 Gulp 实现这一点。你可以在这里找到一个示例 webpack 配置[。](https://github.com/pureooze/extension-testing-example/blob/37e5a82cc436fc8256110600255145ad347340f1/webpack.config.js)

[![Karma, Jasmine and Chrome logos](../Images/4169923342084753bb125dd6380c7478.png "Karma, Jasmine and Chrome")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QiVhn0O2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/95124nbr9e5k8vmuvwj8.png)

### 因果报应+茉莉

如果您还不熟悉， [Karma](https://github.com/karma-runner/karma) 是一个工具，它允许在浏览器中执行 JavaScript 代码以进行测试。虽然它可以执行代码，但它无法测试代码，而是依赖于第三方库，如 [Jasmine](https://jasmine.github.io/) 和 [Mocha](https://mochajs.org/) 。在开发萨卡人时，我选择了 Jasmine，因为我以前在其他项目中使用过它。

安装 Karma 和 Jasmine 的第一步是安装它们:

`yarn add jasmine karma karma-chrome-launcher karma-jasmine karma-spec-reporter karma-webpack babel-loader --dev`

在 Karma 开始运行测试之前，它需要知道使用什么配置参数。为了提供这些，在项目的根目录下创建一个 karma.conf.js 文件。我在这里提供了一个示例配置[。注意，Karma 能够独立运行 Jasmine，只需要通过框架配置属性告诉它使用它。](https://github.com/pureooze/extension-testing-example/blob/37e5a82cc436fc8256110600255145ad347340f1/karma.conf.js)

### 铬

那些真正读过 karma 配置的人可能会注意到，它将 Chrome 指定为一项要求:

`browsers: ["ChromeHeadless"]`

正如我前面提到的，Karma 需要一个实际的浏览器来运行 JavaScript 代码。这一行告诉 Karma 应该在它运行的系统上寻找 Chrome，并在[无头模式](https://developers.google.com/web/updates/2017/06/headless-karma-mocha-chai)中启动它。使用 headless 模式的好处是，您可以在测试运行时使用系统，而不是每隔 2 秒钟就被新的测试中断一次。对我来说似乎是一个明显的胜利。

### 添加一个测试

要开始添加测试，使用项目的 src 目录下的代码创建一个 JavaScript 模块。顾名思义，sum 函数只是简单地将传递给它的所有值相加，然后返回总和。

在项目的根目录下创建一个测试目录——这是所有测试所在的地方。看一下 karma 配置文件，注意[这一行](https://github.com/pureooze/extension-testing-example/blob/4f047127a0735c5d97f3b4c6f2b46b063de61d55/karma.conf.js#L4)。它告诉 karma 要加载测试，必须使用 test/index.test.js 文件作为入口点。在 index.test.js 文件中添加[下面的代码](https://github.com/pureooze/extension-testing-example/blob/4f047127a0735c5d97f3b4c6f2b46b063de61d55/test/index.test.js)来导入测试目录中以. test.js 结尾的所有文件

配置完成后，在测试目录[中添加一个新文件 simpleModule.test.js，就像这样](https://github.com/pureooze/extension-testing-example/blob/4f047127a0735c5d97f3b4c6f2b46b063de61d55/test/simpleModule.test.js)。这个文件将包含 simpleModule.js 文件中所有函数的测试。describe 块用于对 Jasmine 日志中的测试进行分类，以便更容易判断哪些模块出现了故障。单个测试位于 it()函数中，它需要一个描述作为第一个参数，测试函数作为第二个参数。要了解更多关于如何使用 Jasmine 编写测试，你可以参考[文档](https://jasmine.github.io/api/3.0/global)。

### 运行测试

为了运行测试，可以直接调用 karma 可执行文件，将配置文件的路径作为参数传入。虽然这可行，但更好的解决方案是将该命令添加到 package.json 文件[中的 npm 脚本中，就像这样](https://github.com/pureooze/extension-testing-example/blob/4f047127a0735c5d97f3b4c6f2b46b063de61d55/package.json#L7)。你现在应该能够运行纱线测试，并看到来自 Karma 的输出，如下所示。

[![Screenshot of result after the tests ran](../Images/df08e78b9978a38e2f6eed39a56b0ec9.png "Screenshot of result after the tests ran")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lPonWH7s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ej9aoz0sbledxm5ro8e5.png)

### 用 WebExtension APIs 进行测试

开发人员在尝试测试扩展时遇到的问题是必须在测试中处理 WebExtension APIs。问题是测试运行的环境——也就是 chrome 中的网页——不能访问 API。这成为一个问题，因为 Jasmine 会抛出一个错误，因为任何与浏览器。*将未定义。

为了解决这个问题，你需要安装 sinon-chrome，一个能够模仿这些 API 的库。

`yarn add sinon-chrome --dev`

在 src 目录中创建一个名为 popup.js 的新模块，代码为。注意 getUrl 函数是如何依赖 browser.runtime.getURL API 的。我们将使用 sinon-chrome 来模拟浏览器返回的响应。

在测试目录中创建一个名为 popup.test.js 的新文件，以存储您刚刚创建的 popup.js 文件的所有测试。将下面的代码添加到测试文件中，注意浏览器 API 是如何被 sinon-chrome 模仿的。对于每一个使用 WebExtension APIs 的测试，您必须指定当 Jasmine 遇到它时每个 API 应该返回什么，从而允许您绕过没有定义 API 的问题。

运行纱线测试，您应该会看到以下测试结果:
[![Screenshot of successful test run with sinon-chrome ran](../Images/f6413b51ea87058821144525d5942581.png "Screenshot of successful test run with sinon-chrome")](https://res.cloudinary.com/practicaldev/image/fetch/s--euIG8pga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kzwn4tdhtmhmgsb1ww16.png)

你可以自由地测试你的 chrome 扩展，而不必担心扩展 API。

## 未来的工作

虽然 Karma、Jasmine 和 Chrome 的这种设置是可行的，但这并不是一个理想的解决方案。使用 Jest 有一些好处，Jest 是一个完全在 Node 中运行的现代测试库，因此不需要测试运行程序和浏览器。不幸的是，Jest 与 Preact 有一些兼容性问题，所以我暂时把它搁置了。希望我能找到一些时间来迁移测试以使用 Jest，因为我认为它会成为一篇好的博文。

你可以在这里下载 Chrome 的萨卡人，在这里下载火狐的 T2
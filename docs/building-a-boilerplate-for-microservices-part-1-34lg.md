# 为微服务构建样板文件—第 1 部分

> 原文：<https://dev.to/timecampus/building-a-boilerplate-for-microservices-part-1-34lg>

### 为微服务构建样板文件—第 1 部分

[![](img/d2d1eefaa0341f745fb904f61dcff63b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gAfbs3eG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/583/1%2AFkI7s0abXnLn-xWszZzsLg.png)

构建项目经常涉及到做许多重复的任务，我们作为开发人员经常会遇到这样的挫折:

> "为什么我不能只研究这该死的商业逻辑，而忘记其他一切？"。

连我也一直有同样的问题。我必须在平台上工作，设置项目结构，安装依赖项，设置监控、测试、林挺、构建、记录、日志、容器化等所需的整个堆栈。如果作为一个团队工作，我必须让其他人也这样做。从那时起，我开始寻找可能帮助我更快开始使用整个堆栈的项目。

虽然我发现样板文件和项目可能会在这个过程中对我有所帮助，但我注意到了一些事情:

1.  其中一些提供的选择不是太少就是太多——我只是想要足够让我快速开始，而不是在上面放太多糖。
2.  其中大多数已经过时，包括许多过时的依赖项，没有得到积极维护，并且在扫描时还包括许多安全问题。
3.  其中一些与我感兴趣的堆栈不匹配——我总是将我对正确技术堆栈的偏好建立在许多因素的基础上，并不是我的所有偏好都得到了满足。
4.  有些包括供应商/框架锁定—我不喜欢与任何特定的供应商或框架有任何紧密的耦合，其中许多将我束缚在特定的东西上，限制了我在未来迁移或创新的能力。
5.  它们中的大多数都不是生产级的——虽然它们在开发环境中都工作得很好，但它们并没有提供我在生产环境中所寻求的可靠性、规模和性能。
6.  有些遵循他们自己的标准——虽然创新是好的，但始终坚持社区中众所周知和公认的标准比建立自己的标准更好。许多项目缺乏这一点。

在所有的研究之后，我开始为基于 Node.js 的微服务栈制作自己的样板文件已经有一段时间了，今天，

**我很高兴地宣布 Node-skeleton** 的 v1.0 版本(开放给任何更好的名字:P)。

**节点-骨架**——【https://github.com/tvvignesh/node-skeleton T2】

在这篇文章的后面，我将分享我在这个项目中做出的所有决定，为什么要这样做，路线图以及如何快速开始。

[**Node.js**](https://nodejs.org/en/) **为服务器端语言**

我希望使用一个轻量级、高性能的平台，擅长 I/O，周围有一个良好的工具生态系统，并且可以在生产中很好地扩展，还可以减少板上开发人员的时间。考虑到这一点，Node.js 成了我的默认选择。你也可以在这里阅读我的文章[，在这里我解释了为什么我绝对喜欢 Node.js 生态系统。](https://medium.com/techahoy/why-i-am-a-big-fan-of-node-js-ecosystem-e99319c7dfe0)

[**ExpressJS**](https://expressjs.com/) **为 Node.js 框架**

我做过很多使用 ExpressJS 作为 Node 框架的项目，我喜欢它的一点是，它很简洁，被广泛采用，有很多工具，稳定，许多其他框架依赖于它或者扩展了 express 的特性。

****/Javascript 为语言****

 **有了大量的 Javascript 版本，并且 JS 是一种弱类型函数式编程语言，在上面有一个超集是有意义的，它允许我使用 JS 最新版本的所有新的惊人特性，充当 transpiler，在需要时向我们编写的代码添加类型，在上面提供一组很好的工具，具有良好的 IDE 支持，有助于处理代码，在构建时删除注释等内容， 与第三方工具很好地集成，Typescript 是我的选择，除此之外，基于 Typescript 构建的项目和围绕它的社区真的很棒。

[**码头工人**](https://www.docker.com/) **为集装箱**

Docker 已经成为容器化应用程序的事实上的标准，它有大量的工具，一个巨大的社区，在云中运行时有许多选择，并且许多 [CNCF](https://www.cncf.io) 项目都是在 Docker 的支持下构建的。这使得用 Docker 在容器上开始运行项目变得非常容易，而像 [Rkt](https://coreos.com/rkt/) 这样的替代品仍然存在。

除此之外，我们还捆绑了一个示例 [docker-compose](https://docs.docker.com/compose/) 文件，以便在开发过程中轻松使用多个容器。

****为磨合主机****

 **虽然容器很棒，但有时需要在主机中直接运行节点进程，而不是在容器中运行，我们需要一个进程管理器来管理它、集群进程、管理日志、进行负载平衡、管理内存、CPU 等资源。由于项目完全是在 Node.js 上完成的，PM2 成了我的默认选择(也有类似于 [forever](https://github.com/foreverjs/forever) 的选择，但是工具支持有限)

[**ESLint**](https://eslint.org/) **为林挺**

虽然为林挺项目使用 TSLint 很有吸引力，但我意识到该项目不如 ESLint 成熟，而且 ESLint 通过 [eslint-typescript](https://www.npmjs.com/package/eslint-plugin-typescript) 插件提供了对林挺类型脚本的支持，这让我的工作变得容易多了。配置好之后，我开始为林挺寻找规则的灵感，在定义规则的时候，我从 [Airbnb](https://github.com/airbnb/javascript) 和 [XoJS](https://github.com/xojs/xo) 那里获得了很多灵感。

[**【vscode】**](https://code.visualstudio.com/)

这个编辑器是一个野兽，已经变得非常受欢迎，我爱上了它。我曾经是一名 Atom 用户，我转而使用 VSCode 只是为了进行试验，我一直很喜欢这个旅程。您可能想知道为什么在谈论堆栈时还要谈论 VSCode。事实上，VSCode 已经成为样板文件的一部分，因为在我的 VSCode 编辑器中配置的设置是附带使用的，这些设置可以在自动缩进、空格/制表符校正等方面提供帮助，并帮助您遵守要遵循的编码标准。

[**温斯顿**T3】](https://github.com/winstonjs/winston)

Winston 是一个非常棒的用于登录 Node.js 的库，因为它允许很多传输，有一个很好的社区，支持诸如日志级别之类的东西，通过插件支持 Logrotation，如果需要还允许你扩展它。因此，对我来说，在开发环境中使用控制台作为传输工具，在生产环境中使用文件作为传输工具是有意义的。

[**Typedoc**](https://typedoc.org/)

我在为编写的代码寻找一个文档生成器(也可以使用 [JSDoc](http://usejsdoc.org/) )，由于我使用了 Typescript，Typedoc 非常适合这个等式，并为我完成了这项工作。

[**OpenAPI/Swagger**](https://www.openapis.org/)

我希望使用最新的开放 API 规范 3.0 版来记录项目中的 API，我开始深入研究，并注意到我可以通过像 [this](https://mermade.github.io/openapi-gui/) 这样的工具生成 YAML/JSON 规范文件，一旦生成，我可以使用像 [swagger-ui-express](https://github.com/scottie1984/swagger-ui-express) 这样的库来通过 express 端点公开它们，它非常有效。

[**Nodemon**T3】](https://github.com/remy/nodemon)

下一项工作是通过向堆栈中添加即时重启来加快开发过程，鉴于 Nodemon 的巨大受欢迎程度、社区和工作选项负载，Nodemon 是这项工作的合适人选，我所要做的就是添加一个 json 文件，其中包含完成工作所需的选项。

* *测试用**

 **虽然编写您的业务逻辑非常重要，但我已经意识到为您编写的代码编写测试同样重要，因此，我一直在寻找一个可以帮助我的框架，考虑到它带来的性能提升、良好的 API 以及没有全局变量的良好标准等，我选择了 AVA 而不是 Mocha 和其他。

**暴露度量到** [**普罗米修斯**](https://prometheus.io/)

虽然处理应用程序的业务逻辑很重要，但监视其健康和状态、在出现问题时添加警报以及跟踪各种趋势随时间的变化也很重要。这就是普罗米修斯出现的地方。我已经捆绑了 prom-client 来帮助向 prometheus 展示指标，它非常有效。

**在路线图中**

这个项目经历了快速迭代(你可能每周都有新的东西)，这是我在不久的将来的计划。接受建议。

1.  使用 [Passport](http://www.passportjs.org/) 添加示例验证策略
2.  为 CI/CD 添加示例 Jenkinsfile
3.  为 [Kafka](https://kafka.apache.org/) 生产者&消费者添加示例实现
4.  添加示例库配置
5.  添加对[特使](https://www.envoyproxy.io/)和[伊斯蒂奥](https://istio.io/)的支持
6.  添加对 [GraphQL](https://graphql.org/) 的支持

还有更多的在路上。明星/观看项目以密切跟踪它。很快会带来更多消息。

**开始使用节点框架**

请参考项目的自述:[https://github.com/tvvignesh/node-skeleton](https://github.com/tvvignesh/node-skeleton)关于如何在 5 分钟内快速开始你的项目的说明。

**参与本项目**

欢迎投稿。请随意提出有问题的拉动式请求。经过适当的讨论和审查后，我很乐意接受。如果你想在经济上支持，代表这个项目向慈善机构捐款，并分享细节——我会把你加入捐款人名单。

**使用本项目**

该项目在麻省理工学院的许可下开放源代码，你可以使用它而不要求任何个人或商业目的。如果你确实使用它，请让我知道，这样我可以把你添加到用户列表中。

如果您对微服务的样板文件有任何建议，请告诉我，我会考虑如何将其添加到路线图中。请随意使用它，并让我知道它如何为您服务。

在我发布这个项目的下一个主要版本之前，我将会发布另一篇博文。敬请期待！

* * *

### [特维内什](https://www.tvvignesh.com)

在以下位置找到我:

[Twitter](https://twitter.com/techahoy)|[LinkedIn](https://www.linkedin.com/in/tvvignesh)|[脸书](https://www.facebook.com/tvvignesh)|[Github](https://github.com/tvvignesh)|[Medium](https://medium.com/@tvvignesh)

或者加入我们的 [Timecampus](https://www.timecampus.com) 社区:

[Twitter](https://twitter.com/timecampus)|[LinkedIn](https://www.linkedin.com/company/timecampus)|[脸书](http://facebook.com/timecampustech/)|[Github](https://github.com/timecampus)|[Medium](https://medium.com/timecampus)******
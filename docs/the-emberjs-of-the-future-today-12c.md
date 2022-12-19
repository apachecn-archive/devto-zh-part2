# 未来的灰烬...今天！

> 原文：<https://dev.to/nullvoxpopuli/the-emberjs-of-the-future-today-12c>

Ember 有一些令人兴奋的功能，将它从一些人可能更熟悉的“过时”框架转变为一个令人兴奋的、符合人体工程学的、功能齐全的前端工具集，以实现最终的生产力。如果你以前认为 Ember 不值得一试，可以看看这篇文章，我会展示一些我个人最喜欢的功能，我现在正在 emberclear.io 上使用。

**注**:其中一些有点*出血*边缘，还没有正式发布。每个特性都有一个严格的问答过程，完成所有向后兼容的场景和升级路径需要一些时间。因此，官方指南不会提到这些功能。什么是生产就绪/仍处于试验阶段的分类在这篇文章的底部。

为了尽可能保持最新状态，请加入 [Ember 社区 Discord](https://discordapp.com/invite/zT3asNS)

### [异步生命周期挂钩](https://gitlab.com/NullVoxPopuli/emberclear/blob/master/packages/frontend/src/ui/routes/chat/route.ts)

[![Ember route demonstrating server-side rendering gates as well as [decorators](http://ember-decorators.github.io/ember-decorators/latest/), and async-aware lifecycle hooks](../Images/e8301627d3cf3421686802f8f3e21edf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5TId30RW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1oiicra01ryslttayncw.png)

这是一个路由处理程序，演示了异步生命周期挂钩，这些挂钩通过装饰器在服务器端呈现上下文中被禁用。人们可能希望这样做，因为应用程序可能会与 [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) 或 [indexeddb](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 进行交互，而这些在服务器端上下文中并不存在。

*   `beforeModel`是出入路线的警卫。在 [emberclear.io](https://emberclear.io) 中，为了聊天，你必须设置好你的“账户”。
*   `model`为路线提取数据。数据 api 支持图形数据——在`findAll`调用中，我指定对于每个`message`,我还需要`sender`。这有助于减少对后端的请求数量。

### [组件的语法](https://gitlab.com/NullVoxPopuli/emberclear/blob/master/packages/frontend/src/ui/routes/login/template.hbs)

[![Template for the login route demonstrating component invocation using Angle Brackets](../Images/77c5c2c9aa745f40d38d500267cfece9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Uoor2sZH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DmDpD52U8AArQgP.jpg)

紧随 angular、react 和 vue 的脚步，Ember *也有*的组件，这让你的代码感觉更自然，可读性更好——尤其是与更老的(有时令人讨厌的)[手柄](https://handlebarsjs.com/)语法相比，在那里模板将到处加载`{{ ... }}`。

现在，`{{ ... }}`只用于非 HTML 值，或者换句话说，动态/插值。

*   `{{input ... }}`是一个助手，它抽象出一些`<input`配置，并建立双向数据绑定。
*   `{{t ... }}`是一个帮助器，它为动态翻译查找国际化/ i18n 关键字。
*   `@title`是一种符号，它告诉组件该变量打算用作组件的参数，而不是要标记的 HTML 属性。例如，您可以在`FocusCard`模板中指定`...attributes`，并且在`<FocusCard ... />`调用中指定的所有 HTML 属性都将传递给`...attributes`用法。在我看来，这是 react 比`this.props`更大的优势。在 javascript 中，默认情况下显式使用 intend 是如此的不被重视。
*   最后，`{{action ...}}`决定了`button`被点击时会做什么(绑定的 [dom 事件](https://www.youtube.com/watch?v=G9hXjjHFJVs)可以被定制，但是这里没有显示)

### [检测](https://gitlab.com/NullVoxPopuli/emberclear/blob/master/packages/frontend/src/ui/routes/settings/acceptance-test.ts)

[![Testing comes for free out of the box with ember](../Images/6b975271ed197ae623ce386f3107c1b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N_pu42tO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DmDp4uQUUAEyK31.jpg)

Ember 应用程序自带一个异步感知测试框架，该框架使用真正的浏览器。没有 jsdom 或假的浏览器来引起对测试和现实之间奇怪的兼容性问题的担心。测试是真实的。

默认情况下，Ember 附带了 QUnit，但是如果您希望使用一套特定的测试工具，mocha 和 chai 也可以作为一种替代工具得到广泛支持。

### [依赖注入是必须的](https://gitlab.com/NullVoxPopuli/emberclear/blob/master/packages/frontend/src/ui/components/current-chat-name/component.ts)

[![Example dependency injection service consumption within a component](../Images/e055ee51bdbf2eb8d305f9e3635679ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ak0A3rDP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DmDquC-V4AAbLbi.jpg)

在我看来，依赖注入容器使得 *all* 成为一个框架完整性的差异。默认情况下， [Ember 自带依赖注入](https://guides.emberjs.com/release/applications/dependency-injection/)。不需要连接所有的依赖项。没必要什么都进口。不需要担心一个类有多少个实例，因为服务[单例](https://en.wikipedia.org/wiki/Singleton_pattern)。

Ember 依赖注入容器通过帮助管理应用程序级状态而无需任何粘合代码来提高生产率。

### [键盘无障碍](https://gitlab.com/NullVoxPopuli/emberclear/blob/master/packages/frontend/src/ui/routes/application/template.hbs)

[![Declarative Keyboard shortcuts](../Images/09b2c035ddd13559b65215aaf5709365.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P0N64wk---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DmDsqFaUwAAWNWI.jpg)

想要为您的应用程序添加键盘辅助功能吗？Ember 插件 [ember-keyboard](https://github.com/patience-tema-baron/ember-keyboard/) 提供了一套工具，允许你做一些普通的键盘可访问的任务，比如切换模式。

示例截图中的组件由这个无模板组件提供支持: [KeyboardPress](https://gitlab.com/NullVoxPopuli/emberclear/blob/master/packages/frontend/src/ui/components/keyboard-press/component.ts) 。

### 总结

在撰写本文时，一些特性可供早期采用者测试和实验使用。

* * *

但是，今天你可以尝试下面的*:*

 **   [装修工](http://ember-decorators.github.io/ember-decorators/latest/)(甚至[定制装修工](https://gitlab.com/NullVoxPopuli/emberclear/blob/master/packages/frontend/src/utils/decorators.ts))
*   [模块统一布局](https://github.com/emberjs/rfcs/blob/master/text/0143-module-unification.md)

这些是目前在*任何*应用中生产就绪的产品:

*   [打字稿](https://github.com/typed-ember/ember-cli-typescript)
*   [T2`<AngleBracketInvocation />`](https://github.com/rwjblue/ember-angle-bracket-invocation-polyfill)
*   [服务器端渲染](https://www.ember-fastboot.com/)
*   [服务人员](https://github.com/DockYard/ember-service-worker)
*   测试中的异步/等待

尽管这些特性中的每一个可能还没有得到官方的支持，但是仍然有很多人在产品中尝试和使用这些插件和特性。所以，如果你愿意花一些额外的时间来拥有*流血*的边缘，这是值得的；)

注意:如果你想看更多的未来:[余烬辛烷](https://github.com/emberjs/rfcs/blob/26c4d83fb66568e1087a05818fb39a307ebf8da8/text/0000-roadmap-2018.md)

* * *

简单介绍一下我——在 twitter 上，我是 [@NullVoxPopuli](https://twitter.com/nullvoxpopuli) ，我喜欢编程和人们提出的让开发更有效率、更有趣的抽象概念。我在 react 有 2.5 年的专业经验，在 Ember 有 4 年的休闲/爱好经验。我两个都喜欢。我更喜欢把每一个都用于特定的目的。我认为 Ember 比 react 更符合“全功能应用”的目的——尽管从技术上来说，react 和它的支持生态系统*能够*解决 Ember 解决的所有问题。有了 Ember，*真的很好，不用像在 react 生态系统中那样必须找出所有库之间的所有粘合代码。“通过约束提高生产力”是我对框架的座右铭。**
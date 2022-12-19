# 将 AngularJS 测试从 Karma 移植到 Jest

> 原文：<https://dev.to/abdoulayektr/migrating-angularjs-tests-from-karma-to-jest-45j1>

[![](../Images/7fdd945aa845d1cfef8a5c66050eb71d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G1i_O7As--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1495312365922-8a2e01490131%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3Dab7d6c2691cb1fbd878cb449c07bb8a1%26auto%3Dformat%26fit%3Dcrop%26w%3D1350%26q%3D80)

# 免责声明

这只是一篇关于我们如何将 AngularJS 测试移植到 Jest 的文章，它不是一个指南，当然也有一些我不会涉及的内容，但是我希望它能帮助到一些人。

在这篇文章中，我们假设你对使用 karma 的 javascript 单元测试配置有一定的了解，你听说过 Jest，并且你熟悉 NPM、Babel 和 Typescript。

这篇文章很好地解释了 Jest 和其他一些工具。

# 上下文

2017 年底，我们开始将整个前端代码库迁移到 Typescript 和 Webpack。在我看来，Typescript 是大型企业 javascript 代码库的天赐之物，我们很欣赏它。旧的设置维护起来越来越复杂，因为它是定制的，并没有真正遵循 javascript 的最佳实践。

我们的前端代码分为两部分:

*   我们在所有应用程序内部使用的可重用组件库
*   使用库并包含应用程序特定元素(视图、自定义组件、服务等)的代码....)

旧的设置要求我们构建库，然后构建依赖于它的应用程序，以及一大堆会让你做噩梦的东西；我们甚至在一些地方写了“黑魔法”。如果你曾经在代码库中遇到过这样的事情，那就不好了。我想就连你也同意我们必须改变一些东西，我们已经改变了。

回到主题，在用 Webpack 设置了我们的新构建并将我们的 js 文件转换成 typescript 之后；还有最后一件事要做:让测试再次工作！

啊，我没告诉你吗？除了前面提到的，我们的测试不再运行了，这是所有定制东西的副作用。

测试设置是基于*Karma/Mocha/Grunt/PhantomJS*的，只是不再工作了。我的第一反应是更新测试，使它们与 karma 一起工作，但事实证明这是一项比我预期的更艰巨的任务。karma 的插件系统
可能很酷，但这次它比任何东西都麻烦。我试过 karma-typescript(真的很好的 lib 和很棒的 maintainer，大声喊出来[@ mono unity](https://github.com/monounity))；起初一切顺利，大多数库测试都正常，但当我试图运行应用程序测试时，一切都乱套了。我们为 lib 使用了名称空间，karma-typescript 不太喜欢它，所以我打开了一个问题，这个问题已经被 **monouty** 解决了，但是我又遇到了其他问题。

鉴于所有这些问题，我不能让它按时工作，不得不暂时不管它，不幸的是，还有其他事情需要我的注意。快进到 2018 年 4 月 12 日，我和一个朋友参加了一个关于 TDD 和 BDD 的会议(一篇关于会议的法语文章，但有英语幻灯片[链接](http://blog.js-republic.com/meetup-js-star-tdd/))，他们用了 Jest(哇哦，他终于开始谈论它了)。我听说过这个框架，也读过这篇关于如何将它用于 angular 应用的好文章。这让我想起了我与因果报应的未竟事业。我向我的团队抛出了 Jest，考虑到我之前与 karma 的冲突，我们决定继续将我们所有的测试迁移(不知道，直到你尝试了才知道)。

# 迁移

## 因果报应配置

这里是我们使用的 karma 配置文件。第一个是应用程序，并没有试图让它工作。第二个是我们的内部库，也是我尝试过的。
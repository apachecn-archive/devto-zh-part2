# 周五前端:夏季版结束

> 原文：<https://dev.to/kball/friday-frontend-end-of-summer-edition-3oo0>

这是八月的最后一天，夏天即将结束，我确实感觉到了。天气开始变得凉爽，我的孩子已经开始上学，在经历了很多旅行后，我也开始回归正常的生活。

本周我没有视频给你们，但我想感谢所有回答我发出的调查的人。

你们中有很多人想要更多关于前端架构的信息，所以我将继续努力，在未来的新闻简报中加入更多这些资源，以及我们通常提供的 CSS 和 JavaScript 教程和其他精彩内容。

尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [具有 CSS 自定义属性的超级电网组件](https://css-tricks.com/super-power-grid-components-with-css-custom-properties/)

我喜欢这个演练，因为它不仅涵盖了如何使用 Grid 创建响应式组件，而且以一种前所未有的方式为我提出了利用父 click 的自定义属性来操纵组件版本的概念。创建一个超级优雅的方式来为有许多变化的组件编写 CSS！

##### [使用特征检测编写支持跨浏览器的 CSS](https://css-tricks.com/using-feature-detection-to-write-css-with-cross-browser-support/)

本文不仅讨论了我们可以实现 CSS 特征检测的许多不同方法以及这些方法是如何发展的(我的职业生涯无疑是从服务器端浏览器嗅探来设置主体类开始的)，还讨论了我们可能根本不需要特征检测的场景。不错！

##### [两个按钮的故事](https://medium.com/buildit/a-tale-of-two-buttons-e63185aefd5f)

关于组件模块化与 CSS 级联相比的好处，已经有很多文字，但是我还没有看到太多的文章真正一步一步地引导你如何最好地利用级联。这正是它的作用。

##### [使用 CSS 网格进行 Web 表单布局的好处](https://www.sitepoint.com/css-grid-web-form-layout/)

有趣的教程从几个角度来看。首先，它不是把布局作为一个整体来看，而是只关注那些烦人的表单。😝但是第二，它采用渐进增强的方法来应用网格——而不是实现网格然后提供回退，它采用基于浮动的布局，然后在可能的地方使用网格来升级它。

##### [CSS 网格再过 45 分钟！](https://www.youtube.com/watch?v=DCZdCKjnBCs)

我们行业中最优秀的在线教师之一韦斯·博斯的 45 分钟演讲。做得非常好——如果你喜欢视频学习，可以去看看。

#### JavaScript

##### [用 can activate/can activate 儿童卫士保护 Angular v2+路线](https://scotch.io/tutorials/protecting-angular-v2-routes-with-canactivatecanactivatechild-guards)

对路由使用保护是一种非常简单而强大的方法，可以处理诸如对应用程序的不同部分进行基于角色的访问控制之类的事情。这篇文章是关于如何在 Angular 中做到这一点的，但是你可以在 [Vue 路由器](https://router.vuejs.org/guide/advanced/navigation-guards.html#in-component-guards)中应用相同的概念，或者通过[一个带有 React 路由器的 NPM 包](https://www.npmjs.com/package/react-route-guard)。

##### [构建 React 项目—权威指南](https://blog.bitsrc.io/structuring-a-react-project-a-definitive-guide-ac9a754df5eb)

这是一个自以为是的观点，有几篇我不同意(它吹捧 [Bit](https://bitsrc.io/) ，我对此相当怀疑，但它在他们的博客上，所以我想他们必须做一些推广)。也就是说，在如何设计 React 项目方面，本指南中的大部分内容都符合我开始看到的共识，无论你是否同意所有内容，你都可能会从中学到一些东西。

##### [Vue 类组件的错误处理](https://blog.bitsrc.io/errors-handling-for-vue-class-components-2f152f7c7515)

一个有趣的二维教程。首先是因为它深入研究了 Vue 中的错误处理，但更重要的是因为它引入并使用了装饰模式，这是 JavaScript 语言的一个建议部分(目前处于正式成为正式语言的 4 个阶段中的第 2 阶段)。许多 JavaScript 开发人员并不熟悉装饰器，但是正如本文所示，它们可以为许多问题提供非常优雅的解决方案。

##### [理解 Javascript 中的执行上下文和执行栈](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)

有点难懂，但是如果你真的想了解你的工具是如何工作的，这将是有帮助的。深入到语言的语义，以及函数编译和运行的幕后实际发生的事情。

##### [在 React 和 Vue 中编写高度可重用组件的清单](https://hackernoon.com/checklist-for-writing-highly-reusable-components-in-react-and-vue-531f963864bd)

值得考虑的非常有用的想法列表。我不确定我会称之为清单，因为不是每个清单都适用于每个组件，但绝对是一个在设计组件时通读和思考的好清单。

#### 其他牛气

##### [SVG 滤镜游乐场](https://yoksel.github.io/svg-filters/#/)

超级酷的视觉游乐场，探索 SVG 过滤器给你的所有权力。到处玩，添加过滤器和拿走他们，与价值，最后复制出你的过滤器代码！当然，一切都在小猫身上展示，因为互联网是为猫准备的。非常好。我认为结合 feColorMatrix 和 feConvolveMatrix 是我最喜欢的…幽灵小猫！

##### [WorkerDOM:用 DOM 进行 JavaScript 编程的并发性](https://amphtml.wordpress.com/2018/08/21/workerdom/)

这是今年 JSConf 发布的最大的公告。US，一个来自 Google(特别是 AMP 项目)的库，它允许你模仿 DOM 并在 web worker 中进行 DOM 操作。对于执行大量繁重 DOM 操作的应用程序和框架，这将让它们将所有中间工作卸载到第二个线程，只使用主线程进行最终更新。非常非常酷！

##### [渐进增强的力量](https://medium.com/no-divide/the-power-of-progressive-enhancement-98738766b009)

这是一篇很棒的文章，它通过一种渐进的增强方法，带您了解考虑应用程序的过程。我对 web 组件和 Vue 组件是相似的这一点很感兴趣，它们给了你一种在自定义 HTML 元素中提供默认功能的简单方法。我在这方面做得不多，但是考虑到框架的服务器端渲染的驱动力，我想知道我们是否会开始看到“渐进式增强”体验的回归，即使是在一个 JavaScript 优先的环境中。有趣的...

##### [我的团队是如何不小心转到 TypeScript 并爱上它的](https://dev.to/progresstelerik/how-my-team-accidentally-moved-to-typescript-and-loved-it-1j80-temp-slug-918908)

我必须承认，我对打字稿这一潮流有点迟钝，但是最近有一个好朋友向我强烈推荐它，并且最近看到越来越多的人采用它。在 JSConf 的一次演讲中。美国，NPM 的首席运营官分享了一个令人震惊的数据:46%的 NPM 用户在使用打字稿。如果你想知道所有的宣传是关于什么的，这是一篇很棒的文章，它讲述了使用 TypeScript 的一些好处。

##### [巴别塔 7 发布](https://babeljs.io/blog/2018/08/27/7)

Babel 的一个新的主要版本是一件大事，虽然我认为越来越多的 Babel 配置正在由框架和样板处理，但它可能值得看看所有的变化并开始为新的东西做准备。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
# 反应钩子——让我们不要太依恋

> 原文：<https://dev.to/bnevilleoneill/react-hooks-let-s-not-get-too-attached-3l5p>

### React Hooks——不要太执着

[![](img/fad7d3ae55b2df29fbf06c284bf50e19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xwcj5kKZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/950/1%2A3BUc4O4T2cyE11h_HRxcXA.jpeg)

React 团队在 React Conf 18 上宣布了一个名为 Hooks 的新功能的提议后，React 社区出现了奇怪的反应:一系列描述如何使用它的教程，好像它已经在 React 核心中了。

被明确描述为[“新功能提案”](https://reactjs.org/docs/hooks-intro.html)的东西被一些人视为功能*公告*。显然，有太多的宣传和“让我们现在就重写一切”的态度，正如 React 团队的 Dan Abramov 所说:

body[data-twttr-rendered = " true "]{ background-color:transparent；}.twitter-tweet {margin: auto！重要；}

> 围绕 React 的内容产业是福是祸。令人惊讶的是，许多有才华的人创造了伟大的文章和例子。但是，它会对早期阶段的提案进行不必要的炒作，使人们感到失落和困惑。传播爱，不是炒作。
> 
> —  [@dan_abramov](https://twitter.com/dan_abramov/status/1057027428827193344)

函数 notify resize(height){ height = height？height:document . document element . offset height；var 调整大小= falseif(window . donkey & donkey . resize){ donkey . resize(height)；调整大小= true}if (parent && parent。_ resize iframe){ var obj = { iframe:window . frame element，height:height }；父母。_ resize iframe(obj)；调整大小= true} if(window . location & & window . location . hash = = = " # amp = 1 " & & window . parent & # window . parent . postmessage){ window . parent . postmessage({ sentinel:" amp "，type: "embed-size "，height: height}，" * ")；} if(window . WebKit & . window . WebKit . message handlers & . window . WebKit . message handlers . resize){ window . WebKit . message handlers . resize . postmessage(height)；调整大小= true}返回调整大小；}twttr.events.bind('rendered '，function(event){ notify resize()；});twttr.events.bind('resize '，function(event){ notify resize()；});if(父&父。_ resize iframe){ var maxWidth = parse int(window . frame element . get attribute(" width "))；if(500 < maxWidth){ window . frame element . set attribute(" width "，" 500 ")；}}

因此，让我们冷静一下，甚至不要为提议的 API 费心，并思考 React 是如何走到这一步的。

如果你现在更想看到代码并重构你的应用程序来使用钩子，那么[官方文档](https://reactjs.org/docs/hooks-intro.html)是一个很好的起点。

在关于 React 的[第一次会议演讲](https://www.youtube.com/watch?v=x7cQ3mrcKaY)中，皮特·亨特用*钩子*这个词来指代 React 的方法。组件，它允许用户提供定制的更新逻辑——让*挂钩到* React 的内部并调整它们的行为。新的特性提案以类似的方式引入了*钩子*——通过将你的代码挂接到 React 的引擎中，作为一种与 React 交互的方式。

这种使用函数而不是从类继承的想法与 React 的功能和声明精神非常契合。因为基于类的组件遭受了古老的香蕉-大猩猩-丛林问题的困扰，正如乔·阿姆斯特朗所描述的:

> 面向对象语言的问题是，它们拥有所有这些隐含的环境。你想要一个香蕉，但你得到的是一只大猩猩拿着香蕉和整个丛林。

用 React。组件，通常您需要的只是 state 或 ref，但是您得到的是一组您永远不会使用的方法，以及编写令人畏惧的 this 关键字的必要性(这是您知道什么的变位词)。基本上，钩子是一个抛弃类的提议，代替*从* React 继承，*钩子进入* React。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 不用上课怎么反应

React 基于类的 API 出现问题已经有一段时间了。ES6 类本身并不是真正的类(只是语法糖掩盖了原型继承)，它们没有很好地组合，并且这个关键字的使用产生了绑定问题，尤其是在执行异步操作时。

因此，对无类 React 开发的研究开始了。也许最值得注意的项目是[重新组合](https://github.com/acdlite/recompose)(文档中的第一个例子是[向一个功能组件](https://github.com/acdlite/recompose#lift-state-into-functional-wrappers)提供一个状态)，该项目现在因为钩子的引入而停止了。然而，重新组合大量使用了高阶组件模式，不幸的是，这在渲染树中创建了难以阅读和错误的层次。

另一个例子是[反应](https://github.com/reactions/component) [组件](https://github.com/reactions/component)，它使用了另一个非常流行的模式来使类组件更加可组合——渲染道具。这里的问题是，用户首先必须非常熟悉基于类的 React API，才能对如何使用 Reactions 组件有所了解。

也有一些很酷的项目使用新的 [ES6 代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)来管理状态，比如 [react-easy-state](https://github.com/solkimicreb/react-easy-state) 或者——以一种更轻量级和实验性的方式——[react-recreact](https://github.com/davidgilbertson/react-recollect)。我推荐关于后一个的[文章，它很好地探究了如何实现一个超级可读和自我解释的状态管理。可悲的是，对 ES6 代理的支持](https://hackernoon.com/a-different-way-to-manage-state-in-react-2d21dfb94482)[远非理想](https://caniuse.com/#feat=proxy)。

目前，Hooks 实现允许替换*几乎*所有基于类的组件的功能(componentDidCatch 和 getDerivedStateFromErrorare 的 Hook 等价物即将添加)，因此在添加它们以作出反应之后，搜索可以被取消。React 中的类不会很快消失，但是很明显 React 团队为图书馆设想了一个无类的未来。

### 只是其中一种成分，请

类和函数组件之间的二分法对用户来说是不必要的负担。我倾向于将函数组件视为成熟的类组件的一种轻量级的小兄弟——而且，这种区别与[表示和容器组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)模式非常吻合。但是当我想象现在学习 React 时，我认为在开始编写组件之前就必须决定是否使用状态(React 最基本的特性)是一件令人失望的事情。

这有点像[同步与异步函数](http://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/) : async/await 和 Promises 帮助我们逃离回调地狱，但是为什么程序员首先要关心一个操作是否是异步的呢？至少对我来说，钩子比类 Something extends React 更容易理解。组件—我如何知道在丛林中潜伏着什么，我必须将它与 setState 功能一起带来？

### 更具宣示性和功能性的未来

React 以其声明性而自豪，但是使用生命周期方法是非常必要的——一个经典的例子是 componentDidMount 和 componentDidUpdate 方法中的重复代码。使用钩子，这可以通过 useEffect 函数来实现(在官方文档中有一个很好的例子，说明钩子的声明式 API 如何击败生命周期方法的必要性[)。](https://reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update)

[https://medium . com/media/e7b 6 a 20 f 7292666 c 4548486 f 54583 fa 9/href](https://medium.com/media/e7b6a20f7292666c4548486f54583fa9/href)

有了无状态的功能组件，React 更多地朝着函数式编程的方向发展。钩子在这一领域更进一步，因为它们可以不用类而只用函数来构建全功能的 React 应用程序。

当该提案最终确定时，这将是自该库推出以来 React 应用程序开发方式的最大变化。今天的漂亮模式，如高阶组件或 Render Prop，将会显得古怪，甚至粗糙(因为它们引入了一个错误的层次)。熟悉的类 App 扩展 React。组件及其生命周期方法列表将成为过去，如果没有 JSX 语法，一段代码甚至可能看起来不像 React 组件。

但在此之前，我们不要太依赖于提议的 API，而要专注于使用酷的、稳定的特性编写代码。

#### 进一步阅读

*   [官方挂钩提案文件](https://reactjs.org/docs/hooks-intro.html)
*   [格式化 RFC](https://github.com/reactjs/rfcs/blob/hooks-rfc/text/0000-react-hooks.md)
*   [丹·阿布拉莫夫的介绍文章](https://dev.to/ben/making-sense-of-react-hooks-2in4-temp-slug-3153604)

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *
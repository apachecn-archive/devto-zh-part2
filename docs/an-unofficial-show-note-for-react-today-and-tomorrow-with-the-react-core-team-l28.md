# React 核心团队今天和明天的非正式演示笔记

> 原文：<https://dev.to/dance2die/an-unofficial-show-note-for-react-today-and-tomorrow-with-the-react-core-team-l28>

*照片由[莎朗·麦卡琴](https://unsplash.com/photos/rItGZ4vquWk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/show-note?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

刚刚听了 React 播客第 27 集[:React 核心团队](https://reactpodcast.simplecast.fm/27)的今天和明天。

关于新的 React 特性及其背后的“原因”,我们已经讨论了很多内容。

下面是突出讨论的“非官方”显示笔记。

## 目录

*   [懒惰](#lazy)
*   [悬念](#suspense)
*   [并发模式](#concurrent-mode)
*   [调度器](#scheduler)
*   [反应缓存](#react-cache)
*   [剖面图](#profiler)
*   [挂钩](#hooks)
*   [反应融合](#%E2%98%A2-react-fusion)

## 😪懒惰的

其目的是支持代码拆分，并将其嵌入 React 以促进更容易的使用和采用。

另一种可能是，人们会忘记拆分代码，将所有内容打包在一个文件中。

将它添加到 React 中使其易于使用，并且可以比用户 land 选项(如 [React Loadable](https://github.com/jamiebuilds/react-loadable) )更好地优化。

## 🌉焦虑

*   在此版本中仅支持立即回退。
*   v16.7 将启用显示前暂停。
*   尚不支持服务器端呈现(SSR)。

## ♒并发模式

#### 🔹先决条件

在选择加入之前，使用 React Strict 模式修复所有警告。

*[安德鲁·克拉克](https://twitter.com/acdlite)计划就此写一份“彻底的”文档。*

#### 🔹测试

*   您将能够像同步代码一样进行测试
*   React 团队计划提供一个*助手方法*来支持库/框架中特定的当前模式测试。

## ⏰调度程序

目的是在浏览器中解决一个[优先级队列](https://en.wikipedia.org/wiki/Priority_queue)协调*问题*(如下所述),因此它与 React 无关，是一个通用库。

问题是网站中不同的库有不同的优先级，为了解决这个问题，调度器在浏览器级别协调任务/事件。

**例如)** React 有自己的优先级队列调度系统。在处理浏览器其他部分的时候，哪个有自己的调度系统，哪个优先？

调度程序旨在解决这个协调问题。

React 团队正在与 Chrome 团队合作，以标准化调度。

#### ⚠ Note

*   由于传统的浏览器 API，如文本和键盘输入，某些事件仍将同步发生*。*
**   诸如跳转和点击等可预测的事件将连续发生*。***   而像鼠标这样的事件将被分批到低优先级队列中。**

 **调度程序通常是从开发者那里抽象出来的，因为它通常在框架级别上可用。

很少有理由直接使用它。

#### 🔹关于优先术语的建议

Dan 建议避免使用“高”和“低”来表示优先级，而是使用“正常”来表示优先级。

当一个优先级任务需要被阻塞(用户输入)时，最好使用术语“中断优先级”而不是“高优先级”任务。

***宋注**:好笑的是“优先”这个词已经暗示了“重要”。我们用“高”&“低”来指代优先级。*

*React 团队弃用`componentWillMount`，因为迈克尔杰克逊一直在滥用它(参考 [Sohpie](https://twitter.com/sophiebits) 的回应@ **25:05*** 😂 **j/k** 。

## 💰反应缓存

服务器两个用途

1.  人们可以在 v16.7 中试用悬念，直到 Relay 或 Apollo libraries 等供应商可以使用该参考实现实现缓存。
2.  对于一次性场景，如缓存图像或其他媒体文件，您不需要完整的缓存提供程序。

## 剖面图

一个浏览器开发工具，用于分析 React 组件提交到 DOM 的持续时间。

您仍然可以看到[墙时间(经过的实时时间)](https://en.wikipedia.org/wiki/Elapsed_real_time)，但是它更多的是关于花费在组件上的持续时间。

您仍然可以使用浏览器的内置分析器进行分析，但是使用 React 的[时间分片](https://github.com/facebook/react/tree/ce96e2df4d9f3367e07f25153e5282d4c0869bac/fixtures/unstable-async/time-slicing)和[错误边界](https://reactjs.org/docs/error-boundaries.html)，很难将 React 花费在“组件”上的时间缝合在一起。

分析器旨在提供一个更简单的组件提交时间视图。

将分析器建立在“提交”的基础上有助于您关注您创建的 React 组件**的性能，而不是 React 核心或浏览器的性能。**

 **#### 🔹作文

1.  显示“您”创建的组件的**名称**，以便于扫描。
2.  渲染一个组件花费的**时间，单位为毫秒**。
3.  **颜色**表示性能问题。

#### 🔹颜色；色彩；色调

<figure>[![](../Images/9323ea3a3b2d25cced7acbd6d03b7822.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--raDOte9p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/11/profiler.png%3Fw%3D1170%26ssl%3D1) 

<figcaption>剖面仪在行动</figcaption>

</figure>

1.  **灰色**:未渲染(如 [PureComponent](https://reactjs.org/docs/react-api.html#reactpurecomponent) 或 [shouldComponentUpdate()](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) 设置为 false)。
2.  **冷蓝**:快速提交。
3.  **亮黄色**:慢速提交。
    *   使用您的判断是否优化缓慢的性能，因为颜色显示相对差异(它可能是 5 毫秒或 300 毫秒，取决于情况或用户系统，快速/慢速笔记本电脑)。

#### 🔹跟踪 API

它使您能够按程序收集分析器数据。

*如果想了解更多关于剖析的知识，请参考* [*React 文档*](https://reactjs.org/docs/optimizing-performance.html#profiling-components-with-the-devtools-profiler) *。*

## ⚓钩

#### 🔹为什么？

很难在组件之间共享非呈现逻辑(嵌入在生命周期方法中)。

钩子提供了一种简单的方法，通过复制/剪切和粘贴将非呈现逻辑提取成更小的块，并在功能组件之间共享它们。

使用钩子会导致更简单的 API &更小/可读的代码库。

#### 🔹秘诀

受 [React 路由器](https://reacttraining.com/react-router/)文档的启发，有一个创建实用操作方法的计划。

***Sung 的边注:** [错误边界](https://reactjs.org/docs/error-boundaries.html)类型的文档会很好，因为每个人都只是复制/粘贴参考实现。*

#### 🔹限制

1.  不能在条件句中使用。
    *   想想这个。您没有有条件地声明/定义状态或生命周期方法。

*   因为钩子反映了这些特性，所以在一个条件中使用钩子是没有意义的。
    1.  前缀为“使用*”。
    2.  钩子的定义顺序很重要
*   就像你写同步代码一样。

#### 🔹约束的好处

1.  可以多次使用该逻辑(可共享)。
2.  支持更简单的逻辑组合。

*例如)`render`应该是一个使 React 能够利用它的纯方法。*

#### 🔹使用效果

与实现[组件卸载](https://reactjs.org/docs/react-component.html#componentdidmount) (cDM)、[组件卸载](https://reactjs.org/docs/react-component.html#componentdidmount) (cDM)和[组件卸载](https://reactjs.org/docs/react-component.html#componentwillunmount) (cWU)相比，支持更少的易错代码。

**例如**当你处理需要在`cDM`订阅而在`cWU`取消订阅的代码时，可能会导致泄漏或错误代码。

如果您的代码需要一个 ID 来取消/订阅，您需要考虑在 cDM 和 cWU 之间更新 ID 的情况，这需要在 cDU 中进行检查。

在更新过程中，每次都会调用`useEffect`来防止这种错误。

您可以使用第二个参数`useEffect`进一步优化它，使它只在指定的属性列表更新时运行。

#### 🔹方向

钩子看起来很难，但很容易使用，而电流反应看起来很容易，但很难使用。

## ☢反应过来融合

React 组件中 [AoT(提前)编译器](https://en.wikipedia.org/wiki/Ahead-of-time_compilation)使用[预装](https://prepack.io/)进行静态优化的探索。

## 👋离别赠言

这是 React 播客第 27 集[的“非官方”展示笔记:React 核心团队](https://reactpodcast.simplecast.fm/27)的今天和明天，因为我与该播客没有任何关系。

当我在一张[纸上](https://imgur.com/a/Rdfvv5A)写笔记的时候，我试着尽可能地将笔记分组。

如果你喜欢这个展示笔记，你会让我知道你是否发现了什么遗漏、错误或者需要更新吗？

最后，订阅 React 播客，在 twitter 上关注 React 核心团队(你可以在 React 官方播客[展示说明](https://reactpodcast.simplecast.fm/27)中找到)，因为他们在上面非常活跃。

*帖子[React 与 React 核心团队](https://www.slightedgecoder.com/2018/11/03/an-unofficial-show-note-for-react-today-and-tomorrow-with-the-react-core-team/)今明两天的非官方展示笔记最先出现在 [Sung 的技术博客](https://www.slightedgecoder.com)上。*****
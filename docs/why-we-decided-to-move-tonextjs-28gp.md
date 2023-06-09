# 为什么我们决定转向 Next.js

> 原文：<https://dev.to/giogiordano93/why-we-decided-to-move-tonextjs-28gp>

* * *

首先我们来谈谈 Next.js。

正如医生所说:

> Next.js 是一个用于服务器渲染的 React 应用程序的极简框架。

它有一些方便的功能。我见过的最简单的应用程序设置:

1.  定制 webpack 配置的可能性；
2.  默认情况下启用开发工作流的 HMR；
3.  自动代码拆分；
4.  懒装；
5.  路线导航被阻止，直到其初始属性被解决；
6.  简单的客户端路由；

因此，这简直太神奇了，它消除了配置 webpack、babel 和其他工具的大量工作，并加快了您的开发工作流程(相信我，看到页面上的编辑是即时的！).

* * *

哪些是项目特征？

*   用 Express + react 进行服务器端渲染；
*   路由:Express+react-路由器；
*   捆绑:带有 Babel 和一些其他定制插件的 Webpack
*   懒惰加载的模块；
*   基于路由的块(这对于延迟加载来说很难配置)；
*   造型:styled-components+scss；
*   状态管理:Redux +不可变；

该项目非常酷，我们有一个现代化的堆栈，其中一些功能是自定义实现，这让我们对每个功能有了更好的理解。但是……JavaScript 生态系统每天都在变化，更新一个定制实现所依赖的包(比如 webpack 插件)可能意味着重构整个特性。这让我们开始考虑移除自定义插件，并开始寻找解决方案。

我们怎样才能替代它们呢？使用一个框架或一组工具来处理项目的特性。
但是我们在 [brumbrum.it](https://www.brumbrum.it) 的需求不仅仅是一个一体化的捆绑解决方案。

经过一些分析，我们确定了我们的主要需求:

*   更清晰地实现路线；
*   懒惰加载模块的可能性，禁用它的 SSR
*   更快的开发工作流程；
*   正在删除自定义 webpack 插件；

* * *

有一天我读到了 Next.js，特性很有趣，能满足我们的需求，于是我们决定分析一下:

*   路线必须在 pages 文件夹中，该文件夹下的每个文件都是一条路线。每个路由都有一个 getInitialProps 方法，在它没有被解析之前，不会调用 render。这允许在一个点声明路由依赖关系；
*   Next.js 默认支持动态导入，增加了一些能力，比如 loader 和为 SSR 静态导入模块的可能性；开发构建是懒惰的，这意味着页面是按需构建的。此外，Next.js 将构建的页面保存在内存中，因此第二次请求相同的页面会更快；
*   Next.js 自带了自己的 webpack 配置，它拥有 React 应用程序所需的一切，此外，该配置是可扩展的；

恢复，我们可能已经迁移到 Next.js。迁移到 Next.js 的努力小于每次发布新的 webpack 版本时重构 Webpack 插件，并集成可配置的 lazy lazy loading 组件。

### 那么，我们来说说项目迁移中的惊艳之旅吧。

从路由器开始。每次我们必须增加一条路线，我们必须改变很多文件。花是清澈的，但是太脆了。此外，我们在 express routes 处理程序和 route 组件中有 actions ' dispatches。有两点我们声明了依赖关系，这还不算太糟，但是随着项目的发展，我们觉得有必要统一这些声明。

Next.js 默认启用文件系统路由，但是它不能满足我们的需要，所以我们禁用了它。我们可以使用 Express 作为服务器路由器，但是用 Node.js http 模块实现路由器并不太复杂。

我们需要的是一个 URI 模式匹配，而不是别的。因此，我们为服务器和客户端创建了一个混合路由器，将 URI 解析为路由组件路径。

现在，客户端 Next.js 提供了一个链接组件。我们需要做的是将浏览器中看到的组件路径和 URI 传递给组件。

* * *

下一步是将路由移动到 Next.js 页面，因此我们必须将路由依赖关系移动到`getInitialProps`方法。这使我们能够更清楚地划分路线的行为，在另一个文件夹中删除所有的视觉行为。
路由重构之后，脏活累活就开始了。

首先，我们必须调整传递给组件的属性，例如 Next.js 使用“asPath”作为位置，而 react-router 返回一个 URL 对象。做了这些，是 Redux 的时候了！耶！我没有别的期望。我不得不承认，首先解决方案不是很清楚，但是 Next.js 的例子帮助了我们。我们有不可变的，所以我们必须“transpile”状态，因为从服务器到客户端的转换返回的状态是一个普通的对象，我们需要一个不可变的状态(列表、集合、映射等等)。我不会解释它，我们已经使用了相同的实现作为例子。

完成了 props 重构和 Redux 集成，我们实现了布局，所以我们必须集成样式组件，这很容易，Next.js 允许编辑文档的标记，如头或体，所以我们需要在这里放置所有旧的样式和脚本。此外，我们曾经在“子组件”中编辑 head 标签，在我们使用 react-helmet 之前，现在我们简单地使用 Next.js.
的 head 模块，大部分工作已经完成，效果不错，开发工作流程加快，文件架构更加清晰，但一些奇怪的东西我们必须修补。为了让工作更有趣，我们必须安装一个桥接版本的巴别塔核心。

最后，最后一步:*生产构建配置*。
所有的 webpack 插件和配置都被删除了，是的，什么都没有保留。Next.js 取代了所有这些配置，并通过一些调整来处理我们的代码库。
现在，让我们用一些结果来结束这次神奇的旅行。

构建时间从 57 秒减少到 30 秒，服务器响应时间减少了约 900 毫秒(从 1 秒多，所以快了 90%)，CPU 和内存使用率降低，唯一的负面结果是页面加载时间增加了约 2 秒，但我们正在努力解决这一问题，这可能是任何事情。
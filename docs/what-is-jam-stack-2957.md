# 什么是果酱堆

> 原文：<https://dev.to/leomeloxp/what-is-jam-stack-2957>

这篇文章基于我在 2018 年 5 月 4 日在新泽西🇯🇪举行的当地网络发展会议上的一次演讲。尽情享受吧！

## 卡纸堆叠

[![A stack of jam tubs](img/60808ba69f167def05198b0512b95cb0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i_zyWL0s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rawgit.com/leomeloxp/jam-stack-talk/master/src/assets/stack-of-jam.jpg)

JAM 代表 ***Javascript、API 和标记*** ，一个首字母缩略词嵌套的缩写词。🤓

[jamstack.org](https://jamstack.org/)将 JAM 定义为*基于客户端 JavaScript、可重用 API 和预建标记的现代 web 开发架构。*

给 JAM 下定义并不能给我们足够的深度，但是它背后有什么秘密吗？

[![Michael Jordan looking disappoint at my bad puns](img/6d8747f771443cf6aaad05a47ccbc9e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uGXmHuEN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rawgit.com/leomeloxp/jam-stack-talk/master/src/assets/secret-stuff.jpg)

让我们来看看一个示例 JAM 网站的文件结构:

[![Screenshot of a file tree containing an index.html, style.css and script.js files](img/9c1f6e896078afc6b1917b3b326c8dc4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aFTV-XBf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rawgit.com/leomeloxp/jam-stack-talk/master/src/assets/jam-code.png)

如你所见，这背后没有魔法。只是一组我们都习惯于在项目前端看到的文件

[![Bugs bunny holding a bottle with "Secret Stuff" written on it](img/1ecf1de0bf3337e2f4d4b0bdd5ddbefe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--siCAnsud--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://rawgit.com/leomeloxp/jam-stack-talk/master/src/assets/secret-stuff-gone.gif)

基于这种文件结构，有些人甚至会认为 [Space Jam 的网站](https://www.warnerbros.com/archive/spacejam/movie/jam.htm)是一个 Jam 网站(剧透:它不是，它甚至不使用 CSS😂).

JAM 对我们如此有用的原因在于它的 Javascript 和 API 方面

Javascript + API =

[![Animated gif of bugs bunny showing his muscles in a hyperbolic way](img/a2f8b6c00c1b3410a4a450616f00dd74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XaQMgNSQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://rawgit.com/leomeloxp/jam-stack-talk/master/src/assets/bugs-bunny.gif)

如果我们看一个使用 [Gatsby JS](https://www.gatsbyjs.org/) 、[Rachio Smart spreader](https://rach.io/store/)构建的 JAM 网站的例子，我们可以看到，对于最终用户来说，没有即时的视觉变化，这个网站是静态构建的，仍然在 API 的帮助下提供完整的电子商务和结账旅程。

## 我为什么要给自己找麻烦？

即使最终用户不会真的有任何直接的优势，也有很多原因会鼓励你在下一个项目中考虑 JAM 方法。让我们来看看其中的一些:

### 负载速度

**没有数据库查询**:因为你的网站的标记是在构建时建立的，所以在每个页面请求时没有数据库查询。这既节省了服务器负载，也节省了总体响应时间。

**无模板解析**:根据你的栈，你的代码库中可能有一个模板文件，它被解析并用于构建 HTML，该 HTML 在每个页面请求时被发送回浏览器。对于静态站点，这种情况不会发生，这意味着您还可以通过从请求/响应流中去除这一方面来节省服务器负载和响应时间。

### 分离顾虑

**前端负责所有演示**

随着前端框架越来越受欢迎，功能越来越多，将网站/应用程序的呈现从任何后端框架中分离出来意味着您可以利用前端堆栈提供给您的所有功能，从而为最终用户提供更好的体验。

**集中/集中的 UX 设计**

同样重要的是，如果您不需要担心应用程序的后端如何处理其数据和业务逻辑，您可以将更多精力放在确保您的用户体验达到您想要的和您的最终用户期望的水平上，而不会受到后端解决方案的任何潜在限制。

### 内容更新的频率

我们多久更新一次网页？

这是经常被忽视的一点。对于我们的一些网页，内容很少更新，如联系页面，条款和条件，博客帖子。

您可能会在这些页面发布后很快更新几次，以修复潜在的打字错误或不一致，但一旦这段时间过去了，您就再也不会编辑该页面了，所以为什么不静态构建它，为您的服务器节省每次请求都必须编译该页面的麻烦呢？

**经常变化的内容可以使用 API**

如果您的页面仍然需要提供动态或时间敏感的内容，您可以使用 API 在页面加载后更新该内容，并可能在客户端缓存该内容，或者(次优)在页面加载后包含该内容。

### 无需到处实施

没有必要将整个应用程序一次全部移动到 JAM。

**JAM Stack 你的博客**

如上所述，某些页面在发布后不太可能更改，因此将它们设为静态将为最终用户提供更高的性能和更好的体验。

**用户认证区域可以保持不堵塞**

如果你的应用程序有某种需要保持动态的认证区域，当*阻塞*你的应用程序时，你可以跳过这一部分。

**逐渐迁移至堵塞**

本节的要点是，您可以慢慢地将您的内容迁移到静态构建，并在您发现应用程序中 JAM 方法的机会时引入更多 API 支持的动态内容。

### 配合服务人员、水疗和动画页面过渡

JAM stack 使我们能够更容易地将新的 web 工作流添加到我们的应用程序中，例如[服务人员](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)，单页面应用程序框架和页面之间的动画转换。

**离线支持**

通过服务人员，您可以在用户离线时向他们提供网站的部分或全部内容。这是一个很好的功能，但如果你的核心市场包括游客或互联网连接不佳的用户，这一功能尤为重要。

**在构建时向服务人员推送更新**

正如我前面所说的，有些页面在发布后很少会改变内容，但是当它们改变时，您可以使用服务人员根据您的需要重新验证您的内容。这将确保您的最终用户在其设备上始终拥有最相关的内容。

**页面过渡让 UX 变得圆滑(就像在本地应用上一样)**

这可能看起来不像是静态和基于 JAM 的网站的关键特性，但是如果你花一点时间想想一些本地应用程序在它们的不同区域之间的转换有多好，想到我们可以在基于 web 的应用程序上做同样的事情真的很令人兴奋。如果你需要一些灵感，请看莎拉·德拉斯纳的[页面转换](https://page-transitions.com/)的例子。

### 缓存

最后，缓存静态构建的站点甚至比动态站点更有效，因为您可以缓存站点的所有方面，并且根据您的内容类型，您的缓存可以设置为非常长的到期日期。

对于站点中任何需要动态的部分，您可以使用 JAM 的 Javascript 和 API 方面在页面加载后拉入内容。

## 告诫

正如我们领域中的大多数事情一样，在设计基于 JAM 的应用程序时，也可能需要权衡，或者至少需要记住一些事情。我已经在上面提到了这些，但是为了将所有这些都放在一个部分中，在构建 JAM 应用程序时需要考虑一些警告:

**电子商务和极其时间敏感的内容**

如果您的内容对时间非常敏感，并且严重依赖后端解决方案，JAM 可能不是最佳选择。避免这种情况的一种方法是使用 API 来更新前端的内容，但这可能并不总是可行的。

**必须实施 SSR**

这背后有许多原因，但我们总是建议您至少在构建时为您的内容实现某种服务器端呈现逻辑。

**确保没有相关的 JS 支持和 SEO 最佳实践**

我知道这可能听起来有点过时，但是实现无 JS 支持可能是非常相关的，这取决于您的目标受众或者您希望向其交付应用程序的用户的百分比。值得一提的是，与完整的应用程序相比，没有 JS 支持可能会稍有限制，但至少要给没有 JS 的用户某种一致的体验，这一点很重要。

根据网站或应用程序的类型，你也要确保你在搜索引擎优化方面覆盖了你的领域。如果没有人能发现，拥有一个伟大的网站可能毫无意义。

**您应用程序的认证区域**

你的站点的认证区域可能并不总是与 JAM 方法兼容。同样，API 可以在这方面帮助你，但是这取决于你对每一个单独的情况做出决定。

关于这一点的一些建议是对你的站点的 chrome 进行 SSR，并且可能添加一些占位符标记来表明用户特定的内容仍然是用 AJAX/API 获取的。

## 怎样才能加入果酱栈炒作列车？

[![Station officers pushing people into a crowded train](img/079046310de6cb3cab5de8665885d5e1.png)T2】](https://i.giphy.com/media/QgRkEgibQp65q/giphy.gif)

既然你已经看到了果酱的一些优点和缺点，你可能想自己尝试一下。有很多方法可以建立一个基于 JAM 的网站，其中大多数都有某种静态网站的感觉。

我整理了一个列表，里面有一些你可以尝试的选项，但这绝不是一个全面的列表，只是我过去遇到或使用过的东西。

[**React JS**T3】](https://reactjs.org/)

React 可能是目前使用最广泛的前端框架，如果你了解 React，你可能知道所有这些选项。无论哪种方式，都有一些工具可以用来构建 JAM stack:

*   [create-react-app](https://github.com/facebook/create-react-app) :伟大的零配置 react 应用程序架子工，一旦需要更多自定义功能，就有可能*弹出*您的应用程序。
*   GatsbyJS:React 的静态站点生成器，内置 GraphQL 数据层，能够在构建时从 React 组件构建静态 HTML。
*   Next.js :一个支持静态站点的服务器渲染 React 应用的框架。

[**vista . js**](http://vuejs.org/)

Vue 也是一个前端框架，越来越受欢迎，被采用的速度也非常快。如果您正在使用 Vue，以下是一些可以用来构建 JAM 应用程序的工具:

*   [Nuxt](https://nuxtjs.org/) :通用 Vue JS 应用框架，常说是 Next.js 的 Vue 等价物，
*   [Vue press](https://vuepress.vuejs.org/):Vue 静态现场发生器。
*   eagle.js :一个用 Vue.js 构建的可黑客攻击的幻灯片框架(用于创建我的演讲的幻灯片🤓).

**其他选项**

*   [Jekyll](https://jekyllrb.com/) :基于 Ruby 的静态站点生成器，广泛用于 [GitHub 页面](https://pages.github.com/)。
*   [Hugo](https://gohugo.io/) : Go lang 基础静止站点发生器。
*   StaticGen : StaticGen 提供了一个静态站点生成器列表，您可以根据自己的偏好或需求对其进行排序和过滤。
*   构建您自己的: [NIH 综合征](https://en.wikipedia.org/wiki/Not_invented_here)除此之外，如果您有一组您需要的非常具体的特性，您总是可以构建您自己的静态标记，并以适合您的业务和需求的方式将其与您的 API 集成。

## JAM 友好的后端解决方案？

JAM stack 的另一个重要方面是确保您的后端解决方案与前端框架很好地集成，并提供您可以从应用程序的前端/客户端利用的 API。可以帮助您做到这一点的一些框架和服务示例如下:

*   无头 WordPress:使用 WordPress 作为你的 CMS 并不意味着你需要用它来直接生成你的网站的标记。API-first/Headless WordPress 越来越受欢迎，帮助你入门的插件和库就在那里。
*   Netlify CMS :一个基于文件的 CMS，可以直接插入到你的版本控制和构建工作流中。
*   Prerender :你的应用程序的 SEO 友好插件，确保你的网站能被搜索引擎正确抓取
*   你当前选择的框架:这对于所有的 CMS/框架来说可能并不明显，但是你有可能已经建立了一个 JAM 网站并与你当前的后端解决方案集成，可能通过使用 REST 和/或 GraphQL。

## 结论

感谢您阅读我的帖子。JAM stack 非常棒，我相信这是我们最有效的前进方式。当你开始下一个项目时，考虑试试 JAM stack，或者考虑用 JAM 重建你当前设置的一部分，你不会失望的。😉

可能有很多事情我没有谈到(因此不是这篇文章的一部分)，请在评论中随意提及我可能错过的事情。

我演讲的一些灵感来自 JAM Stack 上的另一篇 [dev.to](https://dev.to) 帖子，我推荐你去看看:

[![lauragift21](img/f9c65deb56ce1e30e77570325bbc4634.png)](/lauragift21) [## 什么是 JAMstack，为什么您应该尝试它

### 礼物 Egwuenu Apr 16 ' 18 3min read

#jamstack #staticsites #netlify](/lauragift21/what-is-jamstack-and-why-you-should-try-it-5f8h)

我演讲的幻灯片可以在这里看到[，你也可以查看](https://leomeloxp-jam-talk.netlify.com/) [Github 回购的幻灯片代码](https://github.com/leomeloxp/jam-stack-talk)。干杯🤓
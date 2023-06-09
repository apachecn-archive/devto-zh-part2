# 浏览 Docusaurus、Docz 和 React-Static

> 原文：<https://dev.to/swyx/a-glance-through-docusaurus-docz-and-react-static-47in>

DocGens/[SSG](https://www.staticgen.com/)很难评估，因为它们在表面上看起来都很相似，你必须真正投入时间才能理解它们之间的重要特性和差异。我非常了解[Gatsby](https://gist.github.com/sw-yx/09306ec03df7b4cd8e7469bb74c078fb)，并且使用过 Hugo/Jekyll，并且想看看最近出现的一些基于 React 的新一代站点生成器(是的，3 个中的 2 个有特定的文档焦点，我不介意)。

# Docusaurus

[Docusaurus](https://docusaurus.io/) 非常专注于文档用例，用于脸书每个主要项目的文档——除了使用 Gatsby 的 React。安装和启动速度非常快，但基本演示乍一看并不令人印象深刻，因为它实际上专注于 blog 和 docs 的 Markdown，一些组件在`/core`中，页面在`/pages`中。堪比拘束的盖茨比。配置东西的`siteConfig.js`和`sidebars.json`选择感觉有点难看/武断，但并不重要。

**黑仔特色**:我觉得 Docusaurus 的闪光点在[搜索](https://docusaurus.io/docs/en/search)、 [i18n/l20n](https://docusaurus.io/docs/en/translation) 、[版本化](https://docusaurus.io/docs/en/versioning)。在这里，它受益于一个非常集中的用例以及关于合作伙伴/问题域的固执己见的选择——它是最少的配置。我认为版本化文档是一个杀手锏。

Docusaurus 还附带了一些[提供的组件](https://docusaurus.io/docs/en/api-pages#provided-components)，这些组件在 docs 中很有用，并且附带了一些内置的主题(基本上是可以通过`siteConfig.js`设置的颜色)。Prismjs 还包括零设置语法高亮。一个有趣的模型，它提供了很多便利，并且不会限制您添加自定义的 React 组件或 CSS。

部署故事也是一个不错的接触，特别是提供了一个 [publish-gh-pages](https://docusaurus.io/docs/deployment#deploying-to-github-pages) 脚本，从经验来看，这是一个棘手的问题。当然，Netlify 也在场。如果你`yarn build`查看构建文件夹，你甚至可以看到他们为你准备了一个`sitemap.xml`，非常可爱。(博客还附带了 RSS 的`atom.xml`和`feed.xml`)。

Docusaurus 本身有令人印象深刻的良好记录，我想这可能并不令人惊讶，但我仍然很欣赏。

**缺点？老实说，我很难想出真正的缺点。没有插件系统，所以它不可扩展。config/sidebar 有点不直观，如果你需要使用更多的构建过程，比如 SASS，那就靠你自己了，但老实说，这些只是吹毛求疵。印象极其深刻。**

制作者 : Docusaurus 是由脸书制作和赞助的——似乎是 Eric Nakagawa 和 Joel Marcey 带头发起的，你可以通过[他们的 Twitter](https://twitter.com/docusaurus/following) 或[他们的 commits 查看团队的其他成员。](https://github.com/facebook/Docusaurus/graphs/contributors)。

* * *

# Docz

[Docz](https://www.docz.site/) 的演示很流畅——认真去看视频吧。价值主张是显而易见的——你可以把它放到一个现有的项目中，通过把 [`mdx`文件](https://www.docz.site/introduction/writing-mdx)放在你的 JSX 文件旁边来生成文档。

这确实意味着 Docz 比 Docusaurus 更受 React 生态系统的约束(尽管他们正在致力于 Preact/Vue 支持)，但这种权衡再次支持(可选)使用 MDX 的能力，这非常好。连同[提供的组件 API](https://www.docz.site/documentation/components-api)，特别是`Playground`和`PropsTable`组件(这是很棒的想法！！)它使得记录一个 React 组件库变得极其简单。但暂时除此之外没做多少别的:)

**黑仔的特点是**:零配置 MDX 文档，以及支持类型脚本的`Playground`和`PropsTable`组件。

仅仅通过做`yarn docz dev` *就能启动 docz 服务器，甚至不需要添加 npm 脚本*，这是一个非常非常好的体验。我甚至不知道你能做到这一点！

我觉得我不是 Docz 的目标受众，因为 Docz 上大约一半的文档都花在了主题化上，而我并不特别关心这些。如果你需要的话，我想这很酷。

插件的故事有很大的潜力，已经有很多记录良好的生命周期挂钩。虽然没有太多值得夸耀的插件，但这个项目仍然非常年轻(仅在 2018 年 6 月 11 日宣布)。

一个非常好的触摸是控制台输出-这东西看起来设计师-非常性感。

**弊？**:它确实最适合在内部记录 React(JS 或 Typescript)组件库。与 Storybook 类似，它不能帮助你生成一个好看的登陆页面或博客或任何东西，它实际上是一堆放在一起的 mdx。所以如果我这么做，这是我的首选，而不是别的。`yarn docz build`也没有构建一个静态站点，它只是制作了一个生产 JS 包来服务于`index.html`。(在我将 Docz 纳入组合之前，我并不知道这一点，现在为时已晚)。

这种极度关注为 JSX 组件生成文档的做法的好处是，Docz 实际上可以与其他静态站点共存，并且您仍然可以从中获得价值。假设你的 Docusaurus 站点有一个可重用的组件库；您可以使用 Docz 来帮助开发并控制这种情况。

制作人 : [佩德罗·纳克](https://twitter.com/pedronauck?lang=en)，他已经做了一堆其他有趣的事情，比如[反应-采用](https://github.com/pedronauck/react-adopt)。绝对值得关注。

* * *

# 做出反应——静止

在我看来，这是当前盖茨比的替代方案，所以我期望比上面两个有更多的自由度，也许更复杂。(它也老了一点，已经是 6.0.0 版了)

首先要注意的是[阶梯式 CLI 体验](https://react-static.js.org/docs/#quick-start)。这当然是一个小接触，但仍然比盖茨比高一级。在 CLI 中有一堆[非常有趣的模板](https://react-static.js.org/docs/#examples-and-templates)，这很好。我的目光被“动画路线”吸引了，因为我知道这是一场与 SSGs 的斗争。

作为一个对 Gatsby 的文档有贡献的人，我只想说:React-Static 的文档写得非常好，尤其是对核心概念的介绍。动态路由也更容易。[模板生成](https://react-static.js.org/docs/concepts/#pagination)感觉有点类似于`gatsby-node.js`里面的 Gatsby 模板，但是可能需要争论的文件更少。GraphqQL 不再是一等公民，我将不得不摆弄数据获取，看看我对此有何感想。

**黑仔特色**:这很难讲清楚，但 React-Static 以其所缺乏的东西而闻名，这些都是违反直觉的好特色。[所有的数据获取](https://react-static.js.org/docs/concepts#code-data-and-prop-splitting)都在 [`static.config.js`](https://react-static.js.org/docs/config) 内完成，没有神奇的 graphql 组件，见鬼根本没有 graphql。数据通过集成的[路由数据](https://react-static.js.org/docs/components#routedata)传入。没有一堆其他文件需要处理，生命周期挂钩也少得多。它不支持插件，所以大概要“插入”你只需写一些与`static.config.js`兼容的东西。总的来说，魔法少了很多，直到现在我才知道我有多喜欢它。谁知道这是否是正确的水平，但对于 80%的用例来说，这肯定是简单性和功能性的适当平衡。

不错的尝试:[Preact](https://react-static.js.org/docs/concepts#using-preact-in-production)、[组件](https://react-static.js.org/docs/components) (react 路由器组件增强了静态站点关注，包括数据和 scrollto)和[方法](https://react-static.js.org/docs/methods)

**缺点？**:缺少插件生态系统意味着需要做更多的定制工作来设置/配置数据源，为页面生成提供数据。启动器/模板相当于一堆样板文件，不太容易组合或重用。我想减少魔法的代价是更多的工作来弥补。

**制作者**:[喷嘴的坦纳·林斯利。出身很重要，你绝对应该看看坦纳的](https://nozzle.io/about) [Next vs Gatsby 文章](https://medium.com/@tannerlinsley/%EF%B8%8F-introducing-react-static-a-progressive-static-site-framework-for-react-3470d2a51ebc)来理解他为什么做出 React-Static。(比我肤浅的调查要深入得多——例如，他注意到了每条路线运送的 JS，这是我绝对没有注意到的)

* * *

特别提及:React static sitegens 上其他值得注意的资源:

*   [海螵蛸](https://cuttlebelle.com/)(因展示我最喜欢的动物而加分)
*   [static-react](http://jxnblk.com/writing/posts/zero-configuration-react-static-site-generator/)2016 年 react 对 SSG 的探索(此处有[的操作指南](http://jxnblk.com/writing/posts/static-site-generation-with-react-and-webpack/)
*   [现象](https://phenomic.io/)
*   [Nextein](https://nextein.now.sh/) -基于 next.js
*   狮子座

* * *

# 整体

我开始这项研究时，对它们各自的功能只有一个模糊的概念，我认为选择其中任何一个都是不负责任的。它们是苹果、桔子和西红柿，它们都以独特而有趣的方式解决不同的问题。这个世界足够广阔，可以有各种各样的解决方案来解决各种各样的问题，我欢迎我的工具箱中增加这些东西。

我会注意到，可能对我来说最大的正面惊喜是 Docusaurus，因为我不知道文档中的这些难题在 Docusaurus 中有多简单。
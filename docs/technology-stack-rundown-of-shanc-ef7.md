# SHaNc 的技术堆栈缩减

> 原文：<https://dev.to/dance2die/technology-stack-rundown-of-shanc-ef7>

*SHaNc 的[工艺栈崩溃的帖子最先出现在](https://www.slightedgecoder.com/2018/06/02/technology-stack-rundown-of-shanc/)[微边编码器](https://www.slightedgecoder.com)上。*

在[上一篇](https://dev.to/dance2die/introducing-shanc--a-static-hacker-news-clone-3ldj)中，我介绍了 [SHaNc](http://shanc.netlify.com/) ，一个静态黑客新闻克隆体。
我将谈论以下使用的技术及其原因。

1.  [盖茨比](https://www.gatsbyjs.org/)
2.  [样式组件](https://www.styled-components.com/)
3.  [Netlify](https://www.netlify.com/)
4.  [Azure 功能](https://azure.microsoft.com/en-us/services/functions/)

### 🏭架构–重新定价

这里是前一篇文章中架构的回顾。

[![architecture](img/f32fa042b2c28a8c671578245154d5d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vr2hK1AF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/05/architecture.jpg%3Fresize%3D1024%252C653%26ssl%3D1)

Gatsby 用于生成静态页面，然后部署到 Netlify。而 Azure 函数让 Netlify 通过 build webhook 重新构建静态页面。

下面我将逐一介绍每个组件。

### 📜盖茨比

#### What❓

这是一个静态站点生成器，使用 ReactJS(以下简称 React)作为 UI 模板引擎。

#### 为什么？🤔

有很多静态站点生成器，我选择它有两个原因。

1.  我对 React 的熟悉——它帮助我快速创建了一个 MVP ( [最小可行产品](https://en.wikipedia.org/wiki/Minimum_viable_product))
2.  从优秀的[文档开始](https://www.gatsbyjs.org/docs/)

#### 如何？🔨

盖茨比通过一个公开可用的[黑客新闻 API](https://github.com/HackerNews/API) (以下简称 HN API)获取黑客新闻数据，然后将其与 React 模板合并以创建一个单独的 HTML 文件。

#### 挑战🤔

Gatsby 只能通过 [GraphQL](https://graphql.org/) 数据源获取外部数据(参考[用 GraphQL](https://www.gatsbyjs.org/docs/querying-with-graphql/) 查询数据)。

我想用[瑞安·弗洛伦斯](https://twitter.com/ryanflorence/)的[盖茨比-source-firebase](https://github.com/ryanflorence/gatsby-source-firebase) 作为 firebase 上托管的 HN API。

Ryan 的插件需要 Firebase 凭证才能通过，但 HN API 是公共的，因此不需要凭证。

由于默认 GitHub 许可问题，我无法分叉，更新，&发布我自己的插件。

所以下一个选项是创建一个[定制的 GraphQL 数据源](https://github.com/dance2die/SHANc/blob/master/gatsby-node.js)，它公开获取的 HN 数据。

*(我将在以后的博客中更详细地介绍它是如何实现的)*

### 📜样式组件

#### What❓

这是一个 React 库，可以让你在 React 中嵌入 CSS([Styled Components Home](https://www.styled-components.com/))。

#### 为什么？🤔

出于好奇，我开始使用它。

但后来我意识到，它使得使用熟悉的 CSS 语法轻松创建简单的风格化 React 组件(React 需要稍微不同的语法，`class`属性使用`className`，并且需要使用`-`将 CSS 样式骆驼化；`lineHeight`而不是`line-height`。

我发现的另一个好处是，我可以命名容器，使代码可读(而不是使用通用的“div”或“span”)和有意的。

你可以看到`storiesComponents`是由一个带有等级、内容的故事组成的，它包含了主体和元数据。
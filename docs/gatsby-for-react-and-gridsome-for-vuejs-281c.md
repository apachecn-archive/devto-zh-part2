# React 的 Gatsby 和 Vue.js 的 Gridsome

> 原文：<https://dev.to/mittalyashu/gatsby-for-react-and-gridsome-for-vuejs-281c>

你是 Vue.js 吗😍情人？我相信你也会喜欢🤪这个新的静态站点发生器💪🏻的 Vue.js + GraphQL = [Gridsome](https://gridsome.org) 。

[![Image](../Images/33b3e6ba170bc69aee2ab6095c7aff4a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d5XgJsJ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/878bujtx44yni3oub1hf.gif)

在今天的日期，这个项目只有 3 个月，它的发展非常快。

[![Github stars](../Images/8b9689cdd6b2b43ea0e4606aa2fd87c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WgbrZn8I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/shkhodh35k7m7jvz0onk.png)

**但是...什么是 Gridsome？**

Gridsome 是 Vue.js 生态系统中缺失的一块。Gatsby.js 为 React.js 做的事情完全改变了游戏规则😎我们如何建立网站。React.js 非常优秀，但我们认为 Vue.js 对于大多数 web 设计人员和开发人员来说更容易上手。Gridsome 是盖茨比的 Vue.js 替代品。

使用 **Gridsome** ，您可以为所有连接的数据源获得一个**通用 GraphQL 层**。这就像是你的网站数据的单一来源，可以在任何页面或组件中使用。连接到任何 CMS 或 API，如 Google 电子表格、Airtable、Instagram Feed、本地 markdown 文件等。

[![Features of gridsome](../Images/7dbb9b9c949a12818b22f7577a1c1f1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1wuJ3lSb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t3eqausptyql5upaaddf.png)

让我们看一些代码👨🏻‍💻。

连接到任何 CMS、API 或本地文件，如 Markdown，并从 GraphQL 层访问它。将数据插入任何。只包含您需要的字段的 vue 文件。

```
<template>
  <div>
    <h2>Latest blog posts</h2>
    <ul>
      <li v-for="edge in $page.allWordPressPost.edges" :key="edge.node._id">
        {{ edge.node.title }}
      </li>
    </ul>
  </div> </template> 
<page-query>
query Blog {
  allWordPressPost (limit: 5) {
    edges {
      node {
        _id
        title
      }
    }
  }
}
</page-query> 
```

你不需要了解 GraphQL 或 Vue 就可以开始使用 Gridsome。有几个初学者工具包可以帮你轻松解决问题。

GraphQL 层和所有数据都可以在本地 GraphQL 平台上访问。当一个 Gridsome 开发项目`gridsome develop`正在运行时，操场通常位于`https://localhost:8080/___explore`。

你听说过谷歌灯塔吗？

不是另一个，实际上，是这个😅

[![Google lighthouse](../Images/c86ff92844fec7ae80b97c5ad0af5355.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8PxUr6De--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.keycdn.com/blog/wp-content/uploads/2017/06/lighthouse-test-report.png)

Gridsome 的目标是创建一个框架，让您能够构建优化的“开箱即用”的网站它遵循了谷歌的 PRPL 模式。使用 Gridsome 制作快速网站不需要成为性能专家。你的网站在 Google lighthouse 上开箱即用，几乎得到满分。以下是 Gridsome 负责的一些性能步骤:

*   图像压缩和延迟加载
*   CSS 和 JS 缩小
*   代码分割
*   HTML 压缩
*   关键 CSS(插件)
*   完全 PWA 和离线支持(插件)

我们只触及了表面，未来还会有更多的东西等着你。

# 贡献给网格体

有两个兄弟在做这个项目，所以任何贡献都是非常欢迎的。

> 让我们承诺让网站开发过程对开发者来说更快更容易。

[![Gridsome Footer](../Images/267e9829fa4eb406e8c5113efb90caff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8r-eq5ky--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/crl5la6m1qu5pjrb53sk.png)

# 链接*(来源&参考)*📰

*   [https://gridsome.org](https://gridsome.org)
*   [https://github . com/grid some/](https://github.com/gridsome/)
*   [https://discord.gg/7znJUkH](https://discord.gg/7znJUkH)

# 条款和条件😂

您同意一旦您阅读了这篇文章，您肯定会尝试使用 Gridsome 并喜欢这篇文章。

觉得这篇文章有趣？考虑给我买杯咖啡。
[![Buy Me A Coffee Button](../Images/8741c1bafaddaa2b9ee9d5b1c31c3e03.png)T3】](https://www.buymeacoffee.com/mittalyashu)
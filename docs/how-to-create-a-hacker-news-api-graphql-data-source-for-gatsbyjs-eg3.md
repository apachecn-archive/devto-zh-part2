# 如何为 GatsbyJS 创建黑客新闻 API GraphQL 数据源

> 原文：<https://dev.to/dance2die/how-to-create-a-hacker-news-api-graphql-data-source-for-gatsbyjs-eg3>

在[之前的帖子](https://dev.to/dance2die/technology-stack-rundown-of-shanc-ef7)中，我为 [SHaNc](http://shanc.netlify.com/) 介绍了一个科技栈。

我将更详细地介绍如何为 [GatsbyJS](https://www.gatsbyjs.org/) 创建黑客新闻 [GraphQL](https://graphql.org/) 数据源。

## ❓为什么？

因为 GatsbyJS 只能通过 GraphQL 端点查询数据。
*指[用 GraphQL](https://www.gatsbyjs.org/docs/querying-with-graphql/) 查询。*

## 🤔假定

我假设你熟悉 JavaScript [承诺](https://developers.google.com/web/fundamentals/primers/promises)，以及[异步](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) / [等待](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)。

## 💭术语&概念

让我们确保我们在同一页上。

1.  [GraphQL Source](https://www.gatsbyjs.org/docs/create-source-plugin/)——这是 GatsbyJS 可以通过 graph QL 查询的数据。
2.  **节点**–一个节点被称为“模型”(根据文档)，它是数据看起来的形状(不是节点)。JS)。
3.  Gatsby-node . js–这是您定义 GraphQL 源代码的地方，它位于项目根目录中。

现在我们已经明确了一些术语和概念，让我们来回顾一下黑客新闻 API。

## 🔍黑客新闻 API 概述

官方的[黑客新闻 API](https://github.com/HackerNews/API) (以下简称“HN API”)公开了“顶级”、“最佳”和“新”故事的顶级端点。

顶级端点仅返回 id，没有其他相关数据。

```
Calling "https://hacker-news.firebaseio.com/v0/topstories.json" 
returns an array of story IDs 

[9127232, 9128437, 9130049, 9127243, 9128571, ..., 9120990] 
```

Enter fullscreen mode Exit fullscreen mode

因此，您需要调用从顶级端点返回的每个故事 ID。这不是一个最佳设计，HN 团队也承认了这一点。
*但我感谢 HN 团队为他们的故事提供了一个公共 API。*

记住这一点，让我们继续创建一个源。

## 🏙实施步骤

现在，让我们看看如何按照下面的步骤将 Hacker News API 包装成一个节点，从而将它转换成 GraphQL 源代码。

1.  从 HN API 获取所有顶级故事 id
2.  创建源节点
3.  提供给盖茨比

### 💡从 HN API 获取所有顶级故事 id

让我们从 HN API 获取所有顶级故事 id。
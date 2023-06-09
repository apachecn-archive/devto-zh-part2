# 为我的 github 页面使用 API 优先的 CMS？

> 原文：<https://dev.to/danielordonez/using-an-api-first-cms-for-my-github-page-10lj>

**重要的**
这是一个我不再在我的 github 页面上主持的项目，但由于历史原因，我决定离开这个帖子。我采纳了我自己的建议，换了一个盖茨比静态网站，反正最后可以随意阅读和评论。

> 一段时间以来，我一直想有一个博客来张贴我当时正在学习或发展的任何东西。

博客有很多很酷的选择，但对于 2018 年的开发者来说，我不认为你会用 Squarespace 甚至 Wordpress 来设置。你可能想完全控制你的博客，让它成为一个不需要数据库的静态网站，甚至可能在 markdown 上写文章。

如果前面提到的描述适合你，我强烈建议你试试 GatsbyJS，从这个关于如何在大约 10 分钟内建立一个 gatsby powered 博客的教程开始。

对我来说，我想要一个简单的老式 index.html，在 github 上发布 css 和 js 文件，minified 和 vuejs 驱动，但没有 webpacky，只需要推一次就完全忘记了，然后在网上写我的内容，让整个事情独自工作。这里可以看到[。](https://github.com/daniel-ordonez/daniel-ordonez.github.io)

## Tipe.io

**下一代 API-首个 CMS**

> 使用强大的编辑工具创建您的内容，并使用 GraphQL 或 REST API 从任何地方访问它。不要让你的 CMS 决定你如何构建你的应用。

😎听起来很棒，对吧！？

> 但是，你以前可以使用类似 Wordpress API 的东西，是什么让它如此特别呢？

简而言之，有了 Wordpress，你就受限于请求的格式和检索到的数据的结构(比如预格式化的 html)。

使用 [Tipe.io](https://tipe.io/) 你可以定义你想要的文档结构，比如帖子、文章、作者等等。由于 GraphQL 的优点，您可以在查询中定义一个结构来只获取您需要的数据。

# 易如🥧项目

**前端** : [检视应用程式](https://github.com/daniel-ordonez/vue-dev-profile)**【CMS】**:[类型。我](https://tipe.io/)**【主机】** : [Github】](https://pages.github.com/)

✋ **等待** 🛑

## 不要公开你的秘密密钥🤫

```
 window.fetch('https://api.tipe.io/graphql', {
  method: 'post',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': YOUR_API_KEY,
    'Tipe-Id': YOUR_ORG_SECRET_KEY
  },
  body: JSON.stringify({ query: query, variables: variables })
})
  .then(function (res) { return res.json() })
  .then(function (result) {
    console.log(result)
  }); 
```

Tipe.io 要求每个请求至少有 2 个密钥

*   🗝️你的 API_KEY
*   🗝️你的 ORG_SECRET_KEY

你永远都不希望这些被看到👀那么，在这种情况下人们会怎么做呢？🤔

## 你需要一个后端

不要担心，我保证它仍然是容易和自由的🤞

[解决方案](https://github.com/daniel-ordonez/tipeio-query-forwarder)是一个 *Express 应用*，它像 Tipe.io 一样监听请求，但只需要查询。将您的密钥安全地存储在服务器中，它使用您的密钥和查询发出 GraphQL 请求，并向您发回响应。

### 往哪里部署？

我只有在微软的 Azure 和谷歌云的 T2 应用引擎上部署 Node 应用的经验。

> 我不喜欢蓝色。

😦当时，App Engine 标准环境还不支持 Nodejs，这意味着它不可能免费运行(现在支持了😲).

所以我调查了来自 zeit.co 的现在的

*现在*让部署 dead 的过程变得简单。
只需安装 de CLI 工具，在你的项目目录下打开终端并运行

```
now 
```

*现在*有一个名为 **OSS** 的免费计划，当然它的选择有限，但不同于说 *heroku* ，你的应用不会去💤过了一段时间。

还有最后一个小细节。OSS 计划公开了你的代码，所以将 T2 密钥写入你的代码会使它们可见👀
不必惊慌，部署时通过 *Now CLI* 将您的密钥设置为环境变量就可以轻松解决

```
now -e MY_API_TOKEN="XXXXX" 
```

## 最后的想法

这是我的第一篇帖子，我敢肯定它很乱，写得也不是很好，但我会感谢你的评论、建议、批评和所有这些好东西，✌️.

告诉我你认为这个主意怎么样，你喜欢吗？是不是太复杂了？
还能更好吗？(当然可以，但是怎么做呢？)

这里是后端的代码

# 鳍
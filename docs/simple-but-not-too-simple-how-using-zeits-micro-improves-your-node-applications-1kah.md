# 简单，但不太简单:如何使用 Zeit 的“微”改进您的节点应用程序

> 原文：<https://dev.to/hugo__df/simple-but-not-too-simple-how-using-zeits-micro-improves-your-node-applications-1kah>

> 离开快速舒适区，扩展您对节点应用程序架构的看法。

**TL；博士**

*   使用函数组合模型来构建 HTTP 服务器是非常棒的
*   功能即服务很棒，但也有一些缺点
*   有一个类似于 FaaS 的简单 API，但是没有编译步骤

*   [作为 JavaScript](#functions-as-a-composition-model-for-javascript) 的组合模型

*   [职能作为服务痛点](#functions-as-a-service-pain-points)

*   [Beyond Express: `micro`作为一个库来编写简单的节点应用程序](#beyond-express-micro-as-a-library-to-write-simple-node-applications)

*   [`micro`如何帮助你的架构](#how-micro-helps-your-architecture)

*   [`micro`文档、生态系统和示例](#the-micro-docs-ecosystem-and-examples)

*   [更多信息](#further-information)

比较`micro`和`express`的例子可以在 github.com/HugoDF/micro-vs-express-example 的[找到。](https://github.com/HugoDF/micro-vs-express-example)

## 作为 JavaScript 的合成模型

以下是我的一些与本文相关的应用程序开发理念:

*   功能太棒了
*   最佳解决方案很简单，但也不太简单
*   JavaScript 最强大的特性是一流的函数

综上所述，在 JavaScript 中，组合函数往往是构建一个解决方案的好方法，这个解决方案推理起来很简单，但却是由标准块构建的。

这就是“功能即服务”(FaaS)，也称为“无服务器”平台对节点开发者有吸引力的原因之一。作为构建 Post Frequenc 的一部分，最初的方法是在 Netlify 的 lambda 平台上编写后端。在原型阶段，它是有效的，有两个事务 lambda 函数:

*   一个是从站点 URL 获取提要 URL
*   一个用于解析来自 URL 的 RSS/Atom 提要。

Netlify Lambdas(实际上最终部署为 AWS lambdas)有一个简单的 API、事件输入、数据输出(使用回调或异步函数)。

我意识到处理`input is a feed URL`和`input is a site URL`二分法的最好方法是同时尝试 fetch，看看什么错误，什么没有。即。我建造了这样的东西:

```
const coerceErrorToObjectProperty = promise =>
  promise.catch(error => Promise.resolve({ error }));

function handler () {
  // deal with input
  const url = "some-url";
  Promise.all([
    coerceErrorToObjectProperty(parseFeed(url)),
    coerceErrorToObjectProperty(getFeedUrl(url))
  ])
    .then(([feedOutput, feedUrlOutput]) => {
      if (!feedOutput.error) {
        console.log('');
        return feedOutput;
      }
      if (!feedUrlOutput.error) {
        console.log('');
        return feedUrlOutput;
      }
      throw new Error(feedOutput.error || feedUrlOutput.error);
    })
    .then(() => {
      // deal with response
    })
    .catch(() => {
      // deal with error
    });
} 
```

## 功能作为服务痛点

此时，我遇到了在 FaaS 上运行的第一个开发人员体验问题:

1.  我的后端现在需要捆绑
2.  调试缩小的代码仍然不太好

这超出了通常用来反对 FaaS 路线的冷启动和超时问题。

随着对 Node 8+的广泛支持，越来越没有理由传输你的后端代码(即使你想使用 es 模块语法，[参见 ES 模块，参见在没有 Babel/Webpack 的 Node 中使用 ES 模块](https://dev.to/hugo__df/use-es-modules-in-node-without-babelwebpack-using-esm-1obk))。

## Beyond Express: `micro`作为一个库编写简单的节点应用程序

> 希望这一部分不会成为太多的“Express vs .`micro`”辩论。
> 
> 我喜欢 Express 的稳定性、成熟性和灵活性，它过去是，现在仍然是 Node.js 服务器生态系统的一匹真正的骏马。
> 
> `micro`更加专注，构建时限制更少(例如，它可以利用节点中的异步/等待支持)

写完 lambdas 之后，我一直在寻找一个后端框架，它有一个像 lambda 一样自包含的 API。那个图书馆是`micro`。这引发了以下思考:

*   我有使用 Express 的经验，但我觉得它对小应用程序来说有太多的摩擦，这是应该的。正如大多数使用过 Express 的人所知道的，你必须为中间件行为安装额外的包，比如主体解析，这是通过`micro`实现的。
*   要在 Express 中发送响应，仍然使用回调语法:`res.json(data)`或`res.send(data)`。
*   基于中间件的扩展系统很酷，但是它并不总是像你希望的那样清晰。请求/响应处理程序有时依赖于由中间件堆栈上的任意中间件设置的属性。

以上几点字面上是`micro`的一些卖点(见[https://github . com/zeit/micro # features):](https://github.com/zeit/micro#features):)

*   **易**:配合`async`和`await` ( [更](https://zeit.co/blog/async-and-await))使用而设计
*   **简单**:面向单一用途模块(功能)
*   **标准**:就 HTTP！
*   **显式**:没有中间件模块声明所有的[依赖](https://github.com/amio/awesome-micro)

与 Express 相比，它还有以下额外的优势(再次来自[https://github . com/zeit/micro # features):](https://github.com/zeit/micro#features):)

*   **快速**:超高性能(甚至 JSON 解析也是可选的)
*   **微**:整个项目大约 260 行代码
*   **敏捷**:超级简单的部署和容器化
*   轻量级:包括所有的依赖项，这个包的重量不到 1 兆字节

在我看来，有以下反对意见:

*   快递就是“够快”
*   Express“足够小”(尽管有时在 Docker 内部的 Nodemon 中运行 Express 会导致 1s 以上的重新加载时间)
*   Express“易于装箱/部署”
*   Express 是“足够轻量级的”(一个 Express 应用+依赖项很少超过几十兆字节，相比之下，Rails 或 Django 应用很容易达到 50-100+兆字节)

它内置了身体解析功能，但没有其他功能。这是件好事，它名副其实。

下面是一些等效的应用程序，它们对发送一个数字的帖子做出响应，并使其增加 1(简单且半无用，但嘿):

*   在一个`express-app.js`文件中表示:

```
const express = require('express');
    const bodyParser = require('body-parser');
    const app = express();
    app.use(bodyParser.json());
    app.post('/', (req, res) => {
      const { value = 0 } = req.body;
      return res.json({
        value: Number(value) + 1
      });
    });
    app.listen(process.env.PORT || 3000, () => {
      console.log('Server listening on PORT', process.env.PORT || 3000);
    }); 
```

*   `micro`在一个`micro-app.js`文件中:

```
const { json, createError } = require('micro');
  module.exports = async (req) => {
    if (req.method !== 'POST') {
      throw createError(404, 'Not Found');
      // to have the same behaviour as the Express app
    }
    const { value = 0 } = await json(req);
    return {
      value: Number(value) + 1
    };
  }; 
```

`package.json`看起来如下:

```
{  "main":  "micro-app.js",  "scripts":  {  "express":  "node express-app.js",  "micro":  "micro"  },  "dependencies":  {  "body-parser":  "^1.18.3",  "express":  "^4.16.4",  "micro":  "^9.3.3"  }  } 
```

你可以在 GitHub 上找到这个工作示例:[github.com/HugoDF/micro-vs-express-example](https://github.com/HugoDF/micro-vs-express-example)。

## `micro`如何帮助你的架构

将我的 lambdas 映射到一个微型应用程序中，我用`[fs-router](https://github.com/jesseditson/fs-router)`设置了两条路线，一条去`input is a site URL OR input is a feed URL`，一条去`input is a feed URL`。

添加`fs-router`时发生的事情很有意思。

在微观上，你必须努力去拥有路线。这项工作类似于 Express 中进行主体解析所需的工作。这说明了这些工具中的每一个认为它们所解决的问题的核心是什么，而不是可以完成但不是核心问题的事情。

的问题是围绕构建简单、明确和大量的(微)服务。这就是路由不在核心的原因:有了微服务，有了正确的接口，一大块服务可能不需要路由。

`express`持相反的观点，它被设计成对 Node 上的任何服务器应用程序都是灵活的。从构建简单的单向应用程序到 REST APIs，再到使用服务器呈现的模板构建功能全面的单部署 web 应用程序。考虑到这一点，它着手解决两个核心问题:路由和标准化插件系统(基于中间件)。

对于这个项目，这种摩擦让我意识到拥有两个具有重复功能的端点是疯狂的:`input is a feed URL`已经被`input is a site URL OR input is a feed URL`覆盖了。所以我删除了第二条路线，去掉了`fs-router`并进行部署👍。

我想说明的另一个 micro 特性是:

```
const applyMiddlewares = handler =>
  health(
    log(
      cors(
        rateLimit(
          handler
        )
      )
    )
);
module.exports = applyMiddlewares((req, res) => 
  'Service with /health, logging, CORS and rate-limiting'
); 
```

“中间件”只是一组功能，它们被组合并应用于一个处理程序(而不是更不透明的`app.use(() => {})`或`app.get(` `'` `/path` `'` `, middleware1, middleware2, handler)`)。处理程序定义什么是想要/需要的增强，而不是期望顶级应用程序提供它们。

## `micro`文档、生态系统和实例

Micro 的文档反映了图书馆的理念:它们干净简单，展示了一些核心用例及动作，以下类似于[https://github.com/zeit/micro#usage:](https://github.com/zeit/micro#usage:)这是 hello world，`app.js`

```
module.exports = () => 'Hello world'; 
```

您可以设置以下`package.json` :

```
{  "main":  "app.js",  "scripts":  {  "start":  "micro"  },  "dependencies":  {  "micro":  "latest"  }  } 
```

然后可以用`npm start`运行。

## 进一步信息

我将写下一些我如何部署我的`micro`服务和 Express 应用程序的细节，关于这个主题已经有一个预告片:[部署选项:数字海洋上的 Netlify+Dokku vs now . sh，GitHub Pages，Heroku 和 AWS](https://dev.to/hugo__df/deployment-options-netlify--dokku-on-digitalocean-vs-nowsh-github-pages-heroku-and-aws-4cab) 。

随时给我发微博 [@hugo__df](https://twitter.com/@hugo__df) 。

[凯利赛克](https://unsplash.com/@kellysikkema?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)
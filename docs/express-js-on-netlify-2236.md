# 网上快递

> 原文：<https://dev.to/neverendingqs/express-js-on-netlify-2236>

Netlify 最初是一项托管静态网站的服务。它不仅从一开始就是一个令人惊叹的服务，管理主机基础设施，让你可以专注于把你的网站放在一起，但他们后来增加了对那些希望超越静态网站的人的支持。AWS(亚马逊网络服务)Lambda 功能，他们最新的[插件](https://www.netlify.com/docs/functions/)，意味着开发者现在可以自由运行任意后端代码来支持他们的网站，而不必离开 Netlify。

但是，如果您来自 Express.js 背景，您可能没有兴趣使用或有时间学习新的技术堆栈。在这篇文章中，我将展示如何在 Lambda 函数上构建 Express.js 应用程序。如果你正在使用另一个 web 框架(例如 Koa.js ),请不要离开！这篇文章的内容也应该适用于任何基于 Node.js 的`http`模块构建的 web 框架。

虽然我在这篇文章中只提到 AWS Lambda 函数，但我真正指的是 [API Gateway API 和 Lambda integration](https://docs.aws.amazon.com/apigateway/latest/developerguide/getting-started-with-lambda-integration.html) ，Netlify functions 已经为我们抽象出来了。

## Lambda 函数快速介绍

想象一个在服务器上运行的 web 应用程序中更新数据库的函数:

```
function updateDatabase(data) {
  ... // update the database
  return newValue;
} 
```

想象它被绑在一条路线上:

```
app.post('/updatestate', (res, req) => {
  const newValue = updateDatabase(res.body);
  req.json(newValue);
}); 
```

您需要多少台服务器来服务于`/updatestate`端点？您知道何时在高负载期间增加服务器，何时缩减规模以节省资金吗？

Lambda 函数背后的思想是，你只需支付你所需要的，而不必担心对应用程序负载变化的响应。如果什么都没发生，你根本不用付钱。如果请求数量激增，AWS 会在后台自动为您进行伸缩，以匹配该负载。你所要做的就是提供你希望 Lambda 在每次请求时执行的代码。在我们的示例中，这相当于`updateDatabase()`，看起来像这样:

```
'use strict';
function updateDatabase(data) {
  ... // update the database
  return newValue;
}

exports.handler = function(event, context, callback) {
  if(event.httpMethod === 'POST' && event.path === '/my/path') {
    const requestBody = JSON.parse(event.body);
    const newValue = updateDatabase(requestBody);
    callback(null, {
      statusCode: 200,
      body: newValue
    });
  } else {
    callback(null, {
      statusCode: 400,
      body: {}
    });
  }
} 
```

Lambda 将为每个请求调用`handler`函数。`event`对象是 Lambda 表示传入请求的方式，`callback(err, data)`函数是告诉 Lambda 发送回什么响应的方式。

## Express.js via 无服务器-http

Express.js 是建立在`http`模块上的网络框架，简化了路线管理，如 Evan Hahn 的[理解 Express.js 中所述。如果您查看该文章中的以下示例，您会注意到在使用`http`时必须管理路由，类似于使用 Lambda 函数时:](https://evanhahn.com/understanding-express/)

```
var http = require("http");

http.createServer(function(req, res) {
  // Homepage
  if (req.url === "/") {
    res.writeHead(200, { "Content-Type": "text/html" });
    res.end("Welcome to the homepage!");
  }

  // About page
  else if (req.url === "/about") {
    res.writeHead(200, { "Content-Type": "text/html" });
    res.end("Welcome to the about page!");
  }

  // 404'd!
  else {
    res.writeHead(404, { "Content-Type": "text/plain" });
    res.end("404 error! File not found.");
  }
}).listen(1337, "localhost"); 
```

回到我们的 Lambda 函数，您会注意到`event`对象和`callback`函数与 Express.js 的`req`和`res`对象非常相似，因为它们有助于获取关于请求的信息并向客户端返回响应。事实上，两者非常相似，有一个库可以在两者之间进行转换:[无服务器-http](https://github.com/dougmoscrop/serverless-http) 。

使用`serverless-http`，您可以这样做:

```
'use strict';
const express = require('express');
const serverless = require('serverless-http');
const app = express();
const bodyParser = require('body-parser');

function updateDatabase(data) {
  ... // update the database
  return newValue;
}

app.use(bodyParser);
app.post('/updatestate', (res, req) => {
  const newValue = updateDatabase(res.body);
  req.json(newValue);
});

module.exports.handler = serverless(app); 
```

关键是最后一点，其中`serverless-http`充当装饰器，将 Lambda 的`event`翻译成 Express.js 可以使用的东西，并将`req`转换成对 Lambda 的`callback(err, data)`的正确调用。

## 好处

在 Lambda 函数上使用`serverless-http`和 Express.js 简化了路由决策代码，这类似于为什么在`http`模块上使用 Express.js。它还允许你使用你最喜欢的 Express.js 模式和中间件来创建你的 Netlify 网站，而不必离开 Netlify。

## 局限性

这种方法有几个限制。

### 资源限制

Netlify 的 Lambda 函数有一个 [128 MB 内存和 10 秒的执行限制](https://www.netlify.com/docs/functions/#custom-deployment-options)。就处理能力(CPU)而言，AWS 没有透露该值，但它与分配给 Lambda 函数的内存量成比例[。所有内存配置中还有 512 MB 的临时磁盘空间。](https://docs.aws.amazon.com/lambda/latest/dg/resource-model.html)

这些限制意味着您将不能有任何大型或长时间运行的后端请求。如果绕过这些限制，Lambda 函数会立即停止执行，并出现一个错误，该错误会转化为一个 500 HTTP 状态代码。你可以在 AWS 的开发者指南上看到完整的限制。

有趣的是， [1024 MB 的 Lambda 函数比 128 MB 的 Lambda 函数更划算，而且运行时间更短](https://medium.com/epsagon/how-to-make-lambda-faster-memory-performance-benchmark-be6ebc41f0fc)。如果你是 Lambda 函数的大量用户，你可能想要联系销售人员[根据他们的文档](https://www.netlify.com/docs/functions/)请求 1024 MB 的函数。

### 无国籍

AWS 不保证 Lambda 函数的任何实例将被多个请求重用，并且可以在任何时候创建新的实例。这意味着您不能期望任何内存中的状态或文件系统内容跨请求保持不变。想了解更多细节，我推荐看一下[开发者指南](https://docs.aws.amazon.com/lambda/latest/dg/running-lambda-code.html)。

### 静态文件

Netlify 要求每个 Lambda 函数的代码都自包含在一个文件中。这意味着您无法从 Express.js 提供任何静态文件，并且任何视图模板都必须嵌入到 JavaScript 文件中。

### 数据库

大多数 web 应用程序的背后是一个数据库。Netlify 还没有提供数据库服务，所以如果您需要一个持久的数据存储，比如用于会话管理，那么现在您必须自带。

请记住，不能保证后续调用会重用 Lambda 函数的实例，所以不要让**而不是**依赖内存缓存(例如，不要使用[内存库](https://github.com/roccomuso/memorystore)来管理你的会话)。

## 结论

在这篇文章中，我展示了如何将 Express.js 与 Lambda 函数结合使用，并概述了这种方法的局限性。如果您有兴趣进一步探索这个主题，我在[neverendingqs/netlify-express](https://github.com/neverendingqs/netlify-express)上有一个非常简单的部署就绪示例，您可以开始使用。

请在下面留言告诉我你的想法！
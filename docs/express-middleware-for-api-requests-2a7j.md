# 用于 API 请求的快速中间件

> 原文：<https://dev.to/whoisryosuke/express-middleware-for-api-requests-2a7j>

最近，我需要创建一个 NextJS 应用程序，它可以进行经过认证的 API 调用，并且不能向客户端透露它的凭证。解决方案很简单，我必须将 Express 集成到应用程序中。但是，如何从 API 获取数据并将其传递给页面呢？

> 这个过程假设您有一个带有 NextJS、Express、同构获取解决方案和 dotenv(用于 env 变量)的环境设置。如果你遵循了 NextJS 的动态路由指南，你就基本上做到了。但是适应其他服务器框架应该很容易。

## 异步或半同步

我尝试首先在一个单独的函数中获取数据，并在页面呈现在 route 中之前调用它:

```
const credentials = {
  method: 'get',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Basic ' + btoa(process.env.API_USER + ":" + process.env.API_VENDOR)
  }

function fetchApi (endpoint) {
  return fetch(process.env.API_URL + endpoint, credentials)
    .then(r => r.json())
}

    server.get('/facilities', (req, res) => {
      const data = fetchApi('/facilities/v1/')
            .then(data => return data)

      return app.render(req, res, '/facilities', { data })
    }) 
```

Enter fullscreen mode Exit fullscreen mode

这导致了页面呈现和加载，以及之后的数据加载。简单的错误，特别是如果你来自一个非异步的背景。

但是如何创建异步快速路由呢？显然非常容易:

```
 server.get('/facilities', async (req, res) => {
      const data = await fetchApi('/facilities/v1/')
            .then(data => return data)

      return app.render(req, res, '/facilities', { data })
    }) 
```

Enter fullscreen mode Exit fullscreen mode

在呈现您的 route - *的函数之前添加一个 async，因为不要强调它，很容易忘记您是在* *函数*内* *工作的。现在，您只需在页面加载之前对您想要获取的承诺点击一个 await。

## 但是我们能让它重复使用吗？

我需要通过许多路线获取数据，对不同的端点发出许多不同的请求。我们不用在每个路由中重复代码来发出 API 请求，而是用一个**中间件**来做这件事，并将数据转储到`res.locals`(在我们的路由中可以访问)中。

```
// Credentials for authenticated fetch calls to API
const credentials = {
  method: 'get',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Basic ' + btoa(process.env.API_USER + ":" + process.env.API_VENDOR)
  }
}

/**
 * Facade for fetch preloaded with authentication credentials
 * to easily use in any other function
 */
async function fetchApi (endpoint) {
  return await fetch(process.env.API_URL + endpoint, credentials)
    .then(r => r.json())
}

/**
 * A function that returns the middleware function
 * We nest the middleware in a function so we can 
 * pass an endpoint, making the middleware more reusable
 */
function getData(endpoint) {
  return (req, res, next) => {

    /**
     * Here we create an async function so
     * we can load the data before the page renders
     */
    const fetchData = async () => {
      await fetchApi(endpoint)
        .then(data => {
          // We place the data in res.locals to access in the route later
          res.locals.data = data
          next()        
        });
    }
    fetchData();
  }
}

app.prepare()
  .then(() => {
    const server = express()

    server.get('/facilities', getData('/facilities/v1/'), (req, res) => {
      return app.render(req, res, '/facilities', { data: res.locals.data })
    })
  }) 
```

Enter fullscreen mode Exit fullscreen mode

代码对此做了一些解释，但是我必须将中间件函数嵌套在另一个函数中，以便传递端点参数。这是 JS 的工作方式，类似于当你在一个数组上使用`.map()`或`.filter()`时，你想传递一个参数但不能。这是由 Express 解释其中间件的方式引起的封装问题，迫使您包装它，React 社区称之为“HOC”，或返回另一个函数的函数(因此您可以将附加的“道具”传递给子函数-或 React 中的组件)。

现在，在任何路线中，我们只需添加中间件`getData(endpoint)`。

> 您也可以在没有异步的中间件**中进行读取，并依靠承诺链中的`next()`函数。它会保持进度直到加载完成，然后提供“下一个”函数(通常是渲染函数)。我只是让一切保持异步，以防我从中间件中重构它。**

### 超级中间件

您可以将这个中间件应用于整个应用程序(而不是单个路由)，并使用`req.params`对象获取动态路由变量(比如一个博客文章 ID，或者在本例中，一个描述端点的字符串)。

```
function getData() {
  const endpoint = req.params.endpoint
  return (req, res, next) => {

    /**
     * Here we create an async function so
     * we can load the data before the page renders
     */
    const fetchData = async () => {
      await fetchApi(endpoint)
        .then(data => {
          // We place the data in res.locals to access in the route later
          res.locals.data = data
          next()        
        });
    }
    fetchData();
  }
}

   // later in the app...
    server.get('/:endpoint', (req, res) => {
      return app.render(req, res, req.params.endpoint, { data: res.locals.data })
    }) 
```

Enter fullscreen mode Exit fullscreen mode

这允许完全动态地连接到您正在使用的任何 API，因此根据要访问的 API 的大小(最好是简单的),您可以使用一个中间件来管理它们。

## 我❤️中间件

中间件让生活变得更加简单，让应用程序代码变得更加精简。如果你能找到一种方法来简化这段代码(不要太深入 ES6 领域)，我鼓励你在评论中发表它！我总是对发现和分享常见问题的最有效解决方案感兴趣。

希望有所帮助！✌️
良

* * *

**参考文献**:

*   [ExpressJS 中间件](https://expressjs.com/en/guide/using-middleware.html)
*   [MDN:异步功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
*   [中间件:node.js 后端应用的核心](https://hackernoon.com/middleware-the-core-of-node-js-apps-ab01fee39200)
*   [异步快递路线](https://medium.com/@Abazhenov/using-async-await-in-express-with-node-8-b8af872c0016)
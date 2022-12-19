# 路由 restify app，懒人之道。

> 原文：<https://dev.to/yakovmeister/routing-restify-app-the-lazy-way-em3>

你好，几个月前我在 restify 和 express 上工作，我发现我的应用程序以这样的方式路由很麻烦，我必须手动订购它们。所以，我开始开发一个小工具，它可以帮我整理路线，管理我所有的中间件。

请看，[重新定义路由器配置](https://github.com/yakovmeister/restify-router-config)。

它让您以更有效的方式处理各种路由任务，例如(嵌套组、中间件管理、基于通配符的路由排序)

下面是一个简单的用法示例:

```
/**
 * Note that the goal of this snippet is to showcase the usage of the 
 * tool, code provided is not from actual project.
 */
const router = require('restify-router-config')
const restify = require('restify')

const server = restify.createServer()

const apiAuth = (req, res, next) => {
  console.log('authed!'); 
  next()
}

const loggingMW = (req, res, next) => {
  console.log(req._timeStart)

  next()
}

const logDone = (req, res, next) => {
  console.log('done!')

  next()
}

router(server, true) ([
  {
    group: 'api/v1',
    middleware: apiAuth,
    routes: [
      {
        match: '/hello',
        method: 'get',
        action: (req, res, next) => res.send('hello')
      },
      {
        group: 'users',
        middleware: [
          ['before', loggingMW],
          ['after', logDone]
        ],
        routes: [
          {
            match: '/:id',
            method: 'get',
            action: (req, res, next) => {
              res.send('hello')

              next()
            }
          },
          {
            match: '/:id/friends',
            method: 'get',
            action: (req, res, next) => {
              res.send('hello')

              next()
            }
          },
          {
            match: '/',
            method: 'get',
            action: (req, res, next) => {
              res.send('hello')

              next()
            }
          }
        ]
      }
    ]
  }
])

server.listen(4000) 
```

Enter fullscreen mode Exit fullscreen mode

如果你觉得这个工具有用或者没用，请告诉我，我真的觉得它很方便，但是你们觉得呢？顺便说一下，上面显示的用法是使用 restify，这个工具也与 express 兼容，但有些用法可能会有所不同。
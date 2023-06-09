# 构建一个 HTTP 服务器

> 原文：<https://dev.to/flaviocopes/build-an-http-server-23jo>

这里是我们在 [Node.js 简介](https://flaviocopes.com/nodejs/)
中用作 Node Hello World 应用的 HTTP web 服务器

```
const http = require('http')

const port = 3000

const server = http.createServer((req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'text/plain')
  res.end('Hello World\n')
})

server.listen(port, () => {
  console.log(`Server running at http://${hostname}:${port}/`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

下面简单分析一下。我们包括了 [`http`模块](https://nodejs.org/api/http.html)。

我们使用该模块创建一个 HTTP 服务器。

服务器被设置为监听指定的端口`3000`。当服务器准备好了，就调用`listen`回调函数。

我们传递的回调函数是在收到每个请求时执行的函数。每当接收到一个新的请求，就调用 [`request`事件](https://nodejs.org/api/http.html#http_event_request)，提供两个对象:一个请求(一个 [`http.IncomingMessage`](https://nodejs.org/api/http.html#http_class_http_incomingmessage) 对象)和一个响应(一个 [`http.ServerResponse`](https://nodejs.org/api/http.html#http_class_http_serverresponse) 对象)。

`request`提供请求详情。通过它我们可以访问请求头和请求数据。

`response`用于填充我们将要返回给客户端的数据。

在这种情况下用

```
res.statusCode = 200 
```

Enter fullscreen mode Exit fullscreen mode

我们将 statusCode 属性设置为 200，表示响应成功。

我们还设置了内容类型头:

```
res.setHeader('Content-Type', 'text/plain') 
```

Enter fullscreen mode Exit fullscreen mode

我们结束关闭响应，将内容作为参数添加到`end()` :

```
res.end('Hello World\n') 
```

Enter fullscreen mode Exit fullscreen mode
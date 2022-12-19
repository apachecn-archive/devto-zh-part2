# 使用 PM2 时正常关闭节点 JS HTTP 服务器(4/4)

> 原文：<https://dev.to/acanimal/graceful-shutdown-nodejs-http-server-when-using-pm2-44-10o0>

所以你创建了一个 NodeJS 服务器，它接收大量的请求，你真的很高兴，但是，就像每一个软件一样，你发现了一个错误或者给它添加了一个新特性。很明显，您需要关闭 NodeJS 进程并重新启动，以便新代码生效。问题是:**您如何以一种优雅的方式来继续为传入的请求提供服务？**

* * *

关于本系列的更多信息:

1.  [了解 NodeJS 集群模块](https://www.acuriousanimal.com/2017/08/12/understanding-the-nodejs-cluster-module)
2.  [通过 HTTP 服务器使用集群模块](https://www.acuriousanimal.com/2017/08/18/using-cluster-module-with-http-servers)
3.  [使用 PM2 管理 NodeJS 集群](https://www.acuriousanimal.com/2017/08/20/using-pm2-to-manage-cluster)
4.  **使用 PM2 时正常关闭 NodeJS HTTP 服务器**

## 启动 HTTP 服务器

在了解必须如何关闭 HTTP 服务器之前，让我们看看通常如何创建一个服务器。接下来的代码展示了一个非常基本的代码，它带有一个 ExpressJS 服务，当访问`/hello`路径时将返回`Hello World !!!`。您也可以传递一个路径参数，例如带有名称的`/hello/John`，这样它会返回`Hello John !!!`。

```
const express = require('express')

const expressApp = express()

// Responds with Hello World or optionally the name you pass as path param
expressApp.get('/hello/:name?', function (req, res) {
  const name = req.params.name

  if (name) {
    return res.send(`Hello ${name}!!!`)
  }

  return res.send('Hello World !!!')
})

// Start server
expressApp.listen(3000, function () {
  console.log('App listening on port 3000!')
}) 
```

Enter fullscreen mode Exit fullscreen mode

`app.listen()`函数的作用是使用核心`http`模块启动一个新的 HTTP 服务器，并返回一个对 HTTP 服务器对象的引用。具体来说，`listen()`的源代码如下:

```
app.listen = function listen() {
  var server = http.createServer(this);
  return server.listen.apply(server, arguments);
}; 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:创建 express 服务器的另一种方法是将我们的`expressApp`引用直接传递给`http. createServer()`，类似于:`const server = http.createServer(app).listen(3000)`。

## 如何正确关闭 HTTP 服务器？

关闭 HTTP 服务器的正确方法是调用`server.close()`函数，这将阻止服务器接受新的连接，同时保持现有的连接，直到响应它们。

下一段代码展示了一个新的`/close`端点，一旦被调用，它将停止 HTTP 服务器并退出应用程序(停止 nodejs 进程):

```
app.get('/close', (req, res) => {
  console.log('Closing the server...')

  server.close(() => {
    console.log('--> Server call callback run !!')

    process.exit()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

很明显，通过端点关闭服务器不是正确的方法。

## 带和不带 PM2 的正常关机/重启

正常关机的目标是关闭服务器的传入连接，而不终止当前正在处理的连接。

当使用像 PM2 这样的进程管理器时，我们管理一个进程集群，每个进程充当一个 HTTP 服务器。PM2 实现优雅重启的方式是:

*   向每个工作进程发送一个`SIGNINT`信号，
*   工人负责捕捉信号，清理或释放任何使用的资源，并完成其过程，
*   最后，PM2 经理催生了一个新的进程

因为这是通过我们的集群进程顺序完成的，所以客户一定不会受到重启的影响，因为总会有一些进程在工作并处理请求。

当我们部署新代码并希望重启服务器时，这是非常有用的，这样新的更改就会生效，而不会有传入请求的风险。我们可以在我们的应用程序中输入下一个代码:

```
// Graceful shutdown
process.on('SIGINT', () => {
  const cleanUp = () => {
    // Clean up other resources like DB connections
  }

  console.log('Closing server...')

  server.close(() => {
    console.log('Server closed !!! ')

    cleanUp()
    process.exit()
  })

  // Force close server after 5secs
  setTimeout((e) => {
    console.log('Forcing server close !!!', e)

    cleanUp()
    process.exit(1)
  }, 5000)
}) 
```

Enter fullscreen mode Exit fullscreen mode

当`SINGINT`发出 catch 信号时，我们调用`server.close()`来避免接受更多的请求，一旦它被关闭，我们就清理应用程序使用的任何资源，比如关闭数据库连接、关闭打开的文件等，调用`cleanUp()`函数，最后，我们用`process.exit()`退出进程。此外，如果由于某种原因我们的代码花了太多时间来关闭服务器，我们会强制它在一个`setTimeout()`中运行一个非常相似的代码。

## 结论

在创建 HTTP 服务器时，无论是服务页面的 web 服务器还是 API，我们都需要考虑到这样一个事实，即它会随着新功能和错误修复而及时更新，因此我们需要考虑如何将对客户的影响降至最低。

以集群模式运行 nodejs 进程是提高我们的应用程序性能的一种常见方式，我们需要考虑如何优雅地关闭所有这些进程，以便不影响传入的请求。

当使用 HTTP 服务器时，用`process.exit()`终止一个节点进程是不够的，因为它会突然终止所有的通信，我们需要首先停止接受新的连接，释放应用程序使用的所有资源，最后停止进程。
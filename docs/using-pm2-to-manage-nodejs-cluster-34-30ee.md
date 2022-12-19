# 使用 PM2 管理 NodeJS 集群(3/4)

> 原文：<https://dev.to/acanimal/using-pm2-to-manage-nodejs-cluster-34-30ee>

集群模块允许我们创建工作进程来提高 NodeJS 应用程序的性能。这在 web 应用程序中尤其重要，在 web 应用程序中，主进程接收所有请求，并在工作进程之间进行负载平衡。

但是，所有这些功能都伴随着成本，管理与进程管理相关的所有复杂性的应用程序必须承担这些成本:如果工作进程意外存在会发生什么，如何优雅地退出工作进程，如果需要重新启动所有工作进程会发生什么，等等。

在本帖中，我们将展示 [PM2](http://pm2.keymetrics.io) 工具。尽管它是一个通用的流程管理器，但这意味着它可以管理任何类型的流程，如 python、ruby，...不仅仅是 NodeJS 进程，该工具是专门设计来管理想要使用集群模块的 NodeJS 应用程序的。

* * *

关于本系列的更多信息:

1.  [了解 NodeJS 集群模块](https://www.acuriousanimal.com/2017/08/12/understanding-the-nodejs-cluster-module)
2.  [通过 HTTP 服务器使用集群模块](https://www.acuriousanimal.com/2017/08/18/using-cluster-module-with-http-servers)
3.  **使用 PM2 管理 NodeJS 集群**
4.  [使用 PM2 时正常关闭 NodeJS HTTP 服务器](https://www.acuriousanimal.com/2017/08/27/graceful-shutdown-node-processes)

## 介绍 PM2

如前所述，PM2 是一个通用的进程管理器，也就是说，一个程序控制其他进程的执行(就像一个 python 程序检查你是否有新邮件),并做如下事情:检查你的进程正在运行，如果由于某种原因它意外退出，重新执行你的进程，记录它的输出，等等。

对我们来说，最重要的事情是 PM2 简化了 NodeJS 应用程序的执行，使其作为一个集群运行。是的，你编写你的应用程序而不用担心集群模块，是 PM2 创建给定数量的工作进程来运行你的应用程序。

### 集群模块的硬部件

让我们看一个例子，在这个例子中，我们使用集群模块创建了一个非常基本的 HTTP 服务器。主进程将产生与 CPU 一样多的工作进程，并且将注意是否有任何工作进程存在来产生新的工作进程。

```
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
  masterProcess();
} else {
  childProcess();  
}

function masterProcess() {
  console.log(`Master ${process.pid} is running`);

  for (let i = 0; i < numCPUs; i++) {
    console.log(`Forking process number ${i}...`);

    cluster.fork();
  }

  cluster.on('exit', (worker, code, signal) => {
    console.log(`Worker ${worker.process.pid} died`);
    console.log(`Forking a new process...`);

    cluster.fork();
  });
}

function childProcess() {
  console.log(`Worker ${process.pid} started...`);

  http.createServer((req, res) => {
    res.writeHead(200);
    res.end('Hello World');

    process.exit(1);
  }).listen(3000);
} 
```

Enter fullscreen mode Exit fullscreen mode

worker 进程是一个非常简单的 HTTP 服务器，它监听端口 3000，并被编程为返回一个`Hello World`并退出(模拟失败)。

如果我们用`$ node app.js`运行程序，输出将显示如下:

```
$ node app.js

Master 2398 is running
Forking process number 0...
Forking process number 1...
Worker 2399 started...
Worker 2400 started... 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在 URL `http://localhost:3000`进入浏览器，我们将得到一个`Hello World`，并且在控制台中看到类似于
的内容

```
Worker 2400 died
Forking a new process...
Worker 2401 started... 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在让我们来看看 PM2 是如何简化我们的应用程序的。

### PM2 道

继续之前，您需要在系统上安装 PM2。通常情况下，它是作为带有`$ npm install pm2 -g`或`$ yarn global add pm2`的全局模块安装的。

当使用 PM2 时，我们可以忘记与主进程相关的代码部分，这将是 PM2 的责任，所以我们非常基本的 HTTP 服务器可以重写为:

```
const http = require('http');

console.log(`Worker ${process.pid} started...`);

http.createServer((req, res) => {
  res.writeHead(200);
  res.end('Hello World');

  process.exit(1);
}).listen(3000); 
```

Enter fullscreen mode Exit fullscreen mode

现在用`$ pm2 start app.js -i 3`运行 PM2，您将会看到类似如下的输出:

> 请注意选项`-i`,它用于指示要创建的实例数量。这个想法是，这个数字应该和你的 CPU 核心数相同。如果你不知道他们，你可以设置`-i 0`离开 PM2 自动检测它。

```
$ pm2 start app.js -i 3

[PM2] Starting /Users/blablabla/some-project/app.js in cluster_mode (3 instances)
[PM2] Done.

| Name      | mode    | status | ↺ | cpu | memory    |
| ----------|---------|--------|---|-----|-----------|
| app       | cluster | online | 0 | 23% | 27.1 MB   |
| app       | cluster | online | 0 | 26% | 27.3 MB   |
| app       | cluster | online | 0 | 14% | 25.1 MB   | 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到运行`$ pm2 log`的应用程序日志。现在，当访问`http://localhost:3000` URL 时，我们将看到类似于
的日志

```
PM2        | App name:app id:0 disconnected
PM2        | App [app] with id [0] and pid [1299], exited with code [1] via signal [SIGINT]
PM2        | Starting execution sequence in -cluster mode- for app name:app id:0
PM2        | App name:app id:0 online
0|app      | Worker 1489 started... 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到 PM2 进程如何检测到我们的一个工人已经退出，并自动启动一个新的实例。

## 结论

尽管 NodeJS 集群模块是提高性能的强大机制，但它是以管理应用程序可能发现的所有情况所需的复杂性为代价的:如果存在工作人员会发生什么，我们如何在不停机的情况下重新加载应用程序集群，等等。

PM2 是一个专门为 NodeJS 集群设计的进程管理器。除了提供查看日志输出、监控等工具之外，它允许集群应用程序、重启或重新加载，而不需要复杂的代码。

## 参考文献

[PM2 使 Node.js 集群变得简单](https://keymetrics.io/2015/03/26/pm2-clustering-made-easy/)
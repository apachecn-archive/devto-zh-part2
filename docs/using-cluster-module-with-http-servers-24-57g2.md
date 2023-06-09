# 将集群模块用于 HTTP 服务器(2/4)

> 原文：<https://dev.to/acanimal/using-cluster-module-with-http-servers-24-57g2>

[集群](https://nodejs.org/api/cluster.html)模块允许我们在多核 CPU 系统中提高应用程序的性能。无论是在 API 还是基于 ExpressJS 的 web 服务器上工作，这一点都非常重要，我们希望充分利用运行 NodeJS 应用程序的每台机器上的所有 CPU。

集群模块允许我们在一组工作进程中对传入的请求进行负载平衡，因此提高了应用程序的吞吐量。

在上一篇文章[了解 NodeJS 集群模块](https://www.acuriousanimal.com/2017/08/12/understanding-the-nodejs-cluster-module)中，我介绍了集群模块，并展示了它的一些基本用法，以创建工作进程并与主进程进行通信。在这篇文章中，我们将看到如何在创建 HTTP 服务器时使用集群模块，包括使用普通的 [HTTP](https://nodejs.org/api/http.html) 模块和 ExpressJS。

* * *

关于本系列的更多信息:

1.  [了解 NodeJS 集群模块](https://www.acuriousanimal.com/2017/08/12/understanding-the-nodejs-cluster-module)
2.  **通过 HTTP 服务器使用集群模块**
3.  [使用 PM2 管理 NodeJS 集群](https://www.acuriousanimal.com/2017/08/20/using-pm2-to-manage-cluster)
4.  [使用 PM2 时正常关闭 NodeJS HTTP 服务器](https://www.acuriousanimal.com/2017/08/27/graceful-shutdown-node-processes)

## 通过 HTTP 服务器使用集群模块

让我们来看看如何创建一个利用集群模块的真正基本的 HTTP 服务器。

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
}

function childProcess() {
  console.log(`Worker ${process.pid} started...`);

  http.createServer((req, res) => {
    res.writeHead(200);
    res.end('Hello World');
  }).listen(3000);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将代码分为两部分，一部分对应于主进程，另一部分用于初始化工作进程。这样，`masterProcess`函数为每个 CPU 代码派生一个工作进程。另一方面，`childProcess`只是创建一个 HTTP 服务器，监听端口 3000，并返回一个带有 200 状态码的漂亮的`Hello World`文本字符串。

如果运行代码，输出必须类似于:

```
$ node app.js

Master 1859 is running
Forking process number 0...
Forking process number 1...
Forking process number 2...
Forking process number 3...
Worker 1860 started...
Worker 1862 started...
Worker 1863 started...
Worker 1861 started... 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我们的初始进程(主进程)是在每个 CPU 上生成一个新的工作进程，它运行一个处理请求的 HTTP 服务器。如您所见，这可以大大提高服务器性能，因为一个进程处理一百万个请求与四个进程处理一百万个请求是不一样的。

## 集群模块如何与网络连接工作？

前面的例子很简单，但是隐藏了一些微妙的东西，一些神奇的节点简化了我们作为开发者的生活。

在任何操作系统中，一个进程都可以使用一个端口与其他系统通信，这意味着给定的端口只能由该进程使用。因此，问题是，**分叉的工作进程如何使用同一个端口？**

简单来说，答案是主进程监听给定的端口，并在所有子进程/工作进程之间对请求进行负载平衡。来自官方文件:

> 工作进程是使用 child_process.fork()方法生成的，因此它们可以通过 IPC 与父进程通信，并来回传递服务器句柄。
> 
> 群集模块支持两种分配传入连接的方法。
> 
> *   第一种(也是除 Windows 之外的所有平台上的默认方法)是循环法，其中主进程侦听一个端口，接受新的连接，并以循环的方式将它们分配给工作进程，使用一些内置的智能来避免工作进程过载。
>     
>     
> *   第二种方法是主进程创建监听套接字，并将其发送给感兴趣的工人。然后，工人直接接受传入的连接。
>     
>     
> 
> 只要还有一些工作人员活着，服务器就会继续接受连接。如果没有工作线程处于活动状态，现有的连接将被断开，新的连接将被拒绝。

## 集群模块负载均衡的其他替代方案

集群模块允许主进程接收请求，并在所有工作进程之间进行负载平衡。这是一种提高性能的方法，但不是唯一的方法。

在文章 [Node.js 进程负载均衡性能:比较集群模块、iptables 和 Nginx](https://medium.com/@fermads/node-js-process-load-balancing-comparing-cluster-iptables-and-nginx-6746aaf38272) 中你可以找到:节点集群模块、iptables 和 Nginx 反向代理之间的性能比较。

## 结论

如今，性能对任何 web 应用程序都是强制性的，我们需要支持高吞吐量和快速提供数据。

集群模块是一种可能的解决方案，它允许我们拥有一个主进程，并为每个内核创建一个工作进程，以便它们运行一个 HTTP 服务器。集群模块提供了两个强大的功能:

*   通过创建一个 IPC 通道并允许使用`process.send()`发送消息，简化了主设备和工作设备之间的通信。
*   允许工作进程共享同一端口。这样做的目的是使主进程接收请求并在工作进程间多路复用它们。
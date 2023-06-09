# 了解 NodeJS 集群模块(1/4)

> 原文：<https://dev.to/acanimal/understanding-the-nodejs-cluster-module-14-2bi>

> 这篇文章最初写于[一个好奇的动物](https://www.acuriousanimal.com/)博客。

NodeJS processes 在单个进程上运行，这意味着默认情况下它没有利用多核系统的优势。如果你有一个 8 核 CPU，并通过`$ node app.js`运行一个 NodeJS 程序，它将在一个进程中运行，浪费其余的 CPU。

希望 NodeJS 为我们提供了包含一组函数和属性的[集群](https://nodejs.org/api/cluster.html)模块，帮助我们创建使用所有 CPU 的程序。毫不奇怪，集群模块用来最大化 CPU 使用率的机制是通过分叉进程，类似于旧的 [fork()](http://www.includehelp.com/c-programs/c-fork-function-linux-example.aspx) 系统调用 Unix 系统。

* * *

关于本系列的更多信息:

1.  **了解 NodeJS 集群模块**
2.  [通过 HTTP 服务器使用集群模块](https://www.acuriousanimal.com/2017/08/18/using-cluster-module-with-http-servers)
3.  [使用 PM2 管理 NodeJS 集群](https://www.acuriousanimal.com/2017/08/20/using-pm2-to-manage-cluster)
4.  [使用 PM2 时正常关闭 NodeJS HTTP 服务器](https://www.acuriousanimal.com/2017/08/27/graceful-shutdown-node-processes)

## 介绍集群模块

集群模块是一个 NodeJS 模块，它包含一组功能和属性，可以帮助我们派生进程来利用多核系统。这可能是您在节点应用程序中必须注意的第一级可伸缩性，特别是如果您在 HTTP 服务器应用程序中工作，那么在进入更高级别的可伸缩性之前(我的意思是在不同的机器中进行垂直和水平伸缩)。

通过集群模块，一个*父/主*进程可以被分支到任意数量的*子/工人*进程中，并通过 [IPC](https://en.wikipedia.org/wiki/Inter-process_communication) 通信发送消息与它们通信。请记住，进程之间没有共享内存。

接下来的几行是我冒昧复制的 NodeJS 文档中的句子的汇编&我想用几行就能帮助你理解整件事。

> Node.js 的单个实例在单个线程中运行。为了利用多核系统，用户有时会希望启动 Node.js 进程集群来处理负载。
> 
> 集群模块允许轻松创建共享服务器端口的子进程。
> 
> 工作进程(子进程)是使用`child_proces.fork()`方法生成的，因此它们可以通过 IPC 与父进程通信，并来回传递服务器句柄。`child_process.fork()`方法是`child_process.spawn()`的特例，专门用于产生新的 Node.js 进程。像`child_process.spawn()`一样，返回一个`ChildProcess`对象。返回的`ChildProcess`将有一个额外的内置通信通道，允许通过`send()`方法在父节点和子节点之间来回传递消息。详见`subprocess.sen()`。
> 
> 重要的是要记住，除了在两者之间建立的 IPC 通信通道之外，衍生的 Node.js 子进程独立于父进程。每个进程都有自己的内存，有自己的 V8 实例。因为需要额外的资源分配，所以不建议生成大量的 Node.js 子进程。

因此，大部分神奇的事情都是由 [child_process](https://nodejs.org/api/child_process.html) 模块完成的，它负责产生新的进程，并帮助它们之间进行通信，例如，创建管道。你可以在 [Node.js 子进程找到一篇很棒的文章:你需要知道的一切](https://medium.freecodecamp.org/node-js-child-processes-everything-you-need-to-know-e69498fe970a)。

## 一个基本例子

别说了，让我们看看真实的例子。接下来，我们展示一个非常基本的代码:

*   创建一个检索 CPU 数量的主进程，并为每个 CPU 派生一个工作进程
*   每个子进程在控制台打印一条消息并退出。

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

  process.exit();
}

function childProcess() {
  console.log(`Worker ${process.pid} started and finished`);

  process.exit();
} 
```

Enter fullscreen mode Exit fullscreen mode

将代码保存在`app.js`文件中，运行执行:`$ node app.js`。输出应该类似于:

```
$ node app.js

Master 8463 is running
Forking process number 0...
Forking process number 1...
Forking process number 2...
Forking process number 3...
Worker 8464 started and finished
Worker 8465 started and finished
Worker 8467 started and finished
Worker 8466 started and finished 
```

Enter fullscreen mode Exit fullscreen mode

### 代码解释

当我们运行`app.js`程序时，一个操作系统进程被创建，开始运行我们的代码。开始时，集群模式被导入`const cluster = require('cluster')`，在`if`语句中，我们检查`isMaster`属性。

因为进程是*第一个*进程`isMaster`属性是`true`然后我们运行`masterProcess`函数的代码。这个函数没有太多秘密，它根据你机器的 CPU 数量循环，并使用`cluster.fork()`方法分叉当前进程。

`fork()`真正做的是创建一个新的节点进程，就像你通过命令行用`$node app.js`运行它一样，也就是说你有很多进程在运行你的`app.js`程序。

当一个子进程被创建和执行时，它做的和主进程一样，即导入集群模块并执行`if`语句。其中一个差异是对于子进程来说，`cluster.isMaster`的值是`false`，所以它们结束运行`childProcess`函数。

注意，我们用`process.exit()`显式终止主进程和工作进程，默认情况下返回值为零。

> 注意:NodeJS 还提供了[子流程](https://nodejs.org/api/child_process.html)模块，简化了创建和与其他流程的通信。例如，我们可以生成`ls -l`终端命令，并用另一个进程来处理结果。

## 通信主进程和工作进程

当创建一个 worker 进程时，在 worker 和 master 之间会创建一个 IPC 通道，允许我们使用`send()`方法在它们之间进行通信，该方法接受一个 JavaScript 对象作为参数。记住它们是不同的进程(不是线程),所以我们不能使用共享内存作为通信方式。

从主进程中，我们可以使用进程引用(即`someChild.send({ ... })`)向工作进程发送消息，在工作进程中，我们可以简单地使用当前的`process`引用(即`process.send()`)向主进程发送消息。

我们略微更新了之前的代码，允许主进程向工作进程发送和接收消息，工作进程也可以从主进程接收和发送消息:

```
function childProcess() {
  console.log(`Worker ${process.pid} started`);

  process.on('message', function(message) {
    console.log(`Worker ${process.pid} recevies message '${JSON.stringify(message)}'`);
  });

  console.log(`Worker ${process.pid} sends message to master...`);
  process.send({ msg: `Message from worker ${process.pid}` });

  console.log(`Worker ${process.pid} finished`);
} 
```

Enter fullscreen mode Exit fullscreen mode

工人流程简单来说就是理解。首先，我们监听用`process.on('message', handler)`方法注册监听器的`message`事件。后来我们用`process.send({...})`发了一条信息。注意，该消息是一个普通的 JavaScript 对象。

```
let workers = [];

function masterProcess() {
  console.log(`Master ${process.pid} is running`);

  // Fork workers
  for (let i = 0; i < numCPUs; i++) {
    console.log(`Forking process number ${i}...`);

    const worker = cluster.fork();
    workers.push(worker);

    // Listen for messages from worker
    worker.on('message', function(message) {
      console.log(`Master ${process.pid} recevies message '${JSON.stringify(message)}' from worker ${worker.process.pid}`);
    });
  }

  // Send message to the workers
  workers.forEach(function(worker) {
    console.log(`Master ${process.pid} sends message to worker ${worker.process.pid}...`);
    worker.send({ msg: `Message from master ${process.pid}` });    
  }, this);
} 
```

Enter fullscreen mode Exit fullscreen mode

`masterProcess`功能分为两部分。在第一个循环中，我们分叉了与 CPU 数量一样多的工作线程。`cluster.fork()`返回一个代表工人进程的`worker`对象，我们将引用存储在一个数组中，并注册一个监听器来接收来自工人实例的消息。

稍后，我们循环遍历 worker 数组，并从主流程向具体的 worker 发送一条消息。

如果运行代码，输出将类似于:

```
$ node app.js

Master 4045 is running
Forking process number 0...
Forking process number 1...
Master 4045 sends message to worker 4046...
Master 4045 sends message to worker 4047...
Worker 4047 started
Worker 4047 sends message to master...
Worker 4047 finished
Master 4045 recevies message '{"msg":"Message from worker 4047"}' from worker 4047
Worker 4047 recevies message '{"msg":"Message from master 4045"}'
Worker 4046 started
Worker 4046 sends message to master...
Worker 4046 finished
Master 4045 recevies message '{"msg":"Message from worker 4046"}' from worker 4046
Worker 4046 recevies message '{"msg":"Message from master 4045"}' 
```

Enter fullscreen mode Exit fullscreen mode

这里我们没有用`process.exit()`终止进程，所以要关闭应用程序，你需要使用`ctrl+c`。

## 结论

[集群模块](https://nodejs.org/api/cluster.html)为 NodeJS 提供使用 CPU 全部能力所需的能力。尽管在本文中看不到，集群模块是由[子进程](https://nodejs.org/api/child_process.html)模块补充的，它提供了大量工具来处理进程:启动、停止和管道输入/输出等。

集群模块允许我们轻松地创建工作进程。此外，它**神奇地**创建了一个 IPC 通道，用于传递 JavaScript 对象的主进程和工作进程之间的通信。

在我的下一篇文章中，我将展示集群模块在 HTTP 服务器中的重要性，无论是 API 还是 web 服务器使用 ExpressJS。集群模块可以提高我们的应用程序的性能，该应用程序具有与 CPU 核心一样多的工作进程。
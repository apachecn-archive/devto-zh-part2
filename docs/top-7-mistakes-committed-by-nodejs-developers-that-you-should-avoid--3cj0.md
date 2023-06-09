# 节点犯下的 7 大错误。您应该避免的 JS 开发人员

> 原文：<https://dev.to/vaibhavshah/top-7-mistakes-committed-by-nodejs-developers-that-you-should-avoid--3cj0>

[![mistakes that node.js developers should avoid](img/8b45f37706051c364bfb554c344e82f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vf3aRDIi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k4w68germo8aneywa301.jpg)

谈到高性能 web 服务器，三个最重要的特征是——可伸缩性、延迟和吞吐量。在纵向扩展或横向扩展的同时保持低延迟和高吞吐量绝对不是一件容易的事情。但是，对于 Node.js 来说，这是一个关键优势——低延迟和高吞吐量。

## 但是，Node.js 是什么？

节点。Js 是一个开源的 JavaScript 运行时环境，它通过“非阻塞”I/O API 提供来自浏览器的持久连接，可以轻松优化任何应用程序的可伸缩性和吞吐量。这意味着它不会浪费时间等待任何 I/O 请求返回。

传统上，web 服务器用于为每个传入的请求创建新的连接线程，这在某种程度上是可行的，但是消耗了大量的资源和时间。这导致了高延迟和低性能的 web 应用程序。

Node.js 采用完全不同的方法在客户机和 web 服务器之间建立双向通信。它执行单线程事件循环，该循环在可以处理所有连接的系统中注册。每个新连接运行一个 JavaScript 回调函数。这个回调函数可以简单地通过非阻塞 I/O 调用来处理请求。

因此，Node.js 通过消除等待并继续下一个请求来提高 web 服务器的性能。

下图显示了与其他 web 服务器相比，Node.js 在流量和受欢迎程度方面的市场地位。

[![node.js market stats](img/9ea151fd70cb281ff6d43292fec66f9c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KmVPD3Kp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b3qr2n3e3ouxfjtbfpfz.jpg)

尽管 Node.js 有各种各样的优点，但是需要正确地实现它以获得最大的好处。开发人员需要知道如何充分利用他们的 Node.js 项目。为此，他们需要首先理解 Node.js 的主要缺陷。

## 避免 7 大 Node.js 错误

### 1。发起多次回调

有时，开发人员会保存一个文件，然后重新加载 Node.js web 应用程序,看看它是否会很快崩溃。这会导致多次回调执行，因为它们在第一次执行后会忘记返回。JavaScript 依赖回调来帮助它连接各种异步元素，使它们正常工作。

### 2。测试不正确

默认情况下，测试是应用程序开发过程中最重要但却被忽视的部分。一旦一个应用程序准备好了，你不应该忘记执行代码的重复测试和检查。作为 Node.js 开发人员，您应该记住这一重要步骤。你可以借助多种工具来进行成功的测试，比如 Mocha、Jasmine 等。

### 3。缺乏剖析和零监控

经常混淆测试，剖析是一个完全不同的过程。通过研究与程序相关的各个方面，如空间或函数返回时间，分析信息有助于程序优化。

在 Node.js 应用程序中，概要分析将帮助您了解事件循环中的延迟、系统负载、CPU 负载或内存使用等情况。

### 4。没有使用正确的开发工具

静态分析工具仅在开发阶段帮助您识别应用程序错误和问题。还有很多 supervisor 程序以及 pm2，forever，Supervisor 和 nodemon。这些工具帮助您在同一台机器上处理多个应用程序，甚至管理日志。它们甚至允许你在崩溃时重启应用程序。

### 5。阻塞事件循环

众所周知，Node.js 是单线程环境，因此没有两个应用程序部分彼此并行运行。同样，任何阻塞事件循环的东西都会阻塞整个循环。当受 CPU 限制的请求连接到各种客户端，最终阻塞事件循环，使每个客户端等待时，可能会发生这种情况。

在这里，像 StrongOps 和开源模块这样的工具可以帮助您轻松地检测事件循环中的延迟。

### 6。创建大型应用程序

创建 Node.js 应用程序时，尽量保持简单。

这可以通过将事物分解成微小的模块并粘贴到 NPM 来实现。这将简化代码的测试和阅读。Node 支持构建微型模块，帮助您在节点应用程序中实现模块化。

### 7。将数字视为整数数据

开发人员有时会认为将数字视为整数数据是一个严重的错误。在 JavaScript 中，这些被称为浮点数据。对浮动的任何错误估计都会使整个系统出错。

## 结论

Node.js 的创建者 Ryan Dahl 认为 I/O 无阻塞是该平台的主要优势。虽然一些 Node.js 开发人员可能担心在大量 CPU 周期的情况下单个事件循环会被阻塞，但将进程划分为微小模块的能力使 Node.js 成为构建 web 应用程序的完美平台。这可能是网飞、沃尔玛、LinkedIn、PayPal 和 NASA 等大公司使用 Node.js 构建主要应用程序的原因。
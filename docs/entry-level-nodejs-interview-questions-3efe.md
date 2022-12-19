# 入门级节点。JS 面试问题

> 原文：<https://dev.to/sharadtricks/entry-level-nodejs-interview-questions-3efe>

[![NodeJs Questions](../Images/13fee2bd16e0b198dd9120640a5f0221.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--az76qB3G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4xfolf6n29uff5v9zq3k.png) 
你是 Node Js 开发者吗？寻找一些基本的入门级 NodeJs 面试问题。在这篇文章中，我将与拥有 Node.JS 经验的专家成员分享 25 个 [NodeJS 基本面试问题](https://www.onlineinterviewquestions.com/node-js-interview-questions/)。这些面试问题是专门为你在 Node.JS 主题的面试中可能遇到的问题的性质而设计的

## 1.node.js 是什么？

Node.js 是一个服务器端脚本，用于构建可伸缩的程序。它比其他服务器端语言有多种优势，最突出的是非阻塞 I/O。

## 2.哪里可以用 node.js？

Node.js 可用于以下目的
a) Web 应用(尤其是实时 Web 应用)
b)网络应用
c)分布式系统
d)通用应用

## 3.事件驱动编程是什么意思？

在计算机编程中，事件驱动编程是一种编程范式，其中程序的流程由事件决定，如来自其他程序或线程的消息。它是一种应用程序架构技术，分为两个部分 1)事件选择 2)事件处理

## 4.你所说的输入输出是什么意思？

I/O 是输入和输出的简写，它将访问应用程序之外的任何东西。一旦应用程序启动，它将被加载到机器内存中以运行程序。

## 5.node.js 如何工作？

Node.js 工作在 v8 环境下，它是一个虚拟机，利用 JavaScript 作为其脚本语言，并通过非阻塞 I/O 和单线程事件循环实现高输出。

## 6.为什么 Node.js 是单线程的？

对于异步处理，Node.js 是作为一个实验明确创建的。据信，与典型的基于线程的实现相比，在典型的 web 负载下，通过在单个线程上进行异步处理，可以实现更高的性能和可伸缩性。

## 7.解释“控制流”如何控制函数调用的步骤？

a)控制执行顺序
b)收集数据
c)限制并发
d)调用程序中的下一步

## 8.什么是控制流函数？

在几个异步函数调用之间运行的一段通用代码称为控制流函数。

## 9.Node.js 中的两类 API 函数是什么？

Node.js 中的两种 API 函数是
a)异步、非阻塞函数
b)同步、阻塞函数

## 10.使用 node.js 有什么好处？

a)它提供了一种构建可伸缩网络程序的简单方法
b)一般来说很快
c)巨大的并发性
d)异步一切
e)几乎从不阻塞

## 11.async.queue 的两个参数是什么？

async.queue 取的两个参数
a)任务函数
b)并发值

## 12.为什么 node.js 很快受到 JAVA 程序员的关注？

Node.js 很快受到关注，因为它是一个基于循环的 JavaScript 服务器。Node.js 使用户能够在服务器上编写 JavaScript，服务器可以访问 HTTP 堆栈、文件 I/O、TCP 和数据库。

## 13.使用事件循环，哪些任务应该异步完成？

a) I/O 操作
b)繁重的计算
c)任何需要阻塞的东西

## 14.可以访问 node 中的 DOM 吗？

不，您不能在节点中访问 DOM。

## 15.node 在 windows 上运行吗？

是的-确实如此。从[http://nodejs.org/download/](http://nodejs.org/download/)下载 MSI 安装程序

## 16.Node.js 和 Ajax 有什么区别？

Node.js 和 Ajax 的区别在于，Ajax(Asynchronous Javascript 和 XML 的缩写)是一种客户端技术，通常用于更新页面内容而无需刷新页面。而 Node.js 是服务器端 Javascript，用于开发服务器软件。Node.js 不在浏览器中执行，而是由服务器执行。

## 17.Node.js 如何克服 I/O 操作的阻塞问题？

Node.js 通过将基于事件的模型置于其核心，使用事件循环而不是线程来解决这个问题。

## 18.Node.js 有哪些利弊？

优点:
a)如果您的应用程序没有任何 CPU 密集型计算，您可以在 JavaScript 中从上到下构建它，如果您使用像 MongoDB 这样的 JSON 存储对象 DB，甚至可以下至数据库级别。
b)爬虫接收完整呈现的 HTML 响应，这比在 Node.js 上运行的单个页面应用程序或 websockets 应用程序要友好得多
缺点:
a)任何密集的 CPU 计算都会阻碍 node.js 的响应，因此线程平台是更好的方法。
b)对 Node.js 使用关系数据库被认为不太好

## 19.提及在 Node.js 中异步的步骤？

通过以下步骤，您可以异步 Node.js
a)第一类函数
b)函数组合
c)回调计数器
d)事件循环

## 20.Node.js 中的事件循环是什么？

为了处理外部事件并将其转换为回调调用，使用了事件循环。因此，在 I/O 调用时，node.js 可以从一个请求切换到另一个请求。

## 21.node.js 中的‘回调’是什么？

node.js 中使用回调函数来处理对服务器的多个请求。例如，如果您有一个大文件，服务器读取该文件需要很长时间，如果您不希望服务器在处理其他请求的同时读取该大文件，可以使用回调函数。回调函数允许服务器首先处理挂起的请求，并在请求完成时调用一个函数。

## 22.提到 node.js 中最常用的框架？

“Express”是 node.js 中最常用的框架

## 23.node.js 中用于导入外部库的命令是什么？

命令“require”用于导入外部库，例如，“var http=require ("http ")”。这将通过 http 变量加载 http 库和单个导出的对象。

## 24.node.js 中的“非阻塞”是什么意思？

在 node.js 中，“非阻塞”意味着它的 IO 是非阻塞的。Node 使用“libuv”以平台无关的方式处理其 IO。在 windows 上，它使用完成端口；在 Unix 上，它使用 epoll 或 kqueue 等。因此，它发出一个非阻塞请求，并根据请求在事件循环中对其进行排队，该事件循环在主 JavaScript 线程上调用 JavaScript“回调”。

## 25.Node.js 有哪些挑战？

强调一下技术方面，在 Node.js 中让一个进程和一个线程在多核服务器上纵向扩展是一个挑战。

我们已经在 Node JS 上给出了基本的 25 个重要面试问题及答案。如果您有任何其他问题或需要任何进一步的帮助，期待您的回复。
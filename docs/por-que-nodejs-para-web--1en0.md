# Por que Node.js para Web？

> 原文：<https://dev.to/netoolii/por-que-nodejs-para-web--1en0>

常常会出现这样一个问题，那就是为什么要使用哪种语言来构建您的后端。有多种语言，每种语言都有其独特之处和目的，尽管有多种语言可以用来创建后端，但其中一些已经引起了更多的关注。

[![cat confuss](img/cc7ae16ed491e3ec32ad300957f9cc8a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LZvxXyy9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pwlepwh5xp1mugb52l16.png)

当然，没有神奇的答案，更不用说回答这个问题了，但这是一个通用的解决方案，几乎适用于所有的情况。

此帖子的目的是演示 Javascript 如何工作，以及它是否符合您的目标，而不是教您如何制作服务器，因为该语言是众所周知的，并且有一个非常大的社区，因此很容易找到示例，包括帖子下面的建议链接。

## 首先要知道 Javascript 是什么以及它的作用方式。

Javascript 是一种解释语言、多范式、动态排版，最初设计为在 web 应用程序(即浏览器)前端运行。根据这个 github 项目:[https://madnight . github . io/git hut](https://madnight.github.io/githut)，它是最新的，是一个很好的参考，javascript 一直主导着 github 中打开项目的数量。

[![graph](img/29e1f5eb1d605a294a9399ebb1a6afeb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pKCYSUuS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AcZvMqoVXVrMNG1sRTNaLaw.png) 
GitHut，显示 2017 年数据，首先显示 JavaScript。

JavaScript 如何工作的一个很好的例子是 V8，您可以在这里找到更多的信息，Google Chrome 和 Node.js 中使用了这一信息，我们稍后将对此进行讨论。

JavaScript 是一种单线程编程语言，简而言之，它一次只能执行一项操作。
具有呼叫栈或呼叫栈，这是记录程序运行所在位置的结构。因为 Call Stack 是堆栈，所以每次进入某个函数时，都会将其放在堆栈的顶部，如果从某个函数返回，则会将其从堆栈的顶部取出。

[![cat stack](img/a2a7125f756f88a22613bdfdc97a76a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---ZHMojAD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AbaGg851dyIA4HmCcxrwl4Q.jpeg)

由于其基于事件的体系结构，您唯一的线程只负责侦听这些事件，以便在检测到这些事件后立即调用它们，因此语言可以通过这种方式解释这些事件，从而确保不会发生阻塞。由于请求不是连续的，而是异步的，因此对于大量请求来说，执行速度非常快，而且非常完美。所以不用多久就可以在后端使用了。

[![cat insterested](img/ff21c3e22c5eb39f2c5197ab806d3fd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PpNpOo8G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AyDgmWjzJIm8lWxNAaB3pkw.jpeg)

## Ok，mas e o Node.js？

基本上，它使用我们在上面看到的一切，只是运行在服务器端。
记住节点只是 JavaScript 运行时的机器，我们在节点旁边有 NPM 包管理器，该管理器为我们提供了一种简便快捷的方法来使用库和框架，类似于 PHP 的 Composer 等其他语言，它会维护一个 JSON 格式的文件，其中包含您项目中的数据和依赖关系。

好吧，现在头条的答案是。

选择 WEB 节点的好处取决于您计划使用的项目类型，因为它运行简单、优雅、易于维护、易于实施且具有高度可扩展性(这是我们都希望看到的，rs)。

因此，如果您的项目主要需要:

1.  **API:**API 的响应时间和请求数量对任何服务器都非常重要。Node.js 的设计正是为了解决这种情况，而不会损失性能或消耗大量硬件资源。以下是本机处理 restful api 的框架的最佳建议: [Loopback](https://loopback.io/) 。
2.  **实时应用程序和多人游戏:**由于实时应用程序自然是多用户的，因此节点可以轻松地通过跨不同设备传输来保持高流量和数据密集型，因为保持异步通信被认为是这种情况下的最佳选择之一为处理实时信息而制定的框架: [Sails](https://sailsjs.com/) 。
3.  **数据流:T1】读写对于任何编程语言来说都是昂贵的，但由于节点以异步方式运行，因此这样做花费较少。为实时通信而开发的两个图书馆，重点是流道: [Socket.io](https://www.npmjs.com/package/socket.io-stream) 和 [Kurunt](https://www.npmjs.com/package/kurunt) 。**
4.  **物联网:**没有特殊的体系结构原因，但编程工具使许多 IOTs 开发人员将其服务器构建在 Node 上，是 [Node-Red](https://nodered.org/) 使服务器构建变得简单直观，因此没有

#### Para saber mais sobre Node:

1.  [Node.js](https://nodejs.org/en/)
2.  [发布 sobre lugares que usam 节点](http://www.luiztools.com.br/post/por-que-aprender-nodejs/)

#### 参考资料:

1.  [https://blog . session stack . com/how-does-JavaScript-actually-work-part-1-B0 bacc 073 cf](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)
2.  [https://www . trenaweb . com . br/blog/node-js-por-por-vocate-must-know-这项技术/](https://www.treinaweb.com.br/blog/node-js-por-que-voce-deve-conhecer-essa-tecnologia/)
3.  [https://medium . com/thdeveloper/node-js-o-que-% C3 % a9-por-使用-e-first-steps-1118 f 771 b 889](https://medium.com/thdesenvolvedores/node-js-o-que-%C3%A9-por-que-usar-e-primeiros-passos-1118f771b889)
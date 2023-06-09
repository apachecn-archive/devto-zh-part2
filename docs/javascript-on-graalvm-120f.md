# GraalVM 上的 JavaScript

> 原文：<https://dev.to/pmlopes/javascript-on-graalvm-120f>

当我们想到服务器端的 JavaScript 时，我们的脑海中总会想到`nodejs`，但这是正确的吗？有哪些替代方案？为什么我们需要/关心这些替代品？

服务器端的 JavaScript 已经成为主流，像切片面包一样，人人都知道，人人都试过，人人都用(优步，网飞甚至微软...).

Node 让这一切成为可能。这是一个简单但不简单的运行时，给你足够的时间来构建强大的应用程序。

在我的工作中，我认为自己是一个通晓多种语言的开发人员，所以我一直被`Java`、`JavaScript`和其他语言包围着，这让我接触到不同的运行时、`JVM`、`V8`、`ChakraCore`以及最近的 [GraalVM](http://www.graalvm.org/) 。根据他们的网站:

> GraalVM 是一个通用虚拟机，用于运行用 JavaScript、Python 3、Ruby、R、基于 JVM 的语言(如 Java、Scala、Kotlin)和基于 LLVM 的语言(如 C 和 C++)编写的应用程序。

为什么要关心另一个运行时呢？嗯，对我来说，这一切都是从我看到 Techempower 基准测试并注意到与普遍认为的相反，JavaScript 并不是真的超级快！(我相信是超高产的，但是速度很值得商榷)。当然，基准测试应该有所保留，这一次非常有趣，因为它比较了不同语言和运行时的大约 300 个不同的框架。

[![techempower](img/7c6514521e75171843e57cdb56bc7990.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F6xtZHY---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lp2po9c2odbj0la0wzkw.png)

令我绝望的是，`nodejs`的最好成绩排在第 52 位，所以我决定看看我们能做些什么来改善这一点。查看基准源代码并没有多大帮助，因为没有明显的性能瓶颈，所以我决定看看盒子之外的 / `V8`。

[![graaljs-performance](img/ec00e77667d69973dd565d89a9ee83b9.png)T2】](https://www.slideshare.net/ThomasWuerthinger/jazoon2014-slides)

因此，乍一看，`GraalJS`似乎并没有给我们带来太多的改进，只是在这里或那里取得了一些胜利，但是另一方面，Techempower 基准测试表明，从长远来看，JVM 上的作业往往是最快的。

为了验证我的前提，我已经开始为 [Eclipse Vert.x](http://vertx.io/) 或 [ES4X](https://reactiverse.io/es4x) 开发一个名为 EcmaScript 的小项目。想法很简单:

> 使用 JVM/GraalVM 的功能，在 Eclipse Vert.x 工具包的基础上创建一个现代化的 EcmaScript 运行时

为了保持项目的可维护性，我决定保持最小化:

1.  提供一个 **`commonjs`** 模块加载器
2.  使用节点/npm 样式开发又名 **`package.json`**
3.  支持使用 chrome inspector 进行调试
4.  IDE 支持使用**类型脚本定义**

经过几次迭代和反馈后，我决定实现 bechmark。所以我从:
开始

```
npm init -y
npm add vertx-scripts --save-dev
npm add @vertx/core --save-prod
npm add @vertx/web --save-prod
npm add @reactiverse/reactive-pg-client --save-prod
npm install 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，这看起来非常友好，所以我已经实施了基准测试，并通过了社区审查和合并到主分支的过程。当这一切发生时，令人惊奇的事情出现了:

[![bench-db](img/93d18a18d3f5ba5a7cec85d36d443574.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bMZYT8cW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/avcuo8l2emaly6mu57e5.png)

`JavaScript`和`Vert.x`不仅仅是**快**，而且是迄今为止与`nodejs`(或`Spring Framework`作为 Java 中最常用框架的参考)相比**最快的**，甚至比使用相同依赖项`vert.x`和`vertx-web`的基准测试的纯`Java`对手还要快。

事实上，结果是惊人的，因为它将`JavaScript`列为基准测试中最快的 **#2** 语言:

[![es4x-fastest](img/b95bd5a4ecddfe53095a0c719a4bb818.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZqoSb9vE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h5s3h0le6t4urhbsokqz.png)

所以下次你需要编写高性能的 JavaScript 服务器代码时，看看 [ES4X](https://github.com/reactiverse/es4x) ，或者加入这个项目，让它变得更好！！！
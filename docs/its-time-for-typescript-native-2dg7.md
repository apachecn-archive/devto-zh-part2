# 该是打字稿原生的时候了

> 原文：<https://dev.to/zohaibility/its-time-for-typescript-native-2dg7>

除非你一直生活在岩石下；你一定知道这个项目 [Deno](https://github.com/denoland/deno) 。是的，这个项目是由编写 Node.js 的同一个人完成的，根据他的说法，这是他对自己在 Node.js 中所犯错误的救赎:

[![nickytonline](../Images/58e6eb5aa952b0501600a8c841851ced.png)](/nickytonline) [## Node.js 让我后悔的 10 件事

### 尼克·泰勒(他/他)6 月 7 日 181 分钟阅读

#discuss #typescript #go #node](/nickytonline/10-things-i-regret-about-nodejs-14m3)

而项目是写在铁锈和它使用相同的 v8 引擎引擎盖下。这次他做了一个有趣的选择。他选择了 Typescript 作为主要语言，而不是 JS。

现在，这是一个好还是坏的选择是一个单独的辩论，只有时间会告诉我们。我个人认为，在性能调优和开发人员可伸缩性方面，类型化语言比动态语言更相似。有人可能不同意；但是在 Node.js 的大肆宣传停止之后，我们可以清楚地看到 Golang 这样的玩家和 Java/C++/C/C# [这样的中邪恐龙仍然占据着头把交椅](https://www.techempower.com/benchmarks/)。

在浏览 Deno 的源代码时，我想到了 Deno 是否可以像 Go 一样将 TS 项目预编译成一个二进制文件？完全不用依赖于 V8 的 JIT 和复杂流水线怎么样？如果有人编写严格类型化的代码，我们可以从 Typescript 所具有的类型提示中受益匪浅，并且仍然保持良好的特性。

当微软的某个人正在努力寻找一个能让他获得下一次晋升的功能时，我却在期待别的东西。随着 [CLR 现在跨平台](https://github.com/dotnet/coreclr)扔掉 C#球，为我们写一个**类型脚本原生**编译器。在我看来，拥有一个已经理解并编写了 Typescript 的庞大社区，微软更有可能成功！微软可以继续与 Golang 正面交锋，增加。Net core 比现在更受欢迎。如果有人能给我一个像 Typescript 这样的语法，并能让我从 Golang 中获得所有的性能优势，我会放弃 Golang。
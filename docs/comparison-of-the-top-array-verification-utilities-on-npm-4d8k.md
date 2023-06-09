# npm 上顶级阵列验证实用程序的比较

> 原文：<https://dev.to/bnevilleoneill/comparison-of-the-top-array-verification-utilities-on-npm-4d8k>

<figure>[![](img/ca0ba29f7647e369f9ee374235c987c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wlxO_jt9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Alla6x9C9gvtRXkykD3OJ6w.png) 

<figcaption>来源:[http://www.monkeyuser.com/2017/npm-delivery/](http://www.monkeyuser.com/2017/npm-delivery/)</figcaption>

</figure>

几周前，我在做我的业余项目 array-length，这是一个高性能的新 JavaScript 库，用于计算 JavaScript 数组中的元素数量，当时我面临一个有趣的挑战。我需要一种方法来验证用户的输入确实是一个数组，而不是可能破坏我的算法的复杂控制流的其他类型的对象。

正如任何足够有利可图的商业机会经常出现的情况一样，市场上有各种旨在解决这一问题的解决方案。从适合初创公司和中端市场客户的专注、同类最佳的库，到提供广泛功能的高度可扩展的企业级解决方案，不一而足。

鉴于有无数的选择，我惊讶地发现，几乎没有文献可以帮助买家做出明智的决定。也许阵列验证市场太新，Gartner 和 Forrester 之类的公司无法覆盖，这是我对 JavaScript 库进行评论的典型来源。

取而代之的是，我决定花几个星期的时间来观察这片风景，并把我的发现写下来。下面是我对前三名解决方案的总结，涵盖了各种指标，包括性能、可伸缩性、可维护性、互操作性、可调试性、可读性和网络可伸缩性。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)T2】](https://logrocket.com/signup/)

### 被阵列

isarray 是一个优秀的 npm 包，可以准确地检测出它的给定参数是不是数组。而 README 声明，“ *isarray 用于较旧的浏览器和不推荐使用的 Node.js 版本。”*我发现它与新浏览器和最新版本的 Node 配合得相当好。

它接受一个参数并返回一个布尔值，判断给定的对象是否确实是一个数组。

#### 用法

```
var isArray = require('isarray');
console.log(isArray([])); // => true
console.log(isArray({})); // => false 
```

Enter fullscreen mode Exit fullscreen mode

**优点**

*   简洁、高度表达的语法
*   与 MongoDB 配合良好
*   出色的投资回报率(只有 2 个月的回报期)

**缺点**

*   没有第三方插件生态系统
*   3 个公开的 GitHub 问题

### 是-阵

is-array 采用了一种完全不同的 JavaScript 数组验证方法，在包标题中的 is 和 array 之间选择一个连字符，而不是一个空字符串。与 isarray 的复合标题相比，它显著提高了可读性，同时还提供了有眼光的工程师所期望的大部分功能。

它接受一个参数并返回一个布尔值，判断给定的对象是否确实是一个数组。

#### 用法

```
var isArray = require('is-array');
console.log(isArray([])); // => true
console.log(isArray({})); // => false 
```

Enter fullscreen mode Exit fullscreen mode

**优点**

*   它很酷，因为它不太受欢迎

**缺点**

*   更长的名字意味着更大的源代码(更慢的 Git 推送，更少的可用硬盘空间，等等)
*   许可麻省理工学院许可证意味着一个大公司可以拥抱，扩展和消灭它

### Array.isArray()

最后一招，只适用于不允许使用第三方模块的情况(企业安全约束、在甜点岛上使用等)，Array.isArray()是一个内置的 JavaScript 方法，是随 ES5 标准一起引入的。

它接受一个参数并返回一个布尔值，判断给定的对象是否确实是一个数组。

#### 用法

```
console.log(Array.isArray([])); // => true
console.log(Array.isArray({})); // => false 
```

Enter fullscreen mode Exit fullscreen mode

**优点**

*   至少 95%准确

**缺点**

*   不在 npm 上
*   Opera 4 中不支持
*   如果您尝试使用 webpack 导入它，将会引发错误

### 参考文献

*   [https://www.npmjs.com/package/isarray](https://www.npmjs.com/package/isarray)
*   [https://www.npmjs.com/package/is-array](https://www.npmjs.com/package/is-array)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array/isArray](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray)

Rog Locket 是一名开发人员和幽默作家，他为 LogRocket 博客撰写每周讽刺专栏。他完全是虚构的。

* * *

### Plug: LogRocket，一款用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *
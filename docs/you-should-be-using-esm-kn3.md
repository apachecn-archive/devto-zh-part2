# 你应该使用 esm

> 原文：<https://dev.to/bennypowers/you-should-be-using-esm-kn3>

## TL；博士:

今天，你可以在 node 中使用 JavaScript 模块，而无需传输文件，只需`npm -S esm`并使用`node -r esm foo.js`运行你的应用程序。将`"esm": "auto"`添加到你的 package.json 的顶层，使得在同一个应用程序中加载模块和 CJ 变得轻松透明。

如果你已经坚持了这么久，请继续阅读我们是如何走到这一步的固执己见的历史。

[历史](#the-history)

[问题](#the-problem)
[`esm`:更好的解决方案](#esm-a-better-solution)

## 历史

历史上，JavaScript 只适用于浏览器。开发人员使用了许多技术来构建他们的代码，所有这些技术基本上都是对全局变量的抽象。在这些解决方案中，出现了一种名为 CommonJS(或“cjs”)的大众最爱。

```
const { foo } = require('./bar')

const baz = foo + "qux"

module.exports = {
  quux: [baz]
} 
```

Enter fullscreen mode Exit fullscreen mode

CJS 在 JS 开发人员中赢得了关注，主要是因为它是 NodeJS 使用的模块系统。前端开发者可以用像 [webpack](https://webpack.js.org/) 这样的工具*捆绑*基于 cjs 的应用到浏览器可以加载和运行的单个文件脚本中。

一个代码库可以同时在服务器和客户端上运行的想法(伴随着一定程度的工具争论)导致了诸如服务器端渲染、NativeScript/React Native 以及 webpack、 [babel](http://babeljs.io/) 等工具的激增，成为 JS 开发不可协商的先决条件。

2015 年，ECMAScript 第 6 版发布，其中包括语言级模块的语法。

```
import { foo } from './bar.js'

const baz = foo + "qux"

export const quux = [baz] 
```

Enter fullscreen mode Exit fullscreen mode

这些模块是静态的，只限于顶层，这意味着你不能做如下的事情。

```
const moduleName = "foo" + "bar"
if (baz) {
  // nope!
  import { quz } from moduleName
} 
```

Enter fullscreen mode Exit fullscreen mode

CJS 的用户已经习惯了。另一方面，js 模块是可静态分析的，这意味着像 [Rollup](https://www.rollupjs.com/guide/en) 这样的新型工具可以分析 js 文件来做一些有用的事情，比如树摇动，这是一个从包中移除未使用代码的过程。这有助于开发者发布更少的代码，让用户更快地加载网站。

补充说明:一个关于动态导入模块的提案已经进入第三阶段，并且已经可以在 T2 的许多浏览器中使用

如何加载模块和模块图(表示模块之间功能关系的逻辑结构)的细节留给实现者，即浏览器供应商和节点维护者。

浏览器厂商率先起草了[加载器规范](https://whatwg.github.io/loader/)，但是已经有了模块系统的 node 的情况更加复杂，[到今天为止，还没有最终的计划出现](https://medium.com/@giltayar/native-es-modules-in-nodejs-status-and-future-directions-part-i-ee5ea3001f71)，尽管他们已经接近了。

## 运输文件

当 ES2015 规范(当时称为 ES6 或“harmony”)发布时，一个名为 5-to-6 的项目(后来更名为 Babel)出现了，它让 JS 程序员使用令人敬畏的新 ES6 功能编写他们的应用程序，同时发布旧浏览器和 Internet Explorer 可以支持的代码。

将一种语言或一种语言的版本翻译成另一种语言的过程叫做*trans pilling*，是翻译和编译的综合体。

巴别塔已经演变成一种 JavaScript 瑞士军刀。它可以采用各种 JavaScript 版本，甚至是完全独立的语言，并将它们整合成在浏览器中运行的代码。

## 问题

Babel 为 web 开发人员带来了巨大的好处。它使得新的或提议的特性在被浏览器实现之前被一起探索*,这有助于暴露那些特性的边缘情况，导致更好的规范。它也在 web 开发正在经历的从 OOP/procedural 范式到更具功能性的范式的巨变中发挥了巨大的作用。Babel 也为今天的 web 开发人员提供了各种各样的工具和产品...*

 *...但这并不是必须的，这有时会有点问题。

### 运输的费用

开发者杰米·k 说得好:

> ![unknown tweet media content](img/9eab89aae49d8af655acffa3fb3d6982.png)![Jamie K 🏳️‍🌈 profile image](img/5a1e084ac37788fbfef751283e242bb2.png)杰米 K 🏳️‍🌈[@ Jamie builds](https://dev.to/jamiebuilds)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)对 JavaScript 回拖最多的三款浏览器是:
> 
> 1) IE 11 (2.7%的网页)
> 2) Android 4.4-4.4.3 (0.94%的网页)
> 3) Chrome 49 (0.71%的网页)
> 
> 如果我们停止支持这些浏览器...(续)2018 年 7 月 26 日下午 19:46[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1022568918949408768)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1022568918949408768)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1022568918949408768)

向现代浏览器和 ie8 等提供一个大的、一刀切的捆绑包的商业案例正在迅速消失。像[差分服务](https://github.com/Polymer/prpl-server-node)这样的现代技术让我们为有能力的浏览器提供优化、精简的 ES2018，同时为那些不太好的浏览器保留臃肿、传输的包。除此之外，对于 IE11 支持不是绝对业务需求的应用程序，当用户可以并且应该使用最新最好的浏览器时，支持旧的、不安全的浏览器实际上是不负责任的。

### 原则和认知负荷

在节点世界中，传输也伴随着成本。维护一个巴别塔配置并不总是最简单的任务。然而，更重要的是，transpiling 巧妙地传达了“这个代码本身是不正常的，它需要额外的处理才能正常”，我们不应该这样说本机模块，即使 CJS 已经领先一步。

## `esm`:简单的解决方法

`esm`是微软开发者[约翰-大卫道尔顿](https://github.com/jdalton)洛达什成名，*等人*的一个优秀的软件包。它是一个模块加载器，在运行时转换 es 模块，而不是传输它们。

有了`esm`,难以捉摸的‘互操作’就可以工作了。您可以毫不费力地混合搭配本地和 CJS 模块。

你甚至可以使用大多数命令行节点应用程序！例如，优秀的 [`tape`](https://github.com/substack/tape) 测试库并没有开箱即用的模块支持，但是您可以像这样轻松地添加:

```
npx tape -r 'esm' './**/*.test.js' 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

下次你有一个 node.js 项目，在你开始为 transpile 模块写一个 babel 配置之前，试试`esm`。*
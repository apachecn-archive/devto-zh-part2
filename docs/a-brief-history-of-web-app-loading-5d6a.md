# web 应用程序加载简史

> 原文：<https://dev.to/vcarl/a-brief-history-of-web-app-loading-5d6a>

一开始，有一个`<script>`标签。

我们通过在 HTML 中仔细安排脚本来管理依赖性。你必须在加载插件之前加载 jQuery，在加载应用程序代码之前加载库。随着我们开始增加更多的交互性，并从网站发展到网络应用，这种情况开始失控。大型项目有复杂的请求瀑布，难以管理和优化。我们有`defer`和`async`属性，但是它们只在某些情况下有用。我们需要一种更好的方法来管理我们的依赖性。

第一步是当我们开始把我们的脚本连接在一起。这减少了 HTTP 请求的总数，并有助于保证执行顺序，但它仍然是一个手动过程。脚本必须以正确的顺序连接在一起才能工作。我们将脚本连接成组，以根据请求总数平衡每个文件的大小，但是我们仍然必须指定顺序和分组。这是 Javascript 构建步骤的概念流行起来的时候了。

Grunt 成为第一个广受欢迎的“任务运行者”，用于连接脚本和优化资产。然而，在大型项目中，它的配置变得笨拙，而 [Gulp](https://gulpjs.com/) 将这些想法提炼为一个“流”API，推理起来更简单，速度更快。

随着我们对构建步骤的想法越来越熟悉， [CoffeeScript](https://coffeescript.org/) 成为第一个流行的替代语法。有这么多用 Ruby on Rails 编写的应用，web 开发人员渴望 Ruby 更简单的语法。CoffeeScript 的许多想法最终都融入了 es 2015——你可以感谢它的`=>`和`...`等等。它帮助推广的另一个概念是将代码分成模块。每个编译好的 CoffeeScript 文件都被插入到它自己的 IIFE(立即实例化的函数表达式)中，限定每个脚本的范围以防止污染全局名称空间。

[Require.js](https://requirejs.org/) 和[鲍尔](https://bower.io/)来到现场，帮助我们争论我们的第三方代码。Require.js 引入了“异步模块定义”，或 AMD 模块，一些应用程序仍在使用这种打包方法。它们被按需加载到浏览器中，超级酷！不再需要手动调整脚本标签。语法有点笨拙，

```
// from http://requirejs.org/docs/api.html
requirejs(['jquery', 'canvas', 'app/sub'],
  function($,        canvas,   sub) {
    //jQuery, canvas and the app/sub module are all
    //loaded and can be used here now.
}); 
```

Enter fullscreen mode Exit fullscreen mode

但这比我们自己手动管理订单要好得多。Bower 最初是 npm 的补充，在 npm 有许多支持在浏览器中运行的模块之前。最终，Bower 被弃用，取而代之的是 npm，Require.js 增加了传递 Require 函数的选项，以模拟来自 node 的 commonJS 模块。

```
define(function(require, exports, module) {
  var $ = require('jquery');
  var canvas = require('canvas');
  var sub = require('app/sub')
}) 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们有了自动管理加载哪些脚本以及加载顺序的东西。生活很美好。慢慢地，一个新的问题开始出现:添加依赖关系太容易了，我们开始大量使用。因为每个依赖项都是作为一个单独的脚本加载的，所以加载一个 web 应用程序会引发几十个——甚至几百个——对 tiny 的 HTTP 请求。js 文件。同时发生的请求会互相阻止加载，从而延迟初始加载。

为此开发了几个修复程序。HTTP2 的设计考虑到了这个问题，增加了多路复用来帮助缓解这个问题。Require.js 添加了一个优化工具，可以将这些模块捆绑到一个文件或一组文件中，但它不适合开发，而且配置起来很棘手。HTTP2 的推出非常缓慢，最终并不是人们所希望的银弹。

开发人员开始尝试替代方案，捆绑依赖关系的工具数量激增。Browserify、Broccoli.js、Rollup、webpack，当然还有其他我从未听说过的。还有更多的正在被创建，据我所知，Parcel 是最新的一个。它们在 API 和特性上都略有不同。webpack 因其出色的代码拆分功能和灵活性赢得了应用程序的 mindshare，后来的迭代显著提高了可用性(说真的 webpack 4 太棒了)。Rollup 已经成为捆绑库的常用工具，因为它在大多数情况下生成最小的包。

[![An animated gif showing an analyzer's output, with several JS bundles containing many dependencies](img/4420a709885fa3825d8a099ee029c24e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dtO0cVoQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cloud.githubusercontent.com/assets/302213/20628702/93f72404-b338-11e6-92d4-9a365550a701.gif)

对解决依赖关系的工具的关注揭示了 CommonJS 的函数的一些缺点。`require`是 Node.js 的一部分，它的一些语义使得在浏览器中使用更加困难。TC39 标准化了模块定义规范，ES 模块，更好地满足了 Node.js 和浏览器中的不同用例。它还在不断发展——[node . js 最近发布了第 10 版](https://nodejs.org/docs/latest-v10.x/api/esm.html)，提供实验支持，而[动态`import()`功能](https://github.com/tc39/proposal-dynamic-import)还没有完全落地。

这就把我们带到了今天。Webpack 是几年来事实上的捆绑器，并且这些年来一直在稳步改进。我们不仅可以定义 Javascript 包，还可以指定哪些文件依赖于样式表或图像，并仅在需要时加载它们。加载器的存在是为了内联特定大小以下的图像，一些疯狂的人已经开始在他们的 JS 中编写 CSS 了(试试吧，很棒)。

我甚至没有提到 Yarn vs npm vs pnpm，unpkg 之类的服务，或者任何让我们走到今天的戏剧性事件和争论。npm 在 2016 年达到每周 10 亿次下载后，已经进入了平流层，2018 年初在 T2 的数字让那些人相形见绌。我们今天面临的挑战是何时不使用依赖项，以及关注我们发布的代码总量。

这只代表了我在过去 6 年编写运行在浏览器中的代码的第一手经验。在互联网的历史上，这只是很短的一段时间，但是创新和发展的数量令人难以置信。

* * *

感谢阅读！我在 Twitter 上的名字是 [@cvitullo](https://twitter.com/cvitullo) (但在其他大多数地方我都是 vcarl)。我主持了 React 开发者聊天室[React flux](http://join.reactiflux.com/)和 Node 聊天室 [Nodeiflux](https://discordapp.com/invite/vUsrbjd) 。JS 开发者。如果您有任何问题或建议，请联系我们！
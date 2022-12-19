# 选择 JavaScript 构建工具:配置还是不配置

> 原文：<https://dev.to/netlify/choosing-a-javascript-build-tool-to-config-or-not-config-2ia8>

构建工具已经成为现代 web 应用程序工作流中的必要组件。我之前已经介绍了[构建工具能为您做什么的基础知识](https://www.netlify.com/blog/2017/11/15/what-build-tools-can-do-for-you/)来展示构建如何帮助编写脚本、自动化和消除复杂性。考虑到这些因素，我将提供一些更流行的构建工具，以及它们如何对您的项目有意义。

这篇文章并不是详尽的列表。相反，它旨在提供一些建议，帮助您开始研究不同的构建工具如何改进开发工作流。在阅读构建工具的比较时，您将获得一些知识，以便更好地比较这些工具如何符合您的特定需求。

## 我们又在比较什么？

本指南将只关注能够执行项目模块捆绑的构建工具，项目模块捆绑是将动态内容缝合到模块中并将内容捆绑到静态资产文件中的过程。这个过程可以使用脚本、自动化和缩小化来增强。不是所有的构建工具都是一样的，也不是所有的构建工具都能做到以上所有的事情。有些工具需要更多的配置，有些是现成的解决方案，可以满足您的大部分需求。

很可能你会有(或发展出)一个偏好，你希望一个构建工具做多少事情。因此，这篇文章不是选择一个构建工具来管理它们，而是涵盖每个工具的优点、缺陷和理想的项目概况。

| 工具 | 优势 | 逮到你了 | 理想项目 |
| --- | --- | --- | --- |
| 浏览 | 简单 | 开发仅由社区插件驱动 | 小型项目或原型 |
| 网络包 | 灵活的配置和代码分割 | 配置可读性 | 大中型 Web 项目 |
| Rollup.js | 可共享库的捆绑 | 模块捆绑已经内置到浏览器中 | 库或插件 |
| 吞咽 | 任务运行程序 | 用于模块捆扎机的配套工具 | 需要额外脚本的项目 |
| npm | 内置于大多数 JavaScript 项目中 | 手动设置 | 适用于大多数项目 |
| 包裹 | 不需要配置 | 可用文档较少 | 大中型 Web 项目 |
| 微束 | 占地面积小 | 可用文档较少 | 有大小意识的库或插件 |

## 浏览确认

优势:非常简单
问题:项目的开发已经慢了很多
理想的项目:希望摆脱大量使用脚本标签并转而使用 CommonJS 的项目。

[Browserify](http://browserify.org/) 关注入门的简单性，它是 JavaScript 开发中模块捆绑的一个很好的介绍。Browserify 最初是作为一种方式出现的，它允许前端开发人员在浏览器中使用[CommonJS](http://requirejs.org/docs/commonjs.html)(require statements)，就像在服务器中呈现[节点](https://nodejs.org/en/)应用程序一样。以前的 web 开发在页面上使用多个脚本标签来构建现代 web 应用程序。该工具将所有 JavaScript 文件浏览到一个连接(组合和合并)的文件中，该文件可以作为单个脚本标签轻松包含在页面中。

[![browserify-logo](../Images/3ebb8c2f0be02c4aecb18f4181571885.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kaYg6UV9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/browserify/browserify/HEAD/assets/logo.png)

使用 Browserify 从安装 CLI 开始。我建议从命令行使用 npm。

```
 npm install browserify 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，您可以将应用程序的 JavaScript 入口点(很可能是 index.js)指向一个位置，开始浏览过程。

```
 browserify index.js > bundle.js 
```

Enter fullscreen mode Exit fullscreen mode

结果是您的 JavaScript 的捆绑版本可以包含在您的 index.html 中。

```
 <script src="bundle.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

Browserify 实现是功能完整的，并且集中于开箱即用的 JavaScript 改进。为了支持非 JavaScript 资产的捆绑，如 CSS 或图像，有一个健康的列表社区创建的[转换](https://github.com/browserify/browserify/wiki/list-of-transforms)(都以 ify 结尾命名，多么聪明)来为这些解决方案提供资源。我是一个争取开源社区来进一步发展项目的超级粉丝，但是如果你正在尝试 Browserify，请注意:一些转换已经超过六个月没有收到新的更新了。也就是说，通过为您的项目解决方案提供插件，有很多领域可以回馈社区。

与其他一些构建工具不同，Browserify 没有标准的配置文件需要维护。然而，您可以利用节点 [package.json 来处理更多高级配置](https://github.com/browserify/browserify#packagejson)。Browserify 的工作是通过插件和 JavaScript 文件中的内容推断出来的。对于不经常需要更新的项目来说，这可能是一件好事。对于需要大量工具和优化的项目，丢失的配置文件可能会成为负担，因为没有真实的来源或地方来暴露构建工具的魔力。

查阅 Browserify 文档以及转换列表,看看它包含了让您的开发工作流程愉快所需的一切。你也可以使用这个关于[如何用 Browserify](https://codeutopia.net/blog/2016/01/25/getting-started-with-npm-and-browserify-in-a-react-project/) 构建一个 React 应用的教程来看看它的运行。如果简单是你所需要的，那么 Browserify 是你的下一个项目我会考虑的。

## Webpack

优势:积极支持的项目，具有大量现成的功能
问题:需要一点定制配置来获得正确的功能
理想项目:希望跟上最新和最大变化的项目。希望进行代码分割的项目也应该考虑 webpack。

Webpack 是一个构建工具，基于 4 个主要概念:入口、输出、插件和加载器。一旦你理解了[围绕这些概念的想法](https://www.netlify.com/blog/2017/01/03/4-key-concepts-of-webpack/)，你就可以让 Webpack 在一个项目中运行起来。Webpack took 通过一个[插件社区](https://webpack.js.org/plugins/)在某些方面与 Browserify 有相似的增强功能。然而，Webpack 提供了更多开箱即用的功能，并计划继续添加更多功能，并不断地[重新思考项目的设计](https://medium.com/webpack/webpack-4-changes-part-1-week-24-25-fd4d77674e55)。

[![webpack-logo](../Images/45a3004d82866391ee3ea4a075301468.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hODTV-JD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/webpack/media/blob/master/logo/logo-on-white-bg.png%3Fraw%3Dtrue)

我之前写了一篇关于从零开始使用[web pack](https://www.netlify.com/blog/2017/11/30/starting-with-webpack-from-scratch/)的指南，重点是利用 Webpack CLI 来构建 React 应用程序。Webpack 要求您创建单独的配置文件来支持您的 Webpack 构建工作。这个文件只不过是一个 JavaScript 对象，Webpack 使用它根据 config 对象中的键和值在构建过程中启用和禁用特性。

```
 // example of a webpack.config.js

    module.exports = {
      entry:'./index.js',
      output: {
        filename: 'bundle.js'
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在配置中，您可以确定项目的入口点以及您想要放置包的位置。这使得运行 Webpack 构建更加简单，因为不需要记住特定的命令，您只需`webpack`来创建您的构建。

```
 npm install webpack
    webpack 
```

Enter fullscreen mode Exit fullscreen mode

Webpack 配置是一种添加新特性和工具来增强构建过程的好方法，但是像大多数好方法一样，这里或那里的一些添加会导致您的配置膨胀成一种不可管理的形式。看起来不可管理的配置可能是这样一种形式，项目开发团队避免更改或更新 Webpack 配置，因为害怕由于添加到 Webpack 配置文件中的内容过多而破坏构建。

React 团队通过将配置抽象到 create-react-app CLI 工具下的隐藏脚本中，解决了这个 Webpack 问题。如果你看一下[隐藏的配置](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/config/webpack.config.dev.js)，该文件有一些你可能在配置中见过的最好的布局注释，但是它需要如此多的注释这一事实使你质疑是否有更好的方法来拥有这样的微调配置，而不需要注释墙来支持每个决策。

```
 // excerpt from the creat-react-app's webpack config

    module.exports = {
      // You may want 'eval' instead if you prefer to see the compiled output in DevTools.
      // See the discussion in https://github.com/facebookincubator/create-react-app/issues/343.
      devtool: 'cheap-module-source-map',
      // These are the "entry points" to our application.
      // This means they will be the "root" imports that are included in JS bundle.
      // The first two entry points enable "hot" CSS and auto-refreshes for JS.
      entry: [
        // We ship a few polyfills by default:
        require.resolve('./polyfills'),
        // Include an alternative client for WebpackDevServer. A client's job is to
        // connect to WebpackDevServer by a socket and get notified about changes.
        // When you save a file, the client will either apply hot updates (in case
        // of CSS changes), or refresh the page (in case of JS changes). When you
        // make a syntax error, this client will display a syntax error overlay.
        // Note: instead of the default WebpackDevServer client, we use a custom one
        // to bring better experience for Create React App users. You can replace
        // the line below with these two lines if you prefer the stock client:
        // require.resolve('webpack-dev-server/client') + '?/',
        // require.resolve('webpack/hot/dev-server'),
        require.resolve('react-dev-utils/webpackHotDevClient'),
        // Finally, this is your app's code:
        paths.appIndexJs,
        // We include the app code last so that if there is a runtime error during
        // initialization, it doesn't blow up the WebpackDevServer client, and
        // changing JS code would still trigger a refresh.
      ],
      output: {
        // Add /* filename */ comments to generated require()s in the output.
        pathinfo: true,
        // This does not produce a real file. It's just the virtual path that is
        // served by WebpackDevServer in development. This is the JS bundle
        // containing code from all our entry points, and the Webpack runtime.
        filename: 'static/js/bundle.js',
        // There are also additional JS chunk files if you use code splitting.
        chunkFilename: 'static/js/[name].chunk.js',
        // This is the URL that app is served from. We use "/" in development.
        publicPath: publicPath,
        // Point sourcemap entries to original disk location (format as URL on Windows)
        devtoolModuleFilenameTemplate: info =>
          path.resolve(info.absoluteResourcePath).replace(/\\/g, '/'),
      },

      // ... there is so much more to this 
```

Enter fullscreen mode Exit fullscreen mode

Webpack 团队正在积极开发这个项目，并尽他们的努力来消除围绕配置的混乱。许多曾经需要 Webpack 插件的缺失功能现在都包含在库本身中，包括树抖动、丑化，甚至 web 组装(WASM)支持。通过文档精心编写的[也有助于使这个 Webpack 作为一个构建工具更加平易近人，并且自 web pack 2(2016 年秋季)推出以来一直得到持续维护。](https://webpack.js.org/concepts/)

Webpack 不仅关注模块捆绑，它还将代码拆分作为一种内置功能。代码拆分是在需要时，通过利用通常基于路由的单独页面拆分捆绑来仅加载所需内容的做法。这有可能提高页面加载速度和整体浏览体验。然而，代码分割确实有一个学习曲线，我个人还没有完全学会，但 Webpack 团队成员正在尽最大努力用 [webpack.academy](https://webpack.academy/) 拉平这条曲线。

有许多社区构建的 Webpack 配置样板文件，包括一个名为 [Webpackbin](https://www.webpackbin.com/) 的非常简洁的工具。Webpackbin 是一个沙箱，用于构建和配置 Webpack 示例。您可以从这里生成链接，这在研究 Webpack 配置时很好，因为作者倾向于在沙盒中发布他们的配置，并提供 URL 来共享。

[![https://www.webpackbin.com/bins/-KeVMcQWGocAn5VJL6XL](../Images/b1366d4927002ac25ec0c01f9e20eda6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0Dy8-dsD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6EABB039D157C13FE0965A1755676EBA7A2B906D3263E4D65C1B144D2FFE7B1B_1515177774353_Screenshot%2B2018-01-05%2B10.42.33.png)

Webpack 正朝着包含电池的方向努力，但有些部分是单独出售的构建工具。Webpack 几乎可以处理你现在在使用 web 应用程序时所关心的每一个问题，但是你也可能需要大量阅读手册([文档](https://webpack.js.org/concepts/))来使它的构建和运行符合你的喜好。

## Rollup

优势:包管理的内置特性
问题:您需要确保您的项目已经实现了 es6 语法
理想项目:希望在构建过程中使用较少配置，并且已经使用了最新的 ESNext 特性(如 ES 模块)的项目

Rollup 是 JavaScript 的一个模块捆绑器，它将小段代码编译成更大更复杂的代码。它使用 JavaScript 的 ES6 模块系统的新版本，而不是以前的特殊解决方案，如 CommonJS 和 AMD，来执行项目的汇总(捆绑)。ES6 模块允许您自由无缝地组合您最喜欢的库中最有用的单个函数。

[![rollup js logo](../Images/449eae97f8c7fbef9f12c07c1f783370.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6PCDWgD---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rollupjs.org/images/twitter-card.jpg)

可以通过命令行开始使用汇总。只需指向 index.js 并为捆绑输出提供一个名称。

```
 npm install -D rollup
    rollup index.js --o bundle.js --f iife 
```

Enter fullscreen mode Exit fullscreen mode

为了避免我们不断重复相同的命令，您可以选择添加一个 rollup.config.js 文件，类似于我们在 webpack 中看到的。关于配置的相同风险在图 e
中有效

```
 // rollup.config.js
    export default {
      input: 'src/index.js',
      output: {
        file: 'bundle.js',
        format: 'cjs'
      }
    }; 
```

Enter fullscreen mode Exit fullscreen mode

Rollup 越来越受软件包和开放源码维护者欢迎，因为它具有与 web 应用程序相反的软件包管理的内置特性。一些特性反映了你可以用通用模块定义(UMD)做什么，并使 Rollup 成为 JavaScript 的 UMD 需求和 ES6 模块之间的桥梁。因为 ES6 是 hotness Rollup，没有插件就不能与 CommonJS 必需的文件[一起工作。对于那些还没有在工作流中实现 ES6 语法的老项目来说，这只是一个限制。然而，如果你开始一个新项目，对你来说没有太多的限制。](https://github.com/rollup/rollup-plugin-commonjs)

截至 2017 年春季，所有主要浏览器都支持 ES6 模块，Rollup 现在也希望获得新的竞争优势。Rollup 提供了对树抖动的本地支持，能够从您的项目包中移除未使用的代码，您可以在本例中从 [rollup repl](https://rollupjs.org/repl) 中看到这一点。这对于寻求页面优化帮助的项目来说很有价值。

[![rollup site](../Images/0ce68e450c7dfdcf573263c0466aea8e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B77kBAOc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/9gFG4eu.png)

虽然摇树看起来是一个小功能，但考虑一下像 [Momentjs](https://momentjs.com/) 或 [lodash](https://lodash.com/) 这样的大项目。树抖动提供了从您的包中排除库的所有部分的能力，并且只包括您正在使用的那部分库。

除了树之外，你还可以积累更多东西，所以我鼓励你参考[积累指南](https://rollupjs.org/guide/en)以获得[更多信息](https://rollupjs.org/guide/en#tree-shaking)如何在你的下一个项目中利用树摇动和其他特性。

## 大口

优势:一个连接其他构建工具的好工具
问题:如果您需要模块捆绑，这不是您的构建工具
理想项目:需要额外帮助将脚本特性融入构建过程的项目

[Gulp](https://gulpjs.com/) 是一个工具，它将脚本作为一个特性引入到你的工作流想法中。在列表中的所有工具中，Gulp 是一个我不会用于模块捆绑的工具，而是作为一个增强我的构建过程的工具。Gulp 是那些希望将脚本放入 JavaScript 的开发人员的理想选择。Webpack 和 Browserify 中缺少的许多功能可以通过 Gulp 命令将它们链接在一起而得到增强。Gulp 命令既可以用普通的 JavaScript 手写，也可以使用 Gulp 庞大的插件社区。

[![gulp logo](../Images/561d1bbe18d076d17b0017cb005aa2ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GHaA86lH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stefanimhoff.de/assets/images/artikel/gulp-tutorial-1-097f3c0262.png)

您可以将多个命令链接在一起，使用 Gulp 来编写构建过程的脚本。一些常见的事情可能是发送图片到 [Cloudinary](https://cloudinary.com/) 或者编译 JSON 文件来证明你用 [Algolia](https://www.algolia.com/) 的搜索。我真的相信这就是 Gulp 的闪光点，它有许多社区构建的插件来处理简单到复杂的脚本，比如在构建过程中处理 CVS 生成或图像操作。

```
 // example gulp build
    gulp.task("build", function(callback) {
      runSequence(["css", "js"]);
    });

    gulp.task("css", () => (
      gulp.src("./src/css/*.css")
        .pipe(postcss([
          cssImport({from: "./src/css/main.css"}),
        ]))
        .pipe(gulp.dest("./dist"))
    ));

    gulp.task("js", (cb) => {
      //  using webpack ;)
      const myConfig = Object.assign({}, webpackConfig); 
      webpack(myConfig, (err, stats) => {
        if (err) throw new gutil.PluginError("webpack", err);
        gutil.log("[webpack]", stats.toString({
          colors: true,
          progress: true
        }));
        cb();
      });
    }); 
```

Enter fullscreen mode Exit fullscreen mode

Gulp 在构建过程中很好地处理了同步脚本和异步(带插件)脚本，但是很可能你需要使用另一个构建工具来优化和转换。我倾向于使用 Gulp 进行快速脚本编写，但倾向于包含一个单独的捆绑器，如 Webpack 或 Browserify 来处理捆绑的重量。这是我的个人偏好，也是我试图在模块捆绑之前，让 Gulp 做好自己作为任务执行者擅长的事情。Gulp 脚本可能会遇到 Webpack 配置所遇到的同样的问题，即带有大量注释的冗长配置，这正是我所避免的。我建议你自己试试 Gulp，浏览一下 API 文档中关于如何使用它的例子。

## npm

优势:不需要额外的依赖关系
问题:脚本需要手动编写或收集
理想项目:小型项目和原型

我不能把 Gulp 脚本作为构建工具，也不能把 npm(节点包管理器)作为构建工具。npm 通常被认为是在 CDN 上托管 JavaScript 库的包管理器和中介。到目前为止，我提到的每个构建工具都利用 npm 通过命令行进行安装。npm 绝对不仅仅是包管理。npm 附带的一个特性是运行脚本的能力。

npm-scripts 特性可以做很多现代构建工具可以做的事情，同时减少对包的依赖和维护开销。如果我们仔细观察，我们讨论的每个工具都包含一组普通的 JavaScript 文件。这些相同的文件可以使用 npm 脚本和手写连接，而不需要依赖关系。

对于小型项目和原型，从 npm 开始就足够了，我鼓励你在构建工具评估之前先看看 npm。正如 npm 的共同创始人劳里·沃斯解释的那样，“npm 是来给你让路的。这并不意味着给你的构建过程增加一堆仪式。观看 Laurie 在以前的 JSConf EU 中的讲话。

npm 项目的[文档](https://docs.npmjs.com/)以及关于如何使用 [npm 作为构建工具的一般博客文章](https://www.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/)是学习更多的好资源。

## 包裹

优势:零配置需求
难题:更新的项目，需要参考的文档更少
理想的项目:希望快速开始的小型项目和原型

[package](https://parceljs.org/)于 2017 年底问世，并获得了将所有配置疲劳的 JavaScript 点包装在一个小小的包中的特权。Parcel 消除了构建工具的复杂性，并且使用 JavaScript 空间中最流行的插件，包括 babel transforms。

[![parcel logo](../Images/69ccc585bd1e51bc15f55e9dd8c6f706.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8kRmikKM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A5cX4trefu1K5C51uo1VShg.png)

类似于 Browserify 也没有配置文件，但是也没有特定于包的插件。Parcel 依靠现有的 JavaScript 生态系统项目如 Babel 来完成这项工作。包裹只是指挥者。你也可以在你的 package.json 或者。babelrc 和 Parcel 会知道将它包含在构建过程中。不需要额外的配置，这是一个非常有趣的特性。也没有必要再学习一个库来维护你的项目(也就是可怕的 JavaScript 疲劳)。

开始使用 Parcel 与其他类似，但是不是为包提供输入和输出，而是在脚本中提供条目。

```
 npm install parcel-bundler
    parcel build index.js 
```

Enter fullscreen mode Exit fullscreen mode

任何其他功能都可以在文档中找到，但是它们需要你编写现代的 JavaScript 来实现。这个项目真的没有什么神奇之处。看看这个使用 Parcel 运行 React 应用程序的[示例。如上所述，这个项目仍然是相当新的，但似乎有前途。已经有一些很棒的文档可用](https://blog.vigneshm.com/building-a-reactjs-project-with-parceljs-d88cdd178e50)

## 微束

优势:零配置，占用空间极小
难题:更新的项目，需要参考的文档更少
理想项目:注重规模的项目，希望作为插件或附加组件与其他项目共享

如果你没有听说过 Parcel，那么你很有可能没有听说过用于微型模块的零配置捆绑器 [Microbundle](https://github.com/developit/microbundle) 。Microbundle 由前面提到的 [Rollup](https://github.com/rollup/rollup) 项目提供支持，旨在通过取消配置步骤将他们的模块捆绑提升到一个新的水平。与 Parcel 类似，它只使用 package.json 来捆绑您的项目。因此，请确保包含运行您的 JavaScript 和捆绑您的资产所需的所有必需的依赖项。

```
 npm install microbundle
    microbundle 
```

Enter fullscreen mode Exit fullscreen mode

如果没有提供入口点作为选项，Microbundle 将假设您有一个 index.js。如果没有提供输出，它还会创建一个包并缩小同一个文件。不仅创建了捆绑版本，还在捆绑过程中提供了 UMD 版本。

```
 // index.js
    function () {
      console.log("Hello World")
    }

    // microbundled into index.m.js
    function O(){console.log("FOOOOOOOOOO")}O();

    // microbundled into index.umd.js
    !function(e,n){"object"==typeof exports&&"undefined"!=typeof module?n():"function"==typeof define&&define.amd?define(n):n()}(0,function(){console.log("FOOOOOOOOOO")}); 
```

Enter fullscreen mode Exit fullscreen mode

这对于将小的可共享项目嵌入到其他项目中是很有用的。由于这篇文章，我现在才发现这个微捆绑包，但可以看到它对 [Netlify 身份小部件](https://www.netlify.com/docs/identity/)有用，这是一个旨在嵌入到更大项目中的项目，已经被手动捆绑到 UMD 中。

## 现在去建造一些东西

无论你的编程风格如何，都有一个适合你的解决方案，选择一个构建工具取决于你想要什么样的控件。我的尝试是提供一个从无配置到批量配置的过程。尽管有大量的选择，所有的构建工具都可以与 Netlify 一起工作，并且可以成为维护应用程序的一个有用的部分，所以尝试几个，看看哪一个适合你。

如果你是这里没有列出的工具的忠实粉丝，请留下评论让我知道。
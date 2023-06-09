# 9 个基本的 webpack 面试问题和答案

> 原文：<https://dev.to/aershov24/9-basic-webpack-interview-questions-and-answers-4ilk>

[![9 Basic webpack Interview Questions And Answers in 2019](img/55c30820b8e0a602f483f3fc93e3c358.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lk9-o0TT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/190407/pexels-photo-190407.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D350) 
Webpack 是一个开源的 JavaScript 模块捆绑器。它获取依赖项并生成依赖图，从而允许 web 开发人员使用模块化方法来开发 web 应用程序。它还接受各种资产，如 JavaScript、CSS、字体、图像和 HTML，然后将这些资产转换成便于通过浏览器使用的格式。Webpack 的真正力量是其各个部分的总和。

> 最初发表于 [FullStack。永远不要再错过你的技术面试](https://www.fullstack.cafe)

### Q1:什么是 webpack？

> 主题: **Webpack**
> 难度:⭐

Webpack 是一个构建工具，它把你所有的资产，包括 Javascript、图像、字体和 CSS，放在一个依赖图中。Webpack 允许您在源代码中使用`require()`来指向本地文件，比如图像，并决定如何在最终的 Javascript 包中处理它们，比如用指向 CDN 的 URL 替换路径。

🔗**来源:** [blog.andrewray.me](https://blog.andrewray.me/webpack-when-to-use-and-why/)

### Q2:说出使用 webpack 的一些好处

> 主题: **Webpack**
> 难度:⭐⭐⭐

依赖图中的 Webpack 和静态资产提供了许多好处。以下是一些例子:

*   **不良资产消除。**这是杀手锏，尤其是对于 CSS 规则。您只需将图像和 CSS 构建到您的应用程序实际需要的`dist/`文件夹中。
*   **更容易的代码拆分。**例如，因为您知道您的文件`Homepage.js`只需要特定的 CSS 文件，所以 Webpack 可以很容易地构建一个`homepage.css`文件来大大减小初始文件的大小。
*   **您控制如何处理资产。**如果一个图像小于某个尺寸，你可以 [base64 编码它](https://developer.mozilla.org/en-US/docs/Web/API/WindowBase64/Base64_encoding_and_decoding)直接到你的 Javascript 中以减少 HTTP 请求。如果 JSON 文件太大，可以从 URL 加载。你可以通过 Less 自动解析成普通的 CSS。
*   **稳定生产部署。**您不会意外地部署缺少图像或样式过时的代码。
*   Webpack 一开始会让你慢下来，但是正确使用的话会给你带来巨大的速度优势。你得到热页面重载。真正的 CSS 管理。CDN 缓存破坏，因为 Webpack 会自动将文件名更改为文件内容的散列，等等。

Webpack 是 React 社区采用的主要构建工具。

🔗**来源:** [blog.andrewray.me](https://blog.andrewray.me/webpack-when-to-use-and-why/)

### Q3:说出一些你认为非常重要和有帮助的插件

> 主题: **Webpack**
> 难度:⭐⭐⭐

*   **CommonsChunkPlugin**——创建一个单独的文件(称为块)，由多个入口点共享的公共模块组成。
*   **DefinePlugin** -允许你创建可以在编译时配置的全局常量。
*   HtmlWebpackPlugin -简化 HTML 文件的创建，为您的 webpack 包提供服务。
*   从一个或多个包中提取文本到一个单独的文件中。
*   **compression webpackplugin**——准备资产的压缩版本，以内容编码的方式提供给它们。

🔗**来源:**【webpack.js.org】T2

### Q4: Webpack 给我们一个依赖图。那是什么意思？

> 主题: **Webpack**
> 难度:⭐⭐⭐

任何时候一个文件依赖于另一个文件，webpack 都将其视为*依赖*。这允许 webpack 获取非代码资产，如图像或 web 字体，并将其作为应用程序的依赖项提供。

Webpack 允许您在本地“静态资产”上使用 require():

```
<img src={ require('../../assets/logo.png') } /> 
```

Enter fullscreen mode Exit fullscreen mode

当 webpack 处理您的应用程序时，它从命令行或其配置文件中定义的模块列表开始。从这些入口点开始，webpack 递归地构建一个包含应用程序需要的每个模块的依赖图，然后将所有这些模块打包成少量的包(通常只有一个包)供浏览器加载。

`require('logo.png')`源代码从来不会在浏览器中执行(也不会在 Node.js 中执行)。Webpack 构建一个新的 Javascript 文件，用有效的 Javascript 代码(比如 URL)替换`require()`调用。捆绑文件是由节点或浏览器执行的。

🔗**来源:** [blog.andrewray.me](https://blog.andrewray.me/webpack-when-to-use-and-why/)

### Q5:列举 Webpack 的一些陷阱

> 主题:**网页包**
> 难点:□

Webpack 并不完美，存在一些缺陷。

*   文档很糟糕。这种语言经常令人困惑，[比如](http://webpack.github.io/docs/what-is-webpack.html)“web pack 获取具有依赖关系的模块，并生成表示这些模块的静态资产。”什么？甚至页面布局也有问题，随机的侧边栏条目让你无法点击，当你试图阅读时还有动画标志。
*   **源代码**是[同样痛苦的](https://github.com/webpack/webpack/issues/824)。
*   **配置 Webpack** 对于新人来说是个雷区。配置文件语法令人困惑。看看来自[样板项目](https://github.com/gaearon/react-hot-boilerplate)的[既定范例](https://github.com/erikras/react-redux-universal-hot-example/blob/master/webpack/dev.config.js)会有所帮助。
*   Webpack 主要由[一个人](https://github.com/sokra)维护。社区的迅速采用和[成为焦点](https://www.youtube.com/watch?v=VkTCL6Nqm6Y)意味着生态系统远远落后于 React 的成熟。这有副作用，比如文档质量差。
*   Webpack 在一个字符串中引入了一个讨厌的**迷你语言:** `require("!style!css!less!bootstrap/less/bootstrap.less");`这个语法几乎没用过，[勉强解释了一下](http://webpack.github.io/docs/using-loaders.html#loaders-in-require)，但是文档里到处都是。这种字符串语言在我看来是 Webpack 最大的设计缺陷之一。

🔗**来源:** [blog.andrewray.me](https://blog.andrewray.me/webpack-when-to-use-and-why/)

### Q6:解释一下 NPM vs .鲍尔 vs . browser ify vs . Gulp vs . Grunt vs . web pack 的区别？

> 主题:**网页包**
> 难点:□

*   `npm` & `bower`都是包管理者。他们只是下载依赖项，不知道如何自己构建项目。他们所知道的就是在获取所有依赖之后调用`webpack` / `gulp` / `grunt`。
*   `bower`类似于`npm`，但是构建扁平的依赖关系树(不像`npm`递归地这么做)。意思是`npm`获取每个依赖项的依赖项(可能会多次获取相同的依赖项)，而`bower`希望您手动包含子依赖项。有时`bower`和`npm`一起分别用于前端和后端(因为每兆字节在前端可能很重要)。
*   `grunt`和`gulp`是任务执行人，负责自动化一切可以自动化的事情(例如，编译 CSS/Sass、优化图像、制作包并缩小/传输它)。
*   `grunt`对`gulp`(就像`maven`对`gradle`或者配置对代码)。Grunt 基于配置单独的独立任务，每个任务打开/处理/关闭文件。Gulp 需要的代码量更少，并且基于节点流，这允许它构建管道链(不重新打开同一个文件)并且速度更快。
*   Webpack 是一个构建工具，它把你所有的资产，包括 Javascript、图像、字体和 CSS，放在一个依赖图中。
*   `npm` / `bower` +外挂可能取代任务跑者。他们的能力经常交叉，所以如果你需要使用`gulp` / `grunt`而不是`npm` +插件，会有不同的含义。但是任务运行器肯定更适合复杂的任务(例如，“在每个构建上创建 bundle，从 ES6 到 ES5 转换文件，在所有浏览器模拟器上运行它，制作截图并通过 ftp 部署到 dropbox”)。
*   `browserify`允许为浏览器打包节点模块。`browserify` vs `node`的`require`其实就是 [AMD vs CommonJS](https://addyosmani.com/writing-modular-js/) 。

🔗**来源:**【stackoverflow.com】T2

### Q7:web pack 加载器和插件有什么区别？

> 主题:**网页包**
> 难点:□

*   **加载器**在生成包期间或之前在单个文件级别工作。
*   **插件**在包或块级别工作，通常在包生成过程的最后工作。插件还可以修改包本身的创建方式。插件比加载器有更强大的控制能力。

[![](img/a87db62f60d4a231afc74913de733b2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GYOvzWPZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/P7hTM.png)

🔗**来源:**【stackoverflow.com】T2

### Q8:解释这段代码

> 主题:**网页包**
> 难点:

```
new webpack.optimize.CommonsChunkPlugin({
  name: 'common',
  filename: 'common.js',
  chunks: ['home', 'dashboard']
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**CommonsChunkPlugin** 是一个内置特性，它创建一个单独的文件(称为块)，由多个入口点之间共享的公共模块组成。通过将通用模块从包中分离出来，得到的分块文件可以在最初加载一次，并存储在缓存中供以后使用。这导致了页面速度的优化，因为浏览器可以快速地从缓存中提供共享代码，而不是在访问新页面时被迫加载更大的包。

这段代码创建了一个单独的文件:`common.js`，它包含了 home 和 dashboard 块中的公共模块。

🔗**来源:**【webpack.js.org】T2

### Q9:描述 webpack 中的树抖动机制

> 主题:**网页包**
> 难点:□

**树摇动**是 JavaScript 上下文中常用的术语，用于消除死代码。它依赖于 ES2015 模块语法的静态结构，即导入和导出。

您可以将您的应用程序想象成一棵树。您实际使用的源代码和库代表了绿色的活树叶。死亡代码代表秋天消耗掉的褐色枯叶。为了除掉枯叶，你必须摇动树，使它们掉落。

如果您的代码确实有一些副作用(导入时执行特殊行为的代码)，那么使用`sideEffects`属性:

```
{
  "name": "your-project",
  "sideEffects": [
    "./src/some-side-effectful-file.js"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【webpack.js.org】T2

> 谢谢🙌阅读，祝你面试好运！
> *查看更多 FullStack 面试问题&答案上👉 [www.fullstack.cafe](https://www.fullstack.cafe)*
# 前端开发栈:引入复杂性以降低复杂性

> 原文：<https://dev.to/rapasoft/frontend-development-stack-introducing-complexity-to-reduce-complexity-41dl>

作为一名 Java 开发人员，我不太喜欢 JavaScript 这种语言。从本质上来说，它是非常原始和简单的。虽然这对于较简单的脚本编写(JavaScript 的初衷)来说没什么问题，但对于开发大型应用程序来说却是不可用的。它有很多不一致的地方，棘手的部分，总的来说，它有时会让你揪头发。

但是，正如在每一种语言中都有工具、库和框架可以减轻你的痛苦。我真的很惊讶围绕 JavaScript 的生态系统有多成熟，而且你不需要理解它的大部分。几乎所有东西都有一个把手，如果我们把它发挥到极致，你可能根本不知道你正在开发一个 JavaScript 应用程序。您甚至不用编写一行 JavaScript 就可以构建一个成熟的单页面应用程序——例如，使用 TypeScript。

“怎么样？”你可能会问，答案在于那些“把手”带来的隐藏的复杂性。有许多任务是在后台完成的，从编写源代码到创建可以部署的“产品包”。

在本文中，您将找到这些问题的答案:

*   我只想建网站！除了 HTML、CSS 和普通 JavaScript 之外，我是否应该研究一些更复杂的技术？
*   这么多工具和库，我该挑哪一个？
*   把一切都安排好就这么难吗？

...希望还有其他人。

## 项目

让我们用一些具体的、运行中的例子来工作，这样概念就更清楚了。我们将开发一个博客平台的前端应用程序。简单的用户界面将显示来自不同作者和类别的文章。

[![Blog!](img/707e9fac6abf127227c6ac9a5fa3cc69.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5YyiBamt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wz7rcqry5iuv87f92ksi.PNG)

## 天真的做法

你们都记得过去的美好时光，那时你们写 HTML，一些 CSS，然后为了给它添加一些趣味，你们添加了一些 JavaScript 效果？嗯，这就是我们要开始的地方。但从那时起，世界发生了一些变化，现在一切都用 JavaScript 编写。这主要是因为您可以定义可重用的代码部分，然后重用它们。例如，这将是一个标题(`title.js` ):

```
const title = `<h1>👨‍👨‍👦‍👦 Friends blog 🌟</h1>`; 
```

Enter fullscreen mode Exit fullscreen mode

这是我们将如何使用它(`main.js` ):

```
const main = `
    <div class="main container">
        <div class="title">${title}</div>
    </div>
`; 
```

Enter fullscreen mode Exit fullscreen mode

现在为了使用它，我们需要把它放在某个主文件中，例如`index.js` :

```
document.getElementById("app").innerHTML = main; 
```

Enter fullscreen mode Exit fullscreen mode

并在`index.html` :
中使用

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    Blogging platform!

    <script type="text/javascript" src="components/title.js"></script>
    <script type="text/javascript" src="components/main.js"></script>
</head>
<body>
<div id="app">
</div>
<script type="text/javascript" src="index.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们有三个`.js`文件，需要按照特定的顺序导入，因为常量是在全局范围内定义的。当我们在其他`.js`文件中定义额外的组件时，这是一个问题。首先，您需要手动将它们添加到`index.html`中，然后您需要维护具体的顺序。如果用相同的名字定义变量会怎样？它将覆盖前一个。此外，我们太快地加入了进来，并且使用了 ES6(JavaScript 的“版本”)[的特性，比如旧浏览器不支持的字符串模板](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)。所以，不支持 IE9:(。

我们除了头衔什么都没有。在正常情况下，你想要添加更多的组件、样式、一些布局框架(例如引导程序)、添加特定的逻辑、助手类和...把它们捆在一起。

## 将一切捆绑在一起

为什么需要这样做？如果您有大量的`.js`文件，您将需要为它们中的每一个发送一个 HTTP 请求(就像我们的例子中的`title.js`、`main.js`和`index.js`)，这本质上总是比较慢，就好像您将它放入一个文件中(`bundle.js`)。围绕一次 HTTP 请求/响应往返的编排成本太高。

我们可以通过将任务委托给一个工具来实现任务*的自动化！不久前，有两个主要竞争对手，那就是[咕噜](https://gruntjs.com/)和[大口](https://gulpjs.com/)。您可以定义一个特定的任务列表，这些任务应按照特定的顺序执行，以便构建一个项目，例如:*

*   清除以前运行的生成目录
*   遍历源文件列表`.js`
    *   并将它们复制到一个`bundle.js`
    *   并压缩它的内容
*   遍历源文件列表`.css`
    *   并将它们复制到一个`bundle.css`
    *   并压缩它的内容
*   生成并复制`index.html`，它将包含到已构建的`bundle.js`和`bundle.css`文件的链接

Gulp 和 Grunt 在功能上有些相似，但是它们使用不同的方法。虽然 Grunt 更具声明性，但 Gulp 定义的是管道连接在一起的过程。以这个配置(名为`gulpfile.js`)为例:

```
... (gulp plugin definition) ...

gulp.task('buildJs', function () {
    gulp.src([
        './src/components/title.js',
        './src/components/main.js',
        './src/index.js'
    ])
        .pipe(concat('build.js'))
        .pipe(uglify())
        .pipe(gulp.dest('target'))
});

gulp.task('buildCss', function () {
    gulp.src('./src/styles/*.css') // path to your file
        .pipe(minifyCss())
        .pipe(concat('build.css'))
        .pipe(gulp.dest('target'));
});

gulp.task('cleanTarget', function () {
    return gulp.src('target')
        .pipe(clean({force: true}))
});

gulp.task('copyIndexHtml', function () {
    return gulp.src('src/index.html')
        .pipe(gulp.dest('target'));
});

gulp.task('default', gulpSequence('cleanTarget', 'buildJs', 'buildCss', 'copyIndexHtml')); 
```

Enter fullscreen mode Exit fullscreen mode

它做什么非常清楚，在命令行中运行`gulp`将运行`default`任务，该任务将所有东西结合在一起。

Gulp 是如何工作的？这是一个 [node.js](https://nodejs.org/en/) 应用程序，所以为了正确使用它，你必须安装 node.js。您还需要将您的项目转换为 node.js 项目(通过`package.json`配置)并安装 gulp 将使用的所有插件(通过`npm` - [节点包管理器](https://www.npmjs.com/))。因此，我们需要安装不是一个，而是两个工具来自动化任务。

当然，gulp 和 grunt 都有许多插件，可以帮助你实现几乎所有需要的功能。但是它们大多是第三方插件，在设置方面有不同的质量和难度。

在我们的设置中，我们还没有解决`.js`文件的顺序问题(这仍然需要明确),也没有提到另一个问题，即依赖性管理。

## 某种依赖管理

有 Bootstrap、jQuery、moment.js、lodash 和更多你想在项目中使用的酷库和框架。在理想世界中，你应该做的是下载或指向一个`bootstrap.css`并链接到`index.html`中，就像这样:

```
<head>
    ...
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" />
    ...
</head> 
```

Enter fullscreen mode Exit fullscreen mode

你完了！但是现在前端世界没有什么是理想的，一旦你开始添加文件，问题就会出现。您需要解决以下问题:

*   库 A 与库 B 的特定版本兼容吗？
*   为什么将 jQuery 添加到项目中会禁用我所有喜欢的弹出菜单？
*   为什么我们有 4 个库来创建漂亮的弹出菜单？
*   我已经链接了一个在这个链接上不再可用的外部库:(

你很快就会意识到，你至少需要某种`dependency management`。一些将找到你需要的库的正确版本，并链接到项目。不久前，这些问题通过使用[鲍尔](https://bower.io/)得以解决。Bower 是一个包管理器，它将使用它的配置文件(`bower.json`)来定义、下载和安装供您使用的依赖项。

```
{
  "name": "blogging-platform-frontend",
  "description": "The blog!",
  "main": "index.js",
  "authors": ["Pavol Rajzak"],
  "license": "ISC",
  "keywords": ["javascript", "gulp"],
  "homepage": "",
  "ignore": [
    "**/.*",
    "node_modules",
    "bower_components"
  ],
  "dependencies": {
    "bootstrap": "^4.0.0"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

至于 Gulp/Grunt，你需要通过`npm`来安装，然后你可以运行`bower`命令来创建一个包含特定库的`bower_components`目录。然后，您可以使用 gulp/grunt 插件将依赖项连接到项目中。太棒了。似乎是这样，但并不是一切都是理想的。鲍尔现在是一个事实上已经停止的项目，因为它包含了几个设计问题。[你不应该把它用于新的开发](https://gofore.com/en/stop-using-bower-and-how-to-do-it/)。

另一种观点可能是这样的:为了构建项目和维护依赖关系，我们需要这些工具:

*   `.js`脚本的 node.js - runner
*   npm -包管理器
*   咕噜声/吞咽声-任务跑步者
*   鲍尔-包装经理

嗯，我试图让它变得明显，有一些重复，我们可以避免。为什么我们需要 2 个任务运行器和 2 个独立的依赖管理工具？答案是——我们没有。

## 介绍 webpack

所以，我们去掉了 gulp/grunt 和 bower，取而代之的是前端部分的纯 npm/node.js 方案。我们能用它做什么？嗯，不多。主要的任务——将它们捆绑在一起——仍然需要被一些更高级的专用工具所取代。**一个应用捆绑器**。

这里是我们将[网络包](https://webpack.js.org/)引入我们故事的地方。它是一个捆绑器，能够在你的应用中爬行，找到源代码，并根据定义的依赖关系创建应用捆绑包。这里最主要也是最大的不同是，它利用了*模块化方法*，这可以通过多种方式实现。ES6 方式是使用`import` / `export`语句。

让我们来看看我们从简单的方法中改变的例子:

```
... (title.js) ...
export const title = `<h1>👨‍👨‍👦‍👦 Friends blog 🌟</h1>`; 
```

Enter fullscreen mode Exit fullscreen mode

```
... (main.js) ...
import {title} from "./title";

export const main = `
    <div class="main container">
        <div class="title">${title}</div>
    </div>
`; 
```

Enter fullscreen mode Exit fullscreen mode

```
... (index.js) ...
import {main} from "./src/components/main";

document.getElementById("app").innerHTML = main; 
```

Enter fullscreen mode Exit fullscreen mode

现在，这是一个巨大的改进，它解决了在全局名称空间中定义所有组件的问题。既然您可以显式地定义您的依赖项，您就不必担心任何名称空间冲突，因为 webpack 会以安全的方式将它们打包。

Webpack 可以这样配置(`webpack.config.js`):

```
module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /(node_modules)/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['babel-preset-env', 'react'],
                        plugins: [
                            require('babel-plugin-transform-object-rest-spread')
                        ]
                    }
                }
            },
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
        ]
    },
    plugins: [
        new WebpackCleanupPlugin('target', {verbose: true}),
        new CopyWebpackPlugin([{from: 'index.html', to: 'index.html'}])
    ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

这与 gulp/grunt 做的事情非常相似，但是代替任务/插件的是用于处理文件的**加载器**和用于额外任务的*插件*。

最重要的是，您使用`npm` / node.js 来定义和触发构建，所以我们现在只有三个非常具体的工具来完成这项工作。这里有一个`package.json`配置的例子:

```
{
  "name": "blogging-platform-frontend",
  "version": "1.0.0",
  "description": "The simplest UI you can imagine",
  "main": "index.js",
  "scripts": {
    "start": "webpack --watch index.js target/build.js",
    "build": "webpack index.js target/build.js",
  },
  "keywords": [
    "javascript"
  ],
  "author": "Pavol Rajzak",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-jest": "^22.2.0",
    "babel-loader": "^7.1.2",
    "babel-plugin-add-react-displayname": "0.0.5",
    "babel-plugin-transform-object-rest-spread": "^6.26.0",
    "babel-preset-env": "^1.6.1",
    "babel-preset-react": "^6.24.1",
    "copy-webpack-plugin": "^4.5.1",
    "css-loader": "^0.28.9",
    "style-loader": "^0.20.1",
    "url-loader": "^0.6.2",
    "webpack": "^3.10.0",
    "webpack-cleanup-plugin": "^0.5.1"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经发现了配置中的一个新增内容——我们正在使用`babel`。它是一个 transpiler(语言到语言编译器),将我们的 ES6 代码编译成更受支持的 ES5 版本的 JavaScript。我们现在也可以在 IE9 上支持我们的应用程序了，哇哦！`<IronyOff />`

此外，webpack 本身能够在`watch`模式下工作，这将自动获取更改并重新构建包。它还支持多种配置(开发/生产)，它有许多插件，它得到更好的每一个版本！与吞咽/咕哝相反，配置过程肯定更具确定性。

## 我一窍不通！

这些工具的寿命很短，我说的不是几十年的进化，最多几年。如何追踪正在发生的事情？如果你没有直接参与前端开发，这是非常困难的。如果现在使用`webpack`是很酷的事情，在几个月内它可以是[预装](https://github.com/facebook/prepack)。或者说`npm`现在已经被弃用，取而代之的是[纱线](https://yarnpkg.com/lang/en/)。

幸运的是，即使这样也有工具/库/框架！如果你想构建 React 应用，你可以从 [create-react-app](https://github.com/facebook/create-react-app) 开始，对于 Angular 你有 [angular-cli](https://cli.angular.io/) 。您可能需要的一切都已经为您预先配置好了。您仍然需要理解构建和依赖管理的概念，但至少这样会减轻负担。

## 底线

我个人认为，作为一名前端开发人员意味着你需要更加频繁地跟踪 JS 生态系统的变化。在后端，你基本上可以在不研究新趋势的情况下(这是你不应该做的)，在前端几乎是不可能的。但是，如果您不知道什么是“标准”前端堆栈，您可以直接使用样板文件或生成器来针对特定的框架。
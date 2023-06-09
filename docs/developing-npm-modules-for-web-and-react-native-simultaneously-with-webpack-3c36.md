# 使用 Webpack 同时为 Web 和 React Native 开发 NPM 模块

> 原文：<https://dev.to/kylessg/developing-npm-modules-for-web-and-react-native-simultaneously-with-webpack-3c36>

在我作为开发人员的日常工作中，我参与了几个项目，包括一个 web 应用程序和一个 React 本地移动应用程序。

我最近在做的项目是 [https://bullet-train.io](https://bullet-train.io) ，我最近写了几篇关于它的帖子。特别是，这个项目要求包含 JS 和 React 本地客户端库，以便前端应用程序使用该服务。

这篇文章讲述了我是如何构建我的库的，我可以同时对两个模块进行常规更新，同时更新它们各自的示例应用程序，以包含最新的包。它还提供了一个真实示例的链接供您查看。

[![](img/5c4217dabef9be2967523f167f97d29a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jjHZA8Vp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/webpack/media/master/logo/logo-on-white-bg.png)

## 什么是 Webpack？

这篇文章假设你对什么是 Webpack 以及它在你的项目中扮演的角色有一定的了解。在高级别，Webpack 获取条目文件(例如 index.js、screen.scss 和其他资产)，分析它们的依赖性并将它们捆绑在一起，在需要时传输输入(例如使用 babel 将 es6 转换为 vanilla JS，使用 node-sass 将 scss 转换为 css)以创建单个输出文件。

在我们的例子中，我们使用它来创建两个 javascript 库，并发布到 NPM，我们还创建了每个库的副本，并将其部署到示例应用程序中，供人们尝试。

## 项目

在我的例子中，客户端 SDK 充当了子弹头列车 REST API 的用户友好代理，它帮助检索基于环境关键字的特性标志/远程配置列表。它还在幕后做了一些事情，比如用 AsyncStorage 缓存结果，并添加了一些功能来告诉我某个特性是否被启用以及它们配置了什么值。

[![](img/512f36e256880cb027ee181141daf1e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i7hKM26V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/SolidStateGroup/bullet-train-frontend/master/hero.png)

## 第一步:识别共享代码

通常在 React Native 中开发时，您很可能只需要一个 JavaScript 模块就可以完成您想要的。然而，在一些用例中，不同的实现必须以稍微不同的方式工作，或者可能包括本机桥来访问核心设备功能。

在我们的例子中，模块非常相似，但是需要使用 fetch 和 Async 存储的独立实现来运行。为了最大化重用，代码被分成两个条目文件，为`bullet-train-core.js`提供平台特定的聚合填充。

## 第二步:创建一个合理的项目结构

一个很好的起点是制定一个合适的项目结构。该项目分为 3 个部分:

**T2`/`**

顶层是我们的 Webpack 配置和我们的库 js，这些文件并不直接包含在我们的任何 NPM 模块中，而是用于生成各自的包。因为目标是管理两个独立的 NPM 模块，每个模块都有自己的索引条目文件。

**T2`bullet-train-client`**

这个文件夹包含我们捆绑的 web 模块和一个简单的示例 web 应用程序。

**T2`react-native-bullet-train`**

该文件夹包含我们捆绑的 React 本机模块和一个简单的 React 本机应用程序示例。

[![](img/25e3a874d207ee6930af9d20dbc74137.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TxMXml1k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/cGPy5J/g_El_OUv_Z4_QDy8vs_OXb7cr1_A.png)

## 第三步:创建开发流程

为了使开发变得容易，编辑任何顶级文件都会引发一些事情:

*   1.从 index.js 触发一个缩小的包，并将缩小的输出捆绑到/bullet-train-client/lib/index . js。
*   2.从 index.react-native.js 触发一个缩小的包，并将一个缩小的输出包到/react-native-bullet-train/lib/index . js。

除此之外，我们还想在每个示例文件夹中部署一个非缩小版本，这样我们就可以在测试时对其进行调试。

要实现这一点，运行`node_modules/.bin/webpack --watch`是第一步，它监听任何文件更改并运行我们的 Webpack 构建。将上面的片段添加到我们的顶级`package.json`中，这样就可以通过运行`npm run dev`来完成，真正的例子可以在[这里找到](https://github.com/SolidStateGroup/bullet-train-js-client/blob/master/package.json#L9)。

## 步骤 4:编写 Webpack 配置

此时，我让 Webpack 监听更改，我们只需要编写 Webpack 配置文件。

我们的构建系统将与标准网站略有不同，在标准网站中，我们通常有一个入口文件/输出，实际上我们有 4 个。

 `const defaultConfig = { //our base config
mode: "production",
devtool: 'source-map',
module: {
rules: [
{
test: /.(js)$/,
exclude: /node_modules/,
use: ['babel-loader']
}
]
}
};`

...

module . exports =[//export each bundle
web bundle，webExampleBundle，reactNativeBundle，reactNativeExampleBundle
；

这是我们将为 4 个包中的每一个使用的基本配置，它将使用 babel 传输任何 js 文件。我们将模式设置为 production，这样输出就会缩小，将 devtool 设置为 source-map，这样我们就可以在调试时看到代码的可读版本。

**网束**

 `const webBundle = Object.assign({}, defaultConfig, { //Bundle 1: compile the web client
output: {
filename: "index.js",
library: "bullet-train",
libraryTarget: "umd",
path: path.join(__dirname, '/bullet-train-client/lib'),
},
entry: {
main: './index.js'
}
});` 

基于我们的基本配置，web bundle 为`/bullet-train-client/lib/index.js`创建了一个缩小的 bundle。将 libraryTarget 设置为 umd 非常重要，因为它告诉 webpack 将输出设置为 JavaScript 模块，这样我们就可以在应用程序中使用 require('bullet-train-client ')。webExampleBundle 与这个配置完全相同，只是它向`/bullet-train-client/example/src`输出一个文件。

**React 原生包**

 `const reactNativeBundle = Object.assign({}, defaultConfig, { //Bundle 3: compile the react native client
entry: {
main: './index.react-native.js'
},
externals: {
'react-native': 'react-native'
},
output: {
filename: "bullet-train.js",
library: "bullet-train",
libraryTarget: "umd",
path: path.join(__dirname, '/react-native-bullet-train/example'),
}
});` 

与 web 模块不同，React 本机库需要假设 React 本机是作为对等依赖项安装的。这就是使用**外部**的地方，外部是一种从包中排除依赖的方式，并假设它已经存在。如果不这样做，在评估`require('react-native')`时，Webpack 将无法编译。

每当您的模块耦合到外部模块(例如，开源一个 react web 组件)时，您都需要使用这样的配置。

## 第五步:部署

下一步是编写一个简单的方法来部署客户端库和示例。这就像编写以下 npm 脚本一样简单:

`"deploy": "npm run build && cd ./bullet-train-client/ && npm publish && cd ../react-native-bullet-train && npm publish"`

我的过程是在每个`package.json`中增加 NPM 版本，然后运行`npm run deploy`将更新的模块/示例项目发布到 NPM。

[![](img/53edbee9568d2061fdb6d25b00621ddc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7iz83LYa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/eeercy/KAAq1_BA5_R9_CKNv_Nhm_O90n_A.png)

如果你更喜欢通过查看代码来学习，那么所有这些都是在 GitHub 上开源的[。欢迎在这里提出你的任何问题！](https://github.com/SolidStateGroup/bullet-train-js-client)
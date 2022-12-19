# Webpack 4 教程

> 原文：<https://dev.to/techiediaries/webpack-4-tutorial-5a59>

Webpack 4(代号 **Legato** )是 Webpack 的最新版本，它为最流行的模块捆绑器带来了许多新功能，如更好的性能和易用性。

在 biref 中，这些是 Webpack 4 最重要的特性:

*   性能提升和构建速度加快(速度提高 98%)
*   小型应用的零配置
*   更好的纯模块树抖动，没有副作用
*   开发和生产模式介绍
*   `<script async>`支持
*   弃用`CommonsChunkPlugin`而支持`optimize.splitChunks` API
*   默认情况下，您可以导入和导出 web 程序集(Rust、C++、C 等等。)
*   引入*模式*属性，可以选择开发或生产，默认为*生产*

如果您以前使用过 Webpack，那么您可能会同意配置是 Webpack 最令人沮丧的方面之一。

在本教程中，您将了解 Webpack 4，并了解如何使用它来捆绑现代前端应用程序的不同部分，而不必处理复杂的配置，尤其是当您只想启动并运行小型 web 应用程序时，对于小型项目和演示。

Webpack 4 提供了许多默认设置，可以看作是配置的一种约定。

## Webpack 4 默认不需要配置文件

使用 Webpack 时，您必须创建的第一件事是包含许多配置选项的配置文件`webpack.config.js`。最重要的是，[入口点](https://webpack.js.org/concepts/entry-points/)和[输出文件](https://webpack.js.org/concepts/output/)，它们定义了要打包的文件和要生成的包。

```
const config = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: '/dist'
  }  
};

module.exports = config; 
```

Enter fullscreen mode Exit fullscreen mode

有了 Webpack，你不需要添加这个配置，你只需要添加一个带有`index.js`文件的`src`文件夹，然后在打包你的文件后，你应该期望你的包被保存在`dist/main.js`中。

`src/index.js`和`dist/main.js`是 Webpack 4 使用的默认入口和输出点。

现在让我们创建一个小的 JavaScript 应用程序来演示这些新概念

所以让我们首先生成一个新的 npm 模块。

前往您的终端，然后创建一个新文件夹，并在其中导航

```
mkdir webpack4-demo
cd webpack4-demo 
```

Enter fullscreen mode Exit fullscreen mode

现在您需要使用
添加一个`package.json`文件

```
npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要使用 npm
安装 Webpack 4 和 Webpack CLI

```
npm i webpack webpack-cli --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

打开`package.json`并添加一个构建脚本来构建你的应用

```
"scripts": {
      "build": "webpack"
    } 
```

Enter fullscreen mode Exit fullscreen mode

接下来用`index.js`文件
添加一个 *src* 文件夹

```
mkdir src
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

打开`index.js`然后添加任何 JavaScript 代码

```
console.log("hello Webpack 4!"): 
```

Enter fullscreen mode Exit fullscreen mode

最后，使用
运行您的构建脚本

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

您应该将您的捆绑在`dist/main.js`中，这样您就可以看到 Webpack 4 正在使用缺省值，而不需要配置文件。

## 结论

使用 Webpack 4，您可以快速启动并运行您的小型测试应用程序，而没有配置文件的麻烦。
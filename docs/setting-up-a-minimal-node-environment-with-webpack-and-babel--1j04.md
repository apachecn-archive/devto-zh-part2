# 用 Webpack 和 Babel 建立一个最小节点环境

> 原文：<https://dev.to/aurelkurtula/setting-up-a-minimal-node-environment-with-webpack-and-babel--1j04>

今天我将解释如何在 node 中使用 babel 快速启用 ES6，以及如何在使用 react 时使用 webpack。

因为这是针对 node 的，我们显然需要安装 node 和 npm(或 yarn)——这两者的安装超出了本教程的范围。

接下来要全局安装`nodemon`和`babel-node`。

```
npm install -g nodemon babel-node 
```

Enter fullscreen mode Exit fullscreen mode

这意味着这两个软件包安装在您的计算机上，并将为任何未来的项目和在您的本地计算机上的任何独立设置工作。

## 入门

对于每个节点项目，最好的开始方式是创建一个目录，并从终端运行`npm init -y`进入该目录(`-y`会自动对您需要回答或手动跳过的所有问题回答“是”)。这将创建`package.json`文件，该文件跟踪所需的包。

现在创建另一个文件，您可以通过终端`touch .babelrc`来完成。这是巴别塔配置文件。这就是我们要让巴贝尔知道我们需要它注意什么的地方。在其中添加以下代码:

```
{"presets": ['env']} 
```

Enter fullscreen mode Exit fullscreen mode

在写这个教程的时候，我已经使用了`es2015-node5`(我不记得为什么它比`es2015`更好了)，但是根据文档，我们只需要使用`env`预置。

根据[文件](http://babeljs.io/env):

> `babel-preset-env`是一个新的预设，一年前首次发布，取代了许多以前使用的预设，包括:巴别预设-es2015，巴别预设-es2016，巴别预设-es2017，巴别预设-最新[和]其他社区插件涉及 es20xx: **巴别预设-节点 5** ，巴别预设-es 2015-节点等

配置好`.babelrc`后，我们只需要安装`babel-preset-env`

```
npm install babel-preset-env --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

## 测试我们目前所拥有的东西

对于我们到目前为止的设置，让我们创建一个`server.js`文件(它可以被称为任何你喜欢的东西)并为一个快速应用
编写样板文件

```
import express from 'express'; 
const app = express();
app.get('/', (req, res) => {
    res.send('Hello World')
})
app.listen(4000, () => {
  console.log('Listening');
}); 
```

Enter fullscreen mode Exit fullscreen mode

那只是为了测试 ES6 代码是否有效。准备就绪后，让我们使用两个全局安装的模块来编译和运行上面的文件:

```
nodemon --exec babel-node server.js 
```

Enter fullscreen mode Exit fullscreen mode

运行`nodemon`就像运行`node`,但是在第一次运行中，当我们对`server.js`进行修改时，脚本会重新运行，而`babel-node`会根据我们在`.babelrc`中指定的设置编译`server.js`中的代码

## 使用 webpack 来配置 react

在上面的设置之上，我们能够添加对 react 的支持，但是这次我们需要使用 webpack(和 express)。

让我们想象一下我们的样板文件将以
结束的文件结构

```
root/
    .babelrc
    package.json
    server.js
    webpack.config.js
    client/
        style/
            style.css
        index.html 
        index.js 
```

Enter fullscreen mode Exit fullscreen mode

我们已经创建了前三个文件。`client`文件夹将包含 react 项目文件。一个非常基本的设置如下:

在`client/index.js`中，让我们写一个 react 应用的基础:

```
import React from 'react';
import ReactDOM from 'react-dom';
import './style/style.css';
const App = () => {
  return <div>Hello World</div> }
ReactDOM.render(
  <App />,
  document.querySelector('#root')
); 
```

Enter fullscreen mode Exit fullscreen mode

(记住你需要安装`react`和`react-dom`包)

在`client/index.html`中，我们有最基本的 html 代码:

```
<!DOCTYPE html>
<html lang="en">
<head></head>
<body>
    <div id="root" />
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

(很明显，你想要更多的内容，`viewport`设置等等)

请注意，即使`index.js`应该连接到`index.html`,此时我们也没有连接它们。我们会用 webpack 做到这一点。

首先让我们告诉 babel 也要注意 react 语法——我们在`.babelrc`中这样做:

```
{"presets": ['env', 'react']} 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们需要安装预置:`npm i --save-dev babel-preset-react`

## 配置网络包

让我们创建`webpack.config.js`并编写基本结构。

```
import webpack from 'webpack';
import HtmlWebpackPlugin from 'html-webpack-plugin';
import LiveReloadPlugin from 'webpack-livereload-plugin'

export default  {
  entry: './client/index.js',
  output: {
    path: '/',
    filename: 'bundle.js'
  },
  module: {
    rules: [... ]
  },
  plugins: [..]
}; 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们导入所有需要的包:`webpack`当然，还有两个插件，我们将在以后使用时介绍。

我们要导出的对象包含所有 webpack 配置。同样，由于我们使用 webpack 来管理 react 代码，我们将入口点指定为主 react 代码，webpack 将获取它，编译它，并在`bundle.js`将其作为 es5 代码输出(它不会作为原始文件出现在您的目录中，但可以在浏览器`/bundle.js`中访问)

在我们继续之前，让我们安装上面导入的包

```
npm install --save-dev webpack html-webpack-plugin webpack-livereload-plugin 
```

Enter fullscreen mode Exit fullscreen mode

## 设置 webpack 规则

在里面，我们能够让 webpack 根据我们指定的规则执行各种操作。

第一条规则当然是让 webpack 把我们所有的 javascript 代码编译成 ES5，第二条规则是把我们所有的 css 代码都当作 css！

```
export default  {
  ...
  module: {
    rules: [
      {
        use: 'babel-loader',
        test: /\.js$/,
        exclude: /node_modules/
      },
      {
        use: ['style-loader', 'css-loader'],
        test: /\.css$/
      }
    ]
  },
  ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

不言而喻，我们基本上确保了如果正在处理的文件带有`.js`扩展名，那么通过`babel-loader`包运行它(不包括节点模块)。

如果文件有一个`.css`扩展名，运行它通过`style-loader`和`css-loader`包。

虽然我们不导入这些包，但我们确实需要安装它们

```
npm i --save-dev babel-loader style-loader css-loader babel-core 
```

Enter fullscreen mode Exit fullscreen mode

注意，使用`babel-loader`似乎也需要`babel-core`。

你可以添加很多其他的规则，关于图像、字体、svg、缩小等等的规则。

我喜欢 SASS，所以让我们编写另一个规则来处理扩展名为`.scss`的文件。还在`rules`阵内:

```
{
    test: /\.scss$/,
  use: [{
      loader: "style-loader"
  }, {
      loader: "css-loader", options: {
          sourceMap: true
      }
  }, {
      loader: "sass-loader", options: {
          sourceMap: true
      }
  }]
} 
```

Enter fullscreen mode Exit fullscreen mode

我直接从[文档](https://github.com/webpack-contrib/sass-loader#source-maps)中获取了上面的设置。这与其他测试相似，但是因为我们需要添加选项,`use`数组的值是对象。我们只是确保当我们的 SASS 编译成 CSS 时，会生成源映射(这对在浏览器中调试 SASS 非常有用)。

我们知道我们需要安装`sass-loader`,就像我们安装其他装载机一样。

```
npm i --save-dev sass-loader node-sass 
```

Enter fullscreen mode Exit fullscreen mode

(`sass-loader`需要使用`node-sass`)

有了这个设置，在`./client/index.js`中，我们将能够在 react 代码中导入 SASS 文件，webpack 将处理转换。

## [T1】设置 webpack 插件](#setting-up-webpack-plugins)

到目前为止，我们已经配置了输出和规则。当 Webpack 遇到我们的代码时，它完全知道该怎么做。现在我们想要合并我们所有的代码(从入口点)并把它们捆绑在一起

```
import webpack from 'webpack';
import HtmlWebpackPlugin from 'html-webpack-plugin';
import LiveReloadPlugin from 'webpack-livereload-plugin'

export default  {
  entry: './client/index.js',
  ....
  plugins: [
    new HtmlWebpackPlugin({
      template: 'client/index.html'
    }),
    new LiveReloadPlugin()
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

第一个插件`HtmlWebpackPlugin`负责把所有的东西放在一起，读取待运。注意入口点和模板，webpack 链接了两者，因此我们不需要在`client/index.html`中手动添加任何脚本标签

## 使用捆绑

我们已经决定使用 express 向浏览器发送内容。我们需要从 webpack 获取包并通过 express 提供它，这是有道理的。让我们在`server.js` :
中这样做

```
import express from 'express'; 
import webpack from 'webpack';
import webpackMiddleware from 'webpack-dev-middleware';
import webpackConfig from './webpack.config.js';

const app = express();
app.use(webpackMiddleware(webpack(webpackConfig)));

app.get('/api', (req, res) =>  )

app.listen(4000, () => {
  console.log('Listening');
}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的 express 代码中，我们导入我们的 webpack 文件并让 webpack 创建包(`webpack(webpackConfig)`)，然后我们将它转换成 express 可以理解的中间件(`webpackMiddleware(webpack(webpackConfig))`)，最后让 express 将它用作它的中间件。

该中间件接受捆绑的 react 应用程序，并将其提供给本地路由。我们仍然可以创建 react 路由(例如,`/api`),但是 home 路由由 express 应用程序接管。

剩下要做的就是安装我们上面使用的中间件包

```
npm i --save-dev webpack-dev-middleware 
```

Enter fullscreen mode Exit fullscreen mode

## 运行服务器

在`package.json`中，我们添加一个`npm`启动脚本。

```
 "scripts": {
    "start": "nodemon --exec babel-node server.js  --ignore client"
  } 
```

Enter fullscreen mode Exit fullscreen mode

然后，在终端中，我们只需要运行`npm start`，它反过来运行上面的行。我们正在做的是:我们用`nodemon`和`babel-node`运行`server.js`，但是我们告诉他们忽略`/client`文件夹。这是因为，那个特定的文件夹将由 webpack 来处理。

## 结论

你可以从 [github](https://github.com/aurelkurtula/minimal-node-environment) 中克隆这个项目

我一直犹豫要不要写这篇教程，因为我很少需要从头开始设置我的环境。然而，我觉得通过写这篇文章，我学到了更多关于 babel、webpack 和 express 如何协同工作的知识。我希望你也学到了一些东西。(**如有补充请评论:)**
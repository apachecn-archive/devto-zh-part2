# 学习 React -第 1 部分-使用 Webpack 4 (+ CSS / SASS)简单设置 React 应用程序

> 原文：<https://dev.to/felipegalvao/simple-setup-for-a-react-application-with-webpack-4--css--sass-bef>

[最初发表于我的博客](http://felipegalvao.com.br/blog/2018/07/24/simple-setup-for-a-react-application-with-webpack-4-css-sass/)

嘿，伙计们，你们好吗？在这篇文章中，我将向您展示一个快速简单的 React 应用程序的 Webpack 4 设置。

我假设您已经安装了`node`、`npm`和通常的怀疑对象。

首先，让我们创建我们的项目，运行命令`npm init -y`:

```
mkdir webpack-4-react
cd webpack-4-react/
npm init -y 
```

y 标志只是为了在初始化您的项目时，您不必对所有的 npm 问题都按“是”。

## Wepback 的安装

现在，让我们安装 Webpack，以便在我们的项目中使用它。我一直在用[纱线](https://yarnpkg.com/lang/en/)，但是 npm 也一样好用。总结一下，如果你用的是 npm，只需要把所有对`npm i x`的调用都改成`yarn add x`，当你需要作为开发依赖安装的时候，就把`yarn add x -D`换成`npm i x -D`。

接下来，让我们将 Webpack 作为开发依赖项添加到我们的项目中:

```
yarn add webpack webpack-cli webpack-dev-server -D 
```

这将安装 Webpack 开发包，以及 Webpack 的开发服务器，我们可以使用它在本地服务和测试我们的应用程序。这也将更新我们的`package.json`文件，添加这些包作为开发依赖项。

## 创建我们的文件

现在，让我们用一个指向`main.js`文件的脚本标签创建一个基本的 HTML 文件。这个`index.html`文件将位于`dist`文件夹中，同样的还有`main.js`文件，它将由 Webpack 创建(因此，不需要手动创建)。这个 Javascript 文件将包含我们的 React 应用程序代码，并将由 Webpack 生成。

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        React and Webpack4
    </head>
    <body>
        <section id="index"></section>
        <script type="text/javascript" src="main.js"></script>
    </body>
</html> 
```

现在，我们应该创建一个`src`文件夹，并在其中创建一个`index.js`文件，这将是 React 应用程序的起点。它的结构将是最简单的代码。

```
import React from "react";
import ReactDOM from "react-dom";

const Index = () => {
  return <div>Hello React!</div>; };

ReactDOM.render(<Index />, document.getElementById("index")); 
```

现在，为了工作，我们需要将初始化脚本添加到我们的`package.json`文件中。这些脚本将使 Webpack 发挥其魔力，根据我们将在几分钟内定义的配置转换我们的代码。第一个脚本是这样的:

```
"start": "webpack-dev-server --mode development --open", 
```

您将使用这个脚本进行本地开发。它将使用`webpack-dev-server`在本地提供您的文件，并且它将生成我们在上面几行 HTML 文件中链接的`main.js`文件。最后的`--open`标志将使 Webpack 在你的应用服务的本地地址打开你的默认浏览器。另一个脚本是这样的:

```
"build": "webpack --mode production" 
```

使用这个脚本，Webpack development server 将不会运行，但是 Webpack 将会生成您的应用程序文件，为生产做好准备，其中包含所有的代码和一些附加内容。

将这两个脚本添加到`scripts`键中。您的`package.json`文件现在应该是这样的:

```
{
  "name": "webpack-4-react-boilerplate",
  "version": "1.0.0",
  "author": "",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "webpack-dev-server --mode development --open",
    "build": "webpack --mode production"
  },
  "keywords": [],
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.16.1",
    "webpack-cli": "^3.0.8",
    "webpack-dev-server": "^3.1.4"
  }
} 
```

## Webpack 配置

好了，现在，如果你试图用`yarn run start`或`yarn run build`运行这些命令，它们将不起作用。那是因为，为了让 Webpack 理解我们创建的 React 代码，我们需要一些工具来 transpile，也就是把我们写的 React 代码转换成任何浏览器都能理解的代码。我们开始吧。首先，让我们安装必要的 React 包，`React`和`React DOM`。

```
yarn add react react-dom 
```

然后，我们需要安装 Babel 和一些加载程序来传输我们的代码。这些应作为开发依赖项安装:

```
yarn add @babel/core babel-loader @babel/preset-env @babel/preset-react -D 
```

在我们完成这些安装之后，您的`package.json`文件应该看起来像这样:

```
{
  "name": "webpack-4-react-boilerplate",
  "version": "1.0.0",
  "author": "",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "webpack-dev-server --mode development --open",
    "build": "webpack --mode production"
  },
  "keywords": [],
  "license": "ISC",
  "devDependencies": {
    "@babel/core": "^7.2.2",
    "@babel/preset-env": "^7.3.1",
    "@babel/preset-react": "^7.0.0",
    "babel-loader": "^8.0.5"
    "css-loader": "^1.0.1",
    "node-sass": "^4.10.0",
    "sass-loader": "^7.1.0",
    "style-loader": "^0.23.1",
    "webpack": "^4.26.0",
    "webpack-cli": "^3.1.2",
    "webpack-dev-server": "^3.1.10"
  },
  "dependencies": {
    "react": "^16.6.3",
    "react-dom": "^16.6.3",
    "react-scripts": "2.1.1"
  }
} 
```

现在，我们需要创建一个 Webpack 配置文件，即`webpack.config.js`文件。在我上一篇关于 Webpack 的文章中，我已经谈到了它，所以在这篇文章中我会更简短。您的文件应该是这样的:

```
module.exports = {
  entry: ["./src/index.js"],
  output: {
    path: __dirname + "/dist",
    publicPath: "/",
    filename: "main.js"
  },
  devServer: {
    contentBase: "./dist"
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
}; 
```

这是最简单的 Webpack 配置文件。简单解释一下:在`entry`中，我们为我们的应用程序定义了入口文件；在`output`中，我们包含了将由 Webpack 生成的 Javascript 文件的定义；在`devServer`中，我们定义了开发服务器提供文件的文件夹；在`module`中，我们定义了应用程序的一般规则，例如，在这种情况下，将使用什么来传输每种类型的文件。

除了 Webpack 配置文件，因为我们定义了`babel-loader`来传输我们的`.js`文件，我们需要创建 Babel 配置文件，它将指示 Babel 应该使用哪些加载器来传输我们的代码。这个文件，以及 Webpack 配置文件，应该位于您的项目的根目录下，并且应该被命名为`.babelrc`。这也是一个非常简单的文件，应该是这样的:

```
{
    "presets": ["@babel/preset-env", "@babel/preset-react"]
} 
```

现在，我们可以运行我们的命令来本地启动开发服务器:

```
yarn run start 
```

如果一切顺利，您将看到您的浏览器打开，在屏幕上，您的 React 组件带有`Hello React`消息。如果您从初始化脚本中删除了`--open`标志，您可以访问`webpack-dev-server`的默认地址，即[http://localhost:8080/](http://localhost:8080/)

## 添加 CSS

要添加 CSS，我们需要多做一点。为了传输 CSS 文件并在 React 应用程序中使用它们，我们需要更多的加载器。因为我们正在添加 CSS，所以让我们利用 [SASS](https://sass-lang.com/) 在我们的样式文件中添加一些额外的功能。首先，让我们安装一些软件包:`css-loader`、`style-loader`、`sass-loader`，最后是`node-sass`。它们都应该作为开发依赖项安装:

```
yarn add css-loader style-loader sass-loader node-sass -D 
```

现在让我们在 webpack 配置文件中添加另一个条目。这个条目将告诉 webpack 应该如何处理`.scss`文件，这是 SASS 标准文件格式。您的`webpack.config.js`文件现在将如下所示:

```
module.exports = {
  entry: ["./src/index.js"],
  output: {
    path: __dirname + "/dist",
    publicPath: "/",
    filename: "main.js"
  },
  devServer: {
    contentBase: "./dist"
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.scss$/,
        use: [
          {
            loader: "style-loader"
          },
          {
            loader: "css-loader"
          },
          {
            loader: "sass-loader"
          }
        ]
      }
    ]
  }
}; 
```

现在，您应该创建您的主样式文件。在`src`中，你可以创建`styles`文件夹，以便更好地组织你的风格文件。从您的主样式文件中，您将能够导入其他特定文件。所以，让我们创建`/src/styles/main.scss`文件:

```
p {
    background-color: teal;
} 
```

现在，在你的主 React 文件中，你需要做的就是用`import "./styles/main.scss";`导入 SCSS 文件。您的`index.js`现在将是这样的:

```
import React from "react";
import ReactDOM from "react-dom";

import "./styles/main.scss";

const Index = () => {
  return (
    <div>
      <p>Hello React!</p>
    </div>
  );
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

现在你可以看到你的`Hello React`信息有了不同的背景颜色。

正如我提到的，您可以在主文件中导入其他样式文件。我喜欢做的一件事是为组件创建单独的样式文件。例如，我可以创建一个`components`文件夹。在该文件夹中，我将创建一个`_Component.scss`。然后，让我们创建`/src/styles/components/_Component.scss`文件，并在其中包含一些基本的 CSS:

```
p {
    color: white;
} 
```

现在，在`main.scss`文件上，您需要做的就是导入它，就像这样:

```
@import "components/Component";

p {
    background-color: teal;
} 
```

现在，当您的浏览器刷新时，您会注意到带有“Hello React”消息的段落现在有了白色字体。像这样在`.scss`文件中导入的主要要求是，要导入的文件应该以`_`开头。

就像这样，我们有一个尽可能简单但相当完整的 React 项目，Webpack 4 e SASS。

如果你想看最终代码，可以点击这里访问 Github repo [。你可以随意克隆它，把它作为样板，随便什么。](https://github.com/felipegalvao/webpack-4-react-boilerplate)

希望这个 React 设置有用。有任何问题，请在评论区留言。

干杯！
# 使用 Webpack 和 React 实现与 Create-React-App(CRA) CLI 类似的工作流。

> 原文：<https://dev.to/olivermensahdev/before-using-create-react-appcra-understand-how-only-webpack-and-react-achieve-similar-workflow-55a4>

对 React 项目使用 Create-React-App(CRA)简化了开发工作流程。在其背后，是 webpack 的几乎所有任务的使用。但是如果你想知道这个 CRA 是如何工作的，那么这篇文章就是为你准备的。这篇文章是关于在 Webpack 的帮助下从头开始创建 React 项目。

首先，我们需要一些包来帮助我们创建一个 React 项目。

#### 依赖关系。

*   `react`:保存组件、状态、道具和所有 react 的代码的 react 源。
*   `react-dom`:帮助在浏览器中使用 React。

#### 发展依赖关系

*   `webpack`
*   `webpack-cli`
*   `webpack-dev-server`
*   `babel-core`
*   `babel-loader`
*   `babel-preset-env`
*   `babel-preset-react`
*   `html-webpack-plugin`

知道了 React 项目所需的包之后，让我们从创建一个项目开始。创建一个空文件夹，并通过命令行或终端导航到项目文件夹。由于我们将把这些包安装到我们的项目中，我们需要从创建 package.json 文件开始，该文件将保存关于我们项目的元数据。您可以通过运行
来做到这一点

```
 npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

然后，您需要按照
的方式安装依赖项

```
 npm i react react-dom -E 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装完成，我们现在可以添加 devdependencies 作为

```
 npm i webpack webpack-cli webpack-dev-server babel-core babel-loader babel-preset-env babel-preset-react html-webpack-plugin -DE 
```

Enter fullscreen mode Exit fullscreen mode

我们需要为 webpack 和 babel 设置配置，这将有助于 webpack 知道应该从哪里开始编译必要的文件以及其他操作所需的重要信息。

在项目的根目录下，创建`webpack.config.js`文件并添加以下内容。

```
 const path = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');

    module.exports = {
        entry:'./src/index.js',
        output: {
            path: path.join(__dirname, '/dist'),
            filename: 'bundle.js'
        },
        module:{
            rules:[
                {
                    test:/\.js$/,
                    exclude: /node_modules/,
                    use: {
                        loader: 'babel-loader'
                    }
                }
            ]
        },
        plugins: [
            new HtmlWebpackPlugin({
                template: './src/index.html'
            })
        ]
    } 
```

Enter fullscreen mode Exit fullscreen mode

在配置文件中，我们有入口、输出、模块和插件键。Webpack 只需要具备这 4 个核心要素就可以正确执行。

入口:webpack 应该开始编译文件的地方，这是我们的入口点，`src/index.js`
输出:我们希望我们编译的代码去的地方，它将是一个捆绑的`js`文件
模块:这是我们放置所有加载器的地方。它寻找所有要由 webpack 编译的`js`文件。此外，我们不需要编译 node_modules，所以我们将它们排除在外。
插件:我们不想在通过构建 react 项目创建的 dist 文件夹中手动添加`index.html`。所以我们允许 HtmlWebpackPlugin 为我们做这件事。基本上就是说使用`./src/index.html`文件在 dist 文件夹中创建一个 index.html 文件。

我们需要为巴别塔添加一个配置，它应该使用哪个预置。在项目根目录下创建`.babelrc`文件，并添加以下内容。

```
 {
        "presets": ["env", "react"]
    } 
```

Enter fullscreen mode Exit fullscreen mode

太棒了，我们已经为我们的开发做好了准备，现在我们可以开始写一些 react 代码了。但是在 webpack 配置中，我们需要在`src`文件夹中有一个名为`index.js`的条目文件，所以我们需要创建它。在该文件中添加以下代码。

```
 import React from 'react';
    import ReactDOM from 'react-dom';
    import App from './components/App';

    ReactDOM.render(<App />, document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

从同一个 webpack 配置中，我们需要创建`./src/index.html`文件。把这些内容加进去。

```
 <!DOCTYPE html>
    <html lang="en">

    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      My React App
    </head>

    <body>
      <div id="app"></div>
    </body>

    </html> 
```

Enter fullscreen mode Exit fullscreen mode

从`./src/index.js`文件中，我们从`/components/App`导入一个`App`组件，由 ReactDOM 渲染。我们也需要创建那个文件。

```
 import React, { Component } from 'react';
    class App extends Component {
      render() {
        return (
          <div>
            <h1> React is Awesome</h1>
          </div>
        );
      }
    }
    export default App; 
```

Enter fullscreen mode Exit fullscreen mode

要运行示例 react 应用程序，我们需要向 package.json 中的脚本添加以下脚本。

```
 "start": "webpack-dev-server --mode development --open --hot",
     "build": "webpack --mode production" 
```

Enter fullscreen mode Exit fullscreen mode

通过在您的终端中执行`npm run start`来运行应用程序，并使用`npm run build`构建 react 应用程序，以将应用程序构建到 dist 文件夹中。

如果您一直在关注，您可以从 [my github repo](https://github.com/OMENSAH/react-without-cra) 下载代码

## 结论。

现在，您已经学习了如何使用 webpack 和 React 来实现与 CRA CLI 几乎相同的工作流，并消除了对 CRA 工作方式的疑虑。

注:如果有些事情不够清楚，让我们在评论区讨论
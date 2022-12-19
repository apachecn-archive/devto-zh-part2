# 从头开始设置 React 工具链

> 原文：<https://dev.to/mahmoudelmahdi/set-up-react-toolchain-from-the-ground-up-3340>

本指南旨在帮助那些希望在没有预先配置的环境或幕后隐藏的魔法的情况下，或者至少对幕后发生的事情有所了解的人从头开始使用 React 应用程序。为了充分利用本指南，我强烈建议你一步一步地去做。

## 谜题

React 应用构建通常是一套用于执行复杂任务的工具，它由三个主要工具组成:**包管理器** [Yarn](https://yarnpkg.com/en/) 或 [Npm](https://www.npmjs.com/) ， **Bundler** [webpack](https://webpack.js.org/) 或[Package](https://parceljs.org/)，以及**编译器**如 [Babel](https://babeljs.io/) (新的 JavaScript 特性)。

## 先决条件

*   节点版本:> = 8.0.0
*   包装经理[纱线](https://yarnpkg.com/en/docs/install)

> 📌下面的代码片段使用`$`来表示类 UNIX 操作系统中的 shell 提示符，尽管它可能被定制为不同的外观。

## 创建项目目录

```
$ mkdir app && cd $_
$ yarn init -y 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，上面的命令非常简单；它做三件事:

*   在当前目录中创建新的“app”文件夹，
    *   然后，它将当前工作目录更改为我们最近创建的项目(第二个语句执行)。
*   将 yarn 初始化为我们的包管理器(交互地创建一个 package.json 文件)。标志`-y`(或`--yes`)根据你的[默认值](https://yarnpkg.com/en/docs/cli/init#toc-setting-defaults-for-yarn-init)生成一个 package.json

## 项目目录结构

在我们深入研究安装、配置和所有讨厌的事情之前！让我们首先构建我们的项目目录，以便对我们的起点和终点有一个全面的了解。

> 📌对于 Windows 操作系统用户，你必须使用`type null >> file`而不是`touch`命令。`mkdir -p src/{...}`命令也不起作用，你可能会发现这个[等效解决方案](https://stackoverflow.com/questions/905226/what-is-equivalent-to-linux-mkdir-p-in-windows)很有帮助，或者单独创建这些子目录/目录。

```
$ mkdir -p src/components tests
$ touch src/index.html src/index.js
$ touch .babelrc .eslintrc.yml jest.config.js webpack.config.js 
```

Enter fullscreen mode Exit fullscreen mode

上面我们执行的命令将产生如下的文件夹结构:

```
app/
├── src/
│ + components/
│ └── index.html
└── index.js
├── tests/
├── .babelrc
├── .eslintrc.yml
├── jest.config.js
└── webpack.config.js 
```

Enter fullscreen mode Exit fullscreen mode

> 💡提示:对于 Linux 和 Macintosh 用户，使用`ls -R`命令行列出所有文件&子目录的内容

## 依赖关系

我更喜欢使用括号扩展(下面的*片段)来安装名称相似的包；它可以修改，更漂亮，更省时。*

**反应** : [反应](https://yarnpkg.com/en/package/react)，[反应-dom](https://yarnpkg.com/en/package/react-dom) ，[反应-热装](https://yarnpkg.com/en/package/react-hot-loader)

```
$ yarn add react react-{dom,hot-loader} 
```

Enter fullscreen mode Exit fullscreen mode

## 对反应道具进行类型检查

从 React v15.5 开始，在[官方文档](https://reactjs.org/docs/typechecking-with-proptypes.html)中提到不推荐使用`React.PropTypes`，建议安装并使用独立的`prop-types\`库。你猜怎么着？你不需要！

**一石二鸟**；截至今天，React v16.0 使用道具类型作为其依赖项之一(*可能会在未来的版本*中删除)，通过安装 [react](https://yarnpkg.com/en/package/react) ，您应该可以开箱即用[道具类型](https://yarnpkg.com/en/package/prop-types)。

[![](../Images/f7fad184412a437a7644e074c0132aa0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w6h8VO5j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g4c68n8n2upf49336odm.png)

**巴比伦** : [@babel/core](https://yarnpkg.com/en/package/@babel/core) 、 [@babel/preset-env](https://yarnpkg.com/en/package/@babel/preset-env) 、 [@babel/preset-react](https://yarnpkg.com/en/package/@babel/preset-react) 、[babel-eslin](https://yarnpkg.com/en/package/babel-eslint)、 [babel-jest](https://yarnpkg.com/en/package/babel-jest)

```
$ yarn add @babel/{core,preset-env,preset-react} -D
$ yarn add babel-{loader,eslint,jest} -D 
```

Enter fullscreen mode Exit fullscreen mode

节点模块上的(@)前缀称为“作用域包”。作用域是将相关包组合在一起的一种方式。

[![](../Images/abcb8732a2dbaa3d938fa29a3f23e311.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IRmCwrXg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/185obe422mwg7kxnipih.png)

**Webpack** : [webpack](https://yarnpkg.com/en/package/webpack) ， [webpack-cli](https://yarnpkg.com/en/package/webpack-cli) ， [webpack-dev-server](https://yarnpkg.com/en/package/webpack-dev-server) ， [html-webpack-plugin](https://yarnpkg.com/en/package/html-webpack-plugin)

```
$ yarn add webpack webpack-{cli,dev-server} -D
$ yarn add html-webpack-plugin -D 
```

Enter fullscreen mode Exit fullscreen mode

**林挺**(可选):[诚信通](https://yarnpkg.com/en/package/eslint)，[诚信通-插件-反应](https://yarnpkg.com/en/package/eslint-plugin-react)

```
$ yarn add eslint eslint-plugin-react -D 
```

Enter fullscreen mode Exit fullscreen mode

**测试**(可选):[玩笑](https://yarnpkg.com/en/package/jest)，[反应-测试-呈现](https://yarnpkg.com/en/package/react-test-renderer)

```
$ yarn add jest react-test-renderer -D
$ yarn add babel-core@7.0.0-bridge.0 -D 
```

Enter fullscreen mode Exit fullscreen mode

## 配置&设置

**web pack**:web pack . config . js

```
const HtmlWebPackPlugin = require('html-webpack-plugin');

const HtmlWebpackPluginConfig = new HtmlWebPackPlugin({
  template: './src/index.html',
  filename: './index.html'
});

module.exports = {
  module: {
   rules: [
      {
        test: /\.js|jsx$/,
        exclude: /node_modules/,
        use: { loader: 'babel-loader' }
      }
    ]
  },
  mode: 'development',
  devServer: {
    historyApiFallback: true,
    compress: true,
    port: 3030,
    open: true
  },
  plugins: [
    HtmlWebpackPluginConfig
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

分解 webpack 配置:

*   规则:我们附加了 babel 作为我们的加载器，当创建模块时，规则与请求相匹配
*   **模式** : webpack 4 引入了生产和开发模式，用于定义 webpack 开发服务器和其他东西。我们稍后在 package.json 脚本中内联设置生产模式
*   **devServer** :一组基本的[选项](https://webpack.js.org/configuration/dev-server/#devserver)
*   **插件**:用于服务我们的 webpack 包，从`./src`下的模板文件生成`./dist/index.html`
*   **入口**:正如你可能注意到的，我们的配置中没有入口点，幸运的是 webpack 4 为我们提供了现成的默认入口点`./src/index.js` ( *你可以覆盖它*

**巴别塔**:。巴伯尔克

```
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-react"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

**是**:是. config.js

```
module.exports = {
  verbose: true,
  moduleDirectories: ['node_modules', 'src/components', 'tests']
}; 
```

Enter fullscreen mode Exit fullscreen mode

出于教程的目的，我指定“src/components”作为我的模块目录，但是随着你的应用程序的增长，你必须根据你的需要覆盖这个设置。

斯洛文尼亚语:
斯洛文尼亚语

```
parser: "babel-eslint"
env:
  browser: true
  node: true
  jest: true
  es6: true
extends:
  - eslint:recommended
  - plugin:react/recommended
settings:
  react:
    version: "16.0" 
```

Enter fullscreen mode Exit fullscreen mode

*   解析器:指定我们需要支持的 JavaScript 语法，在我们的例子中，我们设置了 Babel(用于现代 JavaScript 语言)
*   **env** :一个[环境](https://eslint.org/docs/user-guide/configuring#specifying-environments)定义了预定义的全局变量
*   **扩展**:从基础配置中启用的一组规则
*   **设置**:共享[设置](https://eslint.org/docs/user-guide/configuring#adding-shared-settings)它将被提供给每一个将要执行的规则。

我在这里对配置文件使用 YAML 扩展来指定整个目录配置信息，但是您完全可以随意使用，比如 JavaScript 或 JSON。

**脚本** : package.json

```
"scripts": {
  "start": "webpack-dev-server --hot",
  "lint": "eslint ./src",
  "test": "jest --colors -b -e --logHeapUsage",
  "build": "webpack --mode production"
}, 
```

Enter fullscreen mode Exit fullscreen mode

**React** : src/index.html 渲染容器

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8”>
    <meta name="viewport" content="width=device-width">
    App
  </head>
  <body>
    <div id="root"></div>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

**反应**:创建 src/components/App.js 作为我们的初始组件

```
import React from 'react'
import PropTypes from 'prop-types';
import { hot } from 'react-hot-loader';

const App = ({message}) =><h1>Hello from {message}</h1>;  
App.propTypes = { 
  message: PropTypes.string 
}; 

export default hot(module)(App); 
```

Enter fullscreen mode Exit fullscreen mode

App 组件是一个代表我们的应用程序的无状态组件，由一个实时调整的加载程序包装。

**React** :打开并编辑我们的入口点 src/index.js

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';

ReactDOM.render(
  <App />,
  document.getElementById('root')
); 
```

Enter fullscreen mode Exit fullscreen mode

## 跑小子跑

在我们继续前进之前，我们需要确保一切工作正常

```
$ yarn start 
```

Enter fullscreen mode Exit fullscreen mode

**预期行为**:

应在默认浏览器中打开一个新标签，带有 localhost:3030 地址
DevTools 验证我们的热模块替换已启用
web pack，编译成功

[![](../Images/e995deff64b8264ae2bb5ef14a013ef5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TTRrOwXx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n8swp1r9uqmc5lmukwcj.png)

* * *

## 测试

我们不会用 jest 来深入测试细节。我们将创建一个最小的快照测试，以确保我们的测试配置正常工作。在测试/目录下创建 App.test.js 文件。

**Jest** :创建测试/组件/App.spec.js

```
import React from 'react';
import renderer from 'react-test-renderer';
import App from 'App';

describe('App Component Test Suite', () => {
  it('Should render properly to DOM', () => {
    const wrapper = renderer.create();
    expect(wrapper.toJSON()).toMatchSnapshot();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

…然后运行测试脚本:

```
$ yarn test 
```

Enter fullscreen mode Exit fullscreen mode

第一次运行这个测试时，Jest 会创建一个快照文件。

## 最后但并非最不重要

我们现在准备在生产模式下捆绑我们的 React 应用程序，并优化构建以获得最佳性能，我们所需要的就是运行以下命令:

```
$ yarn build 
```

Enter fullscreen mode Exit fullscreen mode

这将把生产应用程序构建到 dist/build 文件夹中。

## 干得好🙌

一旦你完成了这些，一切都如预期的那样工作，你现在已经掌握了从头开始建立你自己的 React 应用程序的基本技术。您可以通过以下方式更进一步:

*   扩展 webpack 加载器，例如:[样式加载器](https://github.com/webpack-contrib/style-loader)、 [CSS 加载器](https://github.com/webpack-contrib/css-loader)或 [Sass 加载器](https://github.com/webpack-contrib/sass-loader)到您的 webpack 配置中
*   用 Yarn/Npm 脚本添加 Git 钩子:`precommit`，`prepush`。例如，在进行新提交之前运行 linter
*   ...还有很多你可以玩的东西

* * *

如果你喜欢这篇文章，我会非常感激你把它发给你的朋友，或者在社交媒体上分享。谢谢大家！

> [从头开始建立 React 工具链](https://elmahdim.com/react-webpack-babel/) - Mahmoud Elmahdi
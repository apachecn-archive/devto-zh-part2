# 在 create-react-app 中使用 styled-jsx 而不弹出

> 原文：<https://dev.to/greglobinski/use-styled-jsx-in-create-react-app-without-ejection-1aa1>

我喜欢 [styled-jsx](https://github.com/zeit/styled-jsx) 。我用它做了一个[盖茨比](https://gatsbyjs.org) [首发](https://github.com/greglobinski/gatsby-starter-hero-blog)。

如果您不知道， [GatsbyJS](https://gatsbyjs.org) 是 ReactJS 的静态站点生成器。

我假设你知道什么是 [styled-jsx](https://github.com/zeit/styled-jsx) ，否则你不会对这个标题感兴趣。为了以防万一， **styled-jsx** 是一个‘JS 中的 CSS’库。

当我写的时候，我获得了一些使用 **styled-jsx** 和 **GatsbyJS** 的经验，但是几天前我想在一个基于 [create-react-app](https://github.com/facebook/create-react-app) 的应用中使用这个库。我很确定你知道**创建-反应-应用**是什么。:)

然而有一个问题。

为了让 **styled-jsx** 工作，我们必须编辑项目的 [babel](https://babeljs.io/) 配置。没什么复杂的，但是 **create-react-app** 不允许任何那样的改变，除非**弹出**。然而，弹射不是我想要的东西。

我被卡住了。

## React 应用程序重新布线

幸运的是， [react-app-rewired](https://github.com/timarney/react-app-rewired) 前来救援。

> 调整 create-react-app webpack 配置，但不使用“eject ”,也不创建 react-scripts 的分支。create-react-app 的所有优点，没有“无配置”的限制。你可以添加任何你需要的插件，加载器。

所以让我们让 **styled-jsx** 在一个基于 **create-react-app** 的应用程序中工作。

## Rewire create-react-app 使用 styled-jsx

首先用`create-react-app`创建一个 app。

```
npx create-react-app my-styled-jsx-app 
```

移动到新创建的文件夹。

```
cd my-styled-jsx-app 
```

然后安装`react-app-rewire`作为开发依赖项。

```
yarn add react-app-rewired --dev 
```

而`styled-jsx`作为依赖。

```
yarn add styled-jsx 
```

是时候重新布线了。打开`package.js`文件并在此编辑...

```
"scripts": {
  "start": "react-scripts start",
  "build": "react-scripts build",
  "test": "react-scripts test --env=jsdom",
} 
```

...敬这个。

```
"scripts": {
  "start": "react-app-rewired start",
  "build": "react-app-rewired build",
  "test": "react-app-rewired test --env=jsdom"
} 
```

现在我们可以根据需要编辑`babel`的配置了。

在 app 的根目录下新建一个名为`config-overrides.js`的文件。

```
const { injectBabelPlugin } = require("react-app-rewired");

module.exports = function override(config, env) {
  config = injectBabelPlugin(["styled-jsx/babel"], config);

  return config;
}; 
```

就是这样。我们有一个基于`create-react-app`的应用程序，我们可以在不弹出的情况下使用`styled-jsx`。

## 用 styled-jsx 重新设计应用程序的样式

将 css 样式规则从外部 CSS 文件移动到`App`组件的文件中。

```
import React, { Component } from "react";
import logo from "./logo.svg";

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">Welcome to React</h1>
        </header>
        <p className="App-intro">
          To get started, edit <code>src/App.js</code> and save to reload.
        </p>
        {`/* global styles */`}
        <style jsx global>
          {`
            body {
              margin: 0;
              padding: 0;
              font-family: sans-serif;
            }
          `}
        </style>
        {`/* local styles */`}
        <style jsx>{`
          .App {
            text-align: center;
          }

          .App-logo {
            animation: App-logo-spin infinite 20s linear;
            height: 80px;
          }

          .App-header {
            background-color: #222;
            height: 150px;
            padding: 20px;
            color: white;
          }

          .App-title {
            font-size: 1.5em;
          }

          .App-intro {
            font-size: large;
          }

          @keyframes App-logo-spin {
            from {
              transform: rotate(0deg);
            }
            to {
              transform: rotate(360deg);
            }
          }
        `}</style>`
      </div>
    );
  }
}

export default App; 
```

## 清算

最后要做的事情是做一些清理。

删除不必要的 CSS 文件。

```
remove src/index.css scr/App.css 
```

以及来自`App.js`和`index.js`的`import`声明。

## 演示

Github 上有一个 repo-[example-create-react-app-styled-jsx](https://github.com/greglobinski/example-create-react-app-styled-jsx)-附带一个[的现场演示](https://greglobinski.github.io/example-create-react-app-styled-jsx/)。
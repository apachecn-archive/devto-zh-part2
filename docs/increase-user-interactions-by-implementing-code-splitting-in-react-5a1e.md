# React 中的代码拆分

> 原文：<https://dev.to/sagar/increase-user-interactions-by-implementing-code-splitting-in-react-5a1e>

嗨，我是 Sagar，是一名高级软件工程师。我喜欢写一些文章来帮助开发者理解 JavaScript 的魔力。如果你对这篇文章有任何问题，请留下评论，我会回复你，或者在 twitter 上找到我 [@sagar_dev44](https://twitter.com/sagar_dev44) 。

在 JavaScript 生态系统中，在包管理器注册表中有很好的库和框架，在日常生活中，我们将它导入到我们的项目中。当你刚刚开始你的项目时，这没什么，但是一旦你的项目发展起来，你就会面临许多与性能相关的问题。

在这篇文章中，我们将关注一些常见的问题，如大的包大小启动缓慢，并简单地在 React 应用程序中实现代码分割来解决它。

## 捆绑

大多数现代应用程序通过使用 [Webpack](https://webpack.js.org/) 或 [Browserify](http://browserify.org/) 被“捆绑”成一个文件。捆绑代码库是一个很好的方法，直到你的应用程序非常小并且依赖有限。一旦你的代码库增长，你的包的大小也会增长，然后问题就出现了，比如一个大的包，缓慢的启动，缓慢的热模块替换等等。

如果您对捆绑是如何工作的感到好奇，我强烈建议您阅读 webpack 的官方文档。

## 代码拆分

处理大型包和缓慢启动的完美解决方案是在你的应用程序中实现代码分割，即把你的代码分割成较小的块，然后可以按需或并行加载。

最佳实践是将你的块大小保持在 150KB 以下，这样即使在糟糕的网络上，应用程序也会在 3-5 秒内变得更具交互性。

使用 [Create React App](https://github.com/facebook/create-react-app) 、 [Next.js](https://nextjs.org/) 或 [Gatsby](https://gatsbyjs.org/) 创建应用的显著好处是，它们提供开箱即用的代码分割设置，或者你可以自己设置。

如果您想自己设置代码分割，请参见 Webpack 文档上的[安装](https://webpack.js.org/guides/installation/)和[入门](https://webpack.js.org/guides/getting-started/)指南。

## `import()`–动态导入 ES 模块

将代码分割引入应用程序的最佳方式是通过动态导入()。它使我们能够动态加载 es 模块。默认情况下，ES 模块是完全静态的。您必须指定在编译时导入和导出的内容，并且不能在运行时更改。

```
import CONSTANTS from './constants/someFile.js'; // importing CONSTANTS from someFile.js by using es import 
```

Enter fullscreen mode Exit fullscreen mode

es 模块有一些限制，例如 es 模块应该只出现在文件的顶层，这意味着如果我们在 ES 模块导入上面提到任何语句，它都会抛出一个错误，另一个错误是，模块路径是固定的，我们不能计算或动态改变它。

例如，

```
const double = (x) => x*x;
import CONSTANTS from './constants/someFile.js'; // it will throw an error because we created double function above es import module 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，动态导入()es 模块克服了 es 模块的局限性，并提供了异步模块导入功能。

```
const modulePath = './someFile.js'; // path of module
// dynamic import() module
import(modulePath).then(module => {
  return module.default; // return default function of es module
}); 
```

Enter fullscreen mode Exit fullscreen mode

使用动态`import()`我们可以指定 es 模块路径，或者我们可以在运行时改变路径，它返回一个承诺，如果它抛出错误，我们必须在`.then()`方法或`.catch()`方法中处理这个承诺。

注意，动态`import()`语法是 ECMAScript (JavaScript)建议，目前不是语言标准的一部分。预计在不久的将来会被接受。

有两种方法可以在你的应用中实现代码分割，即`route-based`或`component-based`代码分割。你必须决定在你的应用程序中的什么地方引入代码分割可能有点棘手。

## 基于路由的代码拆分

开始代码拆分的一个好地方是应用程序路由。将应用程序分解为每条路线块，然后在用户导航该路线时加载该块。在幕后，webpack 负责创建块，并按需向用户提供块。

我们只需创建一个 asyncComponent，并使用动态`import()`函数导入所需的组件。

让我们创建一个`asyncComponent`组件，它通过动态`import()`返回对组件的承诺来获取所需的组件。成功解决组件承诺后，它将返回所需的组件。简而言之，动态`import()`异步导入组件。

```
// filename: asyncComponent.jsx
import React, { Component } from "react";

const asyncComponent = (getComponent) => {
  // return AsyncComponent class component
  return class AsyncComponent extends Component {
    static Component = null;
    state = {
      Component: AsyncComponent.Component // first time similar to static Component = null
    };

    componentWillMount() {
      if (!this.state.Component) {
        // if this.state.Component is true value then getComponent promise resolve with .then() method
        // For simplicity, I haven't caught an error, but you can catch any errors or show loading bar or animation to user etc.
        getComponent().then(({ default: Component }) => {
          AsyncComponent.Component = Component;
          this.setState({ Component }); // update this.state.Component
        });
      }
    }

    render() {
      const { Component } = this.state; // destructing Component from this.state
      if (Component) {
        // if Component is truthy value then return Component with props
        return <Component {...this.props} />;
      }
      return null;
    }
  };
};

export default asyncComponent; 
```

Enter fullscreen mode Exit fullscreen mode

我们正在做几件事:

1.  `asyncComponent`函数将`getComponent`作为一个参数，当被调用时，它将动态地`import()`运行一个给定的组件。
2.  在`componentWillMount`上，我们简单地用`.then()`方法解析 promise，然后将`this.state.Component`状态突变为动态加载的组件。
3.  最后，在`render()`方法中，我们用`props`从`this.state.Component`返回加载的组件。

现在，该用`asyncComponent`了。从使用 react-router-app 分离应用程序的路由开始。

```
// filename: index.js
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router, Route, Switch } from "react-router-dom";
import asyncComponent from "./asyncComponent";

// import components with asyncComponent (indirectly using dynamic import() function)
const App = asyncComponent(() => import("./App"));
const About = asyncComponent(() => import("./About"));
const PageNotFound = asyncComponent(() => import("./PageNotFound"));

ReactDOM.render(
  <Router>
    <Switch>
      <Route path="/" component={App} exact />
      <Route path="/about" component={About} exact />
      <Route component={PageNotFound} />
    </Switch>
  </Router>,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

如果你用一个由`Create React App`创建的应用程序运行`yarn run build`，你会看到我们的应用程序已经被分成了几个块。

```
# Before implementing code splitting

File sizes after gzip:

  38.35 KB  build/static/js/1.3122c931.chunk.js
  797 B     build/static/js/main.70854436.chunk.js
  763 B     build/static/js/runtime~main.229c360f.js
  511 B     build/static/css/main.a5142c58.chunk.css

# After implementing code splitting

File sizes after gzip:

  38.33 KB  build/static/js/5.51b1e576.chunk.js
  1.42 KB   build/static/js/runtime~main.572d9e91.js
  799 B     build/static/js/main.3dd161f3.chunk.js
  518 B     build/static/js/1.5f724402.chunk.js
  327 B     build/static/css/1.f90c729a.chunk.css
  275 B     build/static/css/main.6a5df30c.chunk.css
  224 B     build/static/js/2.4a4c0b1e.chunk.js
  224 B     build/static/js/3.76306a45.chunk.js 
```

Enter fullscreen mode Exit fullscreen mode

如果你清楚地观察了块的大小，除了剩下的两三个块之外，所有块的大小都小于 100KB。

不要过多考虑`asyncComponent`编码的东西，稍后我们将引入一个`React-Loadable`库，它为我们提供了一个灵活的 API 来实现代码分割。

## 基于组件的代码拆分

正如我们前面看到的，基于路由的代码分割非常简单，我们将代码块分解为应用程序路由。

如果你的具体路线过于复杂，大量使用 UI 组件、模型、标签等。它的块大小比标准块大，如 150KB。在这种情况下，我们必须前进一步，在组件的基础上拆分代码，也称为**基于组件的代码拆分**。

```
// filename: App.jsx
import React, { Component } from "react";
import asyncComponent from "./asyncComponent"; // imported asyncComponent

// simple class based App component
class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      Greeting: null // <== initially set to null
    };
  }

  // handle button clicks
  handleButtonClick = () => {
    if (!this.state.Greeting) {
      // load Greeting component with dynamic import
      const Greeting = asyncComponent(() => import("./Greeting"));
      this.setState(prevState => {
        return {
          Greeting
        };
      });
    }
  };

  render() {
    const { Greeting } = this.state; // grab Greeting component from state
    return (
      <React.Fragment>
        <button onClick={this.handleButtonClick}>Click me</button>
        {Greeting && <Greeting message="lorem ipsum dummy message" />}
      </React.Fragment>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

我们正在做几件事:

1.  我们用一个`button`创建了一个简单的`<App />`类组件。
2.  在`<App />`组件中，点击按钮时，我们动态导入`<Greeting/>`组件并存储在`this.state.Greeting`状态中。
3.  在 render()方法中，首先我们从`this.state`中析构`Greeting`，并存储在一个`Greeting`常量中。后来用逻辑`&&` (AND)运算符，我们交叉检查它不是`null`值。一旦问候是真值，那么我们就将`<Greeting />`组件直接用于`jsx`。
4.  在后台，Webpack 为`<Greeting />`组件创建单独的块，并按需提供给用户。

## 反应可加载

`React Loadable`是由 [@jamiebuilds](https://twitter.com/jamiebuilds) 设计的一个小库，它使得在 React 应用中实现代码拆分变得极其容易。它通过使用动态`import()`和 Webpack 来完成代码拆分。

`React Loadable`提供了`Loadable`更高阶的组件，让你在渲染到你的应用程序之前动态加载任何模块。

使用 npm 或 yarn 将 react-loadable 包安装到您的应用程序中。

```
yarn add react-loadable # I'm sticking with yarn for this article. 
```

Enter fullscreen mode Exit fullscreen mode

**使用 React loadabled**实现基于路由器的代码分割

非常简单，你不需要制作任何异步组件，也不需要编写复杂的设置。只需导入`Loadable`组件并提供`loader`。

```
// filename: index.js
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router, Route, Switch } from "react-router-dom";
import Loadable from 'react-loadable';

const Loading = () => <h1>Loading...</h1>; // loading component

// dynamic loading <App />, <About /> and <PageNotFound /> components
// Loadable is higher order components. it takes loader which dynamic import() of desired component
// and loading which component shows during successfully resolving dyanmic import()
const App = Loadable({
  loader: () => import("./App"),
  loading: Loading
});

const About = Loadable({
  loader: () => import("./About"),
  loading: Loading
});

const PageNotFound = Loadable({
  loader: () => import("./PageNotFound"),
  loading: Loading
});

ReactDOM.render(
  <Router>
    <Switch>
      <Route path="/" component={App} exact />
      <Route path="/about" component={About} exact />
      <Route component={PageNotFound} />
    </Switch>
  </Router>,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

**使用 React loadabled**实现基于组件的代码拆分

基于组件的代码分割就像我们在上一节看到的一样简单。

```
import React, { Component } from "react";
import Loadable from "react-loadable";

const Loading = () => <h1>Loading...</h1>; // loading component

class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      Greeting: null
    };
  }

  handleButtonClick = () => {
    if (!this.state.Greeting) {
      // load Greeting component with Loadable component
      const Greeting = Loadable({
        loader: () => import("./Greeting"),
        loading: Loading
      });
      this.setState(prevState => {
        return {
          Greeting
        };
      });
    }
  };

  render() {
    const { Greeting } = this.state; // grab Greeting component from state
    return (
      <React.Fragment>
        <button onClick={this.handleButtonClick}>Click me</button>
        {Greeting && <Greeting message="lorem ipsum dummy message" />}
      </React.Fragment>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

我希望你喜欢这篇文章。如果你好奇或者想探索更多的代码分割，我为你提供了很好的参考。

您已经在 React 中进行了代码拆分。现在是派对时间。

[![party time](../Images/d203ec64aa56725a2f654c722a7ea23e.png)T2】](https://i.giphy.com/media/s2qXK8wAvkHTO/giphy.gif)

## 参考文献:

1.  [https://reactjs.org/docs/code-splitting.html](https://reactjs.org/docs/code-splitting.html)
2.  [https://developers . Google . com/web/fundamentals/performance/optimizing-JavaScript/code-splitting/](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/code-splitting/)
3.  [https://hacker noon . com/effective-code-splitting-in-react-a-practical-guide-2195359 d5d 49](https://hackernoon.com/effective-code-splitting-in-react-a-practical-guide-2195359d5d49)
4.  [https://alligator.io/react/react-loadable/](https://alligator.io/react/react-loadable/)
5.  [https://webpack.js.org/guides/code-splitting/](https://webpack.js.org/guides/code-splitting/)
# 如何在服务器端渲染 React，在客户端合成 React，并结合客户端和服务器路由

> 原文：<https://dev.to/marvelouswololo/how-to-server-side-render-react-hydrate-it-on-the-client-and-combine-client-and-server-routes-1a3p>

# 如何在服务器端渲染 React，在客户端水合，结合客户端和服务器路由

在这篇文章中，我想分享一种简单的方法来在服务器端呈现您的 React 应用程序，并在客户端合成您的 Javascript 包。如果你不知道“水合物”是什么，我会试着解释:想象一下
你使用 ReactDOMServer API 将 React 组件呈现为一个字符串，
你将向客户端发送 HTML，这是静态的。为了处理您在组件中设置的
动态事件，您必须将这个 HTML
标记附加到它原来的 React 组件上。React 通过向生成的标记发送一个标识
来做到这一点，因此它能够在以后解析哪个事件应该
附加到 DOM 中的哪个元素。(有点)。在
官方文档可以阅读更多[。](https://reactjs.org/docs/react-dom-server.html)

### [下面是最终代码](https://github.com/MarvelousWololo/ssr-react)和[演示](https://react-ssr-mw.herokuapp.com)

在我之前试图在服务器上正确地渲染我的应用程序，并在客户端上进行水合
时，我迷失在 Webpack 的配置中:它在任何主要版本中都有相当大的变化，所以文档和教程经常是过时的。这也是我试图为你节省一些时间。

为了简化学习过程，我尽量保持它的冗长，所以我把它分成了七个部分:

1.  初始 Webpack 配置
2.  第一次服务器端渲染
3.  切换到流
4.  将快速路由器和反应路由器结合起来
5.  使用快速查询字符串
6.  创建测试环境
7.  (试着)代码分割

## 初始 Webpack 配置

首先我们应该安装我们的依赖项:

```
npm i -E express react react-dom 
```

Enter fullscreen mode Exit fullscreen mode

和我们的开发依赖:

```
npm i -DE webpack webpack-cli webpack-node-externals @babel/core babel-loader @babel/preset-env @babel/preset-react 
```

Enter fullscreen mode Exit fullscreen mode

其他有助于我们开发的工具:

```
npm i -DE concurrently nodemon 
```

Enter fullscreen mode Exit fullscreen mode

让我们配置 Webpack。我们将需要两个 Webpack 配置，一个用于
Node.js 服务器代码，另一个用于客户端代码。如果你想看我们的应用程序的结构，请
参考[资源库](https://github.com/MarvelousWololo/ssr-react)。另外，请注意:

1.  我用的是 [ES2015 预设](https://babeljs.io/docs/en/babel-preset-es2015.html)而不是新的[环境预设](https://babeljs.io/docs/en/babel-preset-env)，如果你愿意，你可以自己更改。
2.  我还包含了[transform-class-properties](https://babeljs.io/docs/en/babel-plugin-transform-class-properties)Babel 插件，这样我就不需要到处使用我的类方法了。要不要看你自己，不过默认在 [CRA](https://github.com/facebook/create-react-app) 上。

因为我对服务器和客户端使用相同的模块规则，我将把它们提取到一个变量`js` :

```
// webpack.config.js
const js = {
  test: /\.js$/,
  exclude: /node_modules/,
  use: {
    loader: "babel-loader",
    options: {
      presets: ["@babel/preset-env", "@babel/preset-react"],
    },
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在这两种配置中，我使用了不同的目标。

在服务器配置上，在我之前尝试进行服务器端渲染时，有两个细节我错过了，这样做我甚至无法构建我的应用程序: [`node.__dirname`](https://webpack.js.org/configuration/node/#node) 属性和 Webpack 插件
[web pack-node-externals](https://github.com/liady/webpack-node-externals)的使用
。

在第一种情况下，我已经将`__dirname`设置为 false，因此当 Webpack 编译我们的服务器代码时，它不会提供 polyfill，并将保持
`__dirname`的原始值，当我们使用
Express 提供静态资产时，这种配置很有用，如果我们不将其设置为`false` Express，将无法找到 [`__dirname`](https://nodejs.org/docs/latest/api/modules.html#modules_dirname) 的
引用。

使用`webpack-node-externals`是为了让 Webpack 忽略`node_modules`、
的内容，否则，它将在最终的包中包含整个目录。(我不
确定为什么这不是默认行为，我们需要一个外部库来实现。
我的理解是，如果您将[配置目标设置为
节点](https://webpack.js.org/concepts/targets/#usage)，它应该将
`node_modules`排除在捆绑包之外。)

**注意**:在这两种情况下，我发现文档确实令人困惑，所以请不要相信我的话，如果有进一步的问题，请自己检查文档。

```
// webpack.config.js
const serverConfig = {
  mode: "development",
  target: "node",
  node: {
    __dirname: false,
  },
  externals: [nodeExternals()],
  entry: {
    "index.js": path.resolve(__dirname, "src/index.js"),
  },
  module: {
    rules: [js],
  },
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "[name]",
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

还有我们的客户端配置:

```
// webpack.config.js
const clientConfig = {
  mode: "development",
  target: "web",
  entry: {
    "home.js": path.resolve(__dirname, "src/public/home.js"),
  },
  module: {
    rules: [js],
  },
  output: {
    path: path.resolve(__dirname, "dist/public"),
    filename: "[name]",
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将导出两种配置:

```
// webpack.config.js
module.exports = [serverConfig, clientConfig]; 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里找到最终文件

## 先服务器端渲染

现在我们将创建一个组件，并将它挂载到 DOM:

```
// src/public/components/Hello.js
import React from "react";

const Hello = (props) => (
  <React.Fragment>
    <h1>Hello, {props.name}!</h1>
  </React.Fragment> );

export default Hello; 
```

Enter fullscreen mode Exit fullscreen mode

这是将我们的组件挂载到 DOM 中的文件，注意我们是使用`react-dom`的`hydrate`方法的
，而不是通常的`render`。

```
// src/public/home.js
import React from "react";
import ReactDOM from "react-dom";
import Hello from "./components/Hello";

ReactDOM.hydrate(
  <Hello name={window.__INITIAL__DATA__.name} />,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以编写我们的服务器代码:

```
// src/index.js
import express from "express";
import path from "path";
import React from "react";
import ReactDOMServer from "react-dom/server";
import Hello from "./public/components/Hello";

const app = express();

app.use("/static", express.static(path.resolve(__dirname, "public")));

app.get("/", (req, res) => {
  const name = "Marvelous Wololo";

  const component = ReactDOMServer.renderToString(<Hello name={name} />); 
  const html = `
  <!doctype html>
    <html>
    <head>
      <script>window.__INITIAL__DATA__ = ${JSON.stringify({ name })}</script>
    </head>
    <body>
    <div id="root">${component}</div>
    <script src="/static/home.js"></script>
  </body>
  </html>`;

  res.send(html);
});

app.listen(3000); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们正在对`name`的内容进行字符串化，这样我们就可以在客户端
上重用它的值来合并我们的组件。

然后我们将创建一个 NPM 脚本来运行我们的项目:

```
//  package.json  "scripts":  {  "dev":  "webpack && concurrently \"webpack --watch\"  \"nodemon dist\""  } 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们正在构建
[`concurrently`](https://github.com/kimmobrunfeldt/concurrently) ，观察我们的包中的
变化，并从`/dist`运行我们的服务器。如果我们在没有
第一次构建的情况下启动我们的应用程序，命令将会崩溃，因为`/dist`中还没有文件。

如果您在终端中使用`npm run dev`，您的应用程序将在`localhost:3000`可用。

## 切换到流

现在我们将切换到流 API 以提高我们的性能，如果您
不知道什么是流，您可以在这里
和
阅读更多关于它们的信息，在这里更具体地反应
[。](https://reactjs.org/docs/react-dom-server.html#rendertonodestream)

这是我们新的`/`路线:

```
app.get("/", (req, res) => {
  const name = "Marvelous Wololo";

  const componentStream = ReactDOMServer.renderToNodeStream(
    <Hello name={name} />
  );

  const htmlStart = `
  <!doctype html>
    <html>
    <head>
      <script>window.__INITIAL__DATA__ = ${JSON.stringify({ name })}</script>
    </head>
    <body>
    <div id="root">`;

  res.write(htmlStart);

  componentStream.pipe(res, { end: false });

  const htmlEnd = `</div>
    <script src="/static/home.js"></script>
  </body>
  </html>`;

  componentStream.on("end", () => {
    res.write(htmlEnd);

    res.end();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 将 Express 路由器与 React 路由器结合

我们可以使用带有 React 路由器库的快速路由器。

安装 React 路由器:

```
npm i -E react-router-dom 
```

Enter fullscreen mode Exit fullscreen mode

首先我们需要在`clientConfig` :
中添加一个新的 Webpack 条目

```
// webpack.config.js
  entry: {
    'home.js': path.resolve(__dirname, 'src/public/home.js'),
    'multipleRoutes.js': path.resolve(__dirname, 'src/public/multipleRoutes.js')
  } 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们像对`Home`一样创建两个组件。第一个将是几乎与 React 路由器
文档中的[基本示例相同的
，姑且称之为`MultipleRoutes` :](https://reacttraining.com/react-router/web/example/basic) 

```
// src/public/components/MultipleRoutes.js
import React from "react";
import { Link, Route } from "react-router-dom";

const Home = () => (
  <div>
    <h2>Home</h2>
  </div> );

const About = () => (
  <div>
    <h2>About</h2>
  </div> );

const Topics = ({ match }) => (
  <div>
    <h2>Topics</h2>
    <ul>
      <li>
        <Link to={`${match.url}/rendering`}>Rendering with React</Link>
      </li>
      <li>
        <Link to={`${match.url}/components`}>Components</Link>
      </li>
      <li>
        <Link to={`${match.url}/props-v-state`}>Props v. State</Link>
      </li>
    </ul> 
    <Route path={`${match.url}/:topicId`} component={Topic} />
    <Route
      exact
      path={match.url}
      render={() => <h3>Please select a topic.</h3>}
    />
  </div> );

const Topic = ({ match }) => (
  <div>
    <h3>{match.params.topicId}</h3>
  </div> );

const MultipleRoutes = () => (
  <div>
    <ul>
      <li>
        <Link to="/with-react-router">Home</Link>
      </li>
      <li>
        <Link to="/with-react-router/about">About</Link>
      </li>
      <li>
        <Link to="/with-react-router/topics">Topics</Link>
      </li>
      <li>
        <a href="/">return to server</a>
      </li>
    </ul> 
    <hr />

    <Route exact path="/with-react-router" component={Home} />
    <Route path="/with-react-router/about" component={About} />
    <Route path="/with-react-router/topics" component={Topics} />
  </div> );

export default MultipleRoutes; 
```

Enter fullscreen mode Exit fullscreen mode

和

```
// src/public/multipleRoutes.js
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router } from "react-router-dom";
import MultipleRoutes from "./components/MultipleRoutes";

const BasicExample = () => (
  <Router>
    <MultipleRoutes />
  </Router> );

ReactDOM.hydrate(<BasicExample />, document.getElementById("root")); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的服务器中，我们将导入新组件和 React 路由器
库。我们还将创建一个通配符路由`/with-react-router*`，因此每个
到`/with-react-router`的请求都将在这里处理。例如:`/with-react-router/one`、
、`/with-react-router/two`、`/with-react-router/three`。

```
// src/index.js
// ...
import { StaticRouter as Router } from "react-router-dom";
import MultipleRoutes from "./public/components/MultipleRoutes";
// ...
app.get("/with-react-router*", (req, res) => {
  const context = {};

  const component = ReactDOMServer.renderToString(
    <Router location={req.url} context={context}>
      <MultipleRoutes />
    </Router>
  );

  const html = `
  <!doctype html>
    <html>
    <head>
      document
    </head>
    <body>
      <div id="root">${component}</div>
      <script src="/static/multipleRoutes.js"></script>
    </body>
    </html>
  `;

  if (context.url) {
    res.writeHead(301, { Location: context.url });
    res.end();
  } else {
    res.send(html);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

**注意**我们在
客户端和服务器端使用了与`react-router-dom`不同的路由器。

到目前为止，你必须有一个应用程序，客户端和服务器渲染路线。为了改进
的导航，我们将在
`Hello`组件中添加一个到`/with-react-router`的链接:

```
// src/public/components/Hello.js
// ...
const Hello = (props) => (
  <React.Fragment>
    <h1>Hello, {props.name}!</h1> 
    <a href="/with-react-router">with React Router</a>
  </React.Fragment> ); 
```

Enter fullscreen mode Exit fullscreen mode

## 使用快速查询字符串

因为我们已经用 Express 设置了一个完整的 Node.js 应用程序，所以我们可以访问 Node 必须提供的所有
东西。为了展示这一点，我们将通过我们的`/`路径中的查询字符串:
接收
组件的属性`name`

```
// src/index.js
app.get('/', (req, res) => {
  const { name = 'Marvelous Wololo' } = req.query
// ... 
```

Enter fullscreen mode Exit fullscreen mode

这里我们为变量`name`定义一个默认值，如果`req.query`没有为我们提供一个默认值的话。因此，`Hello`组件将在`localhost:3000?name=anything-I-want-here`呈现您为`name`传递的任何值

## 创建测试环境

为了测试我们的 React 组件，我们将首先安装一些依赖项。我选择了 Mocha 和 Chai 来运行和断言我们的测试，但是您可以使用任何其他的测试运行器/断言库。测试这个环境的缺点是
我们还必须编译测试文件(我不确定是否有其他的
方法可以绕过它，我想没有)。

```
npm i -DE mocha chai react-addons-test-utils enzyme enzyme-adapter-react-16 
```

Enter fullscreen mode Exit fullscreen mode

因此，我将为测试创建一个新的 Webpack 配置，您会注意到该配置几乎与我们已经拥有的服务器文件
完全相同

```
// webpack.tests.js
const webpack = require("webpack");
const nodeExternals = require("webpack-node-externals");
const path = require("path");

const js = {
  test: /\.js$/,
  exclude: /node_modules/,
  use: {
    loader: "babel-loader",
    options: {
      presets: ["@babel/preset-env", "@babel/preset-react"],
    },
  },
};

module.exports = {
  mode: "development",
  target: "node",
  node: {
    __dirname: false,
  },
  externals: [nodeExternals()],
  entry: {
    "app.spec.js": path.resolve(__dirname, "specs/app.spec.js"),
  },
  module: {
    rules: [js],
  },
  output: {
    path: path.resolve(__dirname, "test"),
    filename: "[name]",
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

我将在
项目的根目录下创建一个测试文件`app.spec.js`和一个`specs`目录。

```
// specs/app.spec.js
import { expect } from "chai";
import Enzyme, { shallow } from "enzyme";
import Adapter from "enzyme-adapter-react-16";
import React from "react";
import Hello from "../public/components/Hello";

Enzyme.configure({ adapter: new Adapter() });

describe("<Hello />", () => {
  it("renders <Hello />", () => {
    const wrapper = shallow(<Hello name="tests" />);
    const actual = wrapper.find("h1").text();
    const expected = "Hello, tests!";

    expect(actual).to.be.equal(expected);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们还将创建一个新的(长而难看的)NPM 脚本来运行我们的测试:

```
"scripts":  {  "dev":  "webpack && concurrently \"webpack --watch\"  \"nodemon dist\"",  "test":  "webpack --config webpack.test.js && concurrently \"webpack --config webpack.test.js --watch\"  \"mocha --watch\""  } 
```

Enter fullscreen mode Exit fullscreen mode

此时，运行`npm test`应该通过一个测试用例。

## (尝试)代码拆分

老实说，我认为用 Webpack 进行代码分割的新方法有点难以理解，但我还是会尝试一下。请记住，这是
不是最终的解决方案，你可能会想用 Webpack 来提取最好的
，但我现在不愿意为此浏览文档。我在这里得到的
结果对我来说已经足够好了。抱歉。在
问题的情况下，转至[文档](https://webpack.js.org/guides/code-splitting/)。

所以，如果我们加上:

```
// webpack.config.js
// ...
optimization: {
  splitChunks: {
    chunks: "all";
  }
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的`clientConfig`，Webpack 将把我们的代码分成四个文件:

*   home.js
*   多个路由. js
*   vendors ~ home . js ~ multiple routes . js
*   vendors~multipleRoutes.js

当我们运行`npm run dev`时，它甚至给我们一个很好的报告。我认为这些文件是
不言自明的，但我们仍然有一些文件是给定的
页面专用的，还有一些文件有共同的供应商代码，在
页面之间共享。所以我们在`/`路线底部的脚本标签应该是:

```
<script src="/static/vendors~home.js~multipleRoutes.js"></script>
<script src="/static/home.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

对于`/with-react-router`路线:

```
<script src="/static/vendors~home.js~multipleRoutes.js"></script>
<script src="/static/vendors~multipleRoutes.js"></script>
<script src="/static/multipleRoutes.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

如果你好奇的话，下面是当你将
配置模式设置为`production` :
时，包大小的差异

```
 Asset      Size
                          home.js  1.82 KiB
                multipleRoutes.js  3.27 KiB
        vendors~multipleRoutes.js  24.9 KiB
vendors~home.js~multipleRoutes.js  127 KiB 
```

Enter fullscreen mode Exit fullscreen mode

和`development` :

```
 Asset      Size
                          home.js  8.79 KiB
                multipleRoutes.js  13.6 KiB
        vendors~multipleRoutes.js   147 KiB
vendors~home.js~multipleRoutes.js   971 KiB 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我想就是这样了。我希望你喜欢这个小教程，也希望它对你自己的项目有用。
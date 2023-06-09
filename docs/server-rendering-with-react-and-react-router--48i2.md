# 使用 React 和 React 路由器 V4 的服务器渲染

> 原文：<https://dev.to/tylermcginnis/server-rendering-with-react-and-react-router--48i2>

服务器端渲染 React 应用程序可以提供一些不同的好处，包括性能和 SEO。问题是，在获得这些好处的同时，您的应用程序也会变得更加复杂。在这篇文章中，我们将从头开始，慢慢地构建一个服务器端渲染 React(用 React 路由器),同时分解一些复杂性。

### 视频

[https://www.youtube.com/embed/mZEv4mHsU5E](https://www.youtube.com/embed/mZEv4mHsU5E)

### 岗位

服务器端渲染又名同构 JavaScript 又名通用 JavaScript 是在服务器和客户机上运行相同 JavaScript 代码的~~白日梦~~想法。为什么这是有益的？嗯，您通常会从代码重用、性能提高和 SEO 收益中受益。更恰当的问题是，你获得的好处值得你增加的复杂性吗？当构建服务器渲染的应用程序时，您需要考虑更多的情况。**哪个代码会被共享？** **有没有需要共享的初始状态？** **如何在服务器端和客户端都处理路由？因为所有这些问题都可以线性地回答，我们将在这篇文章中采用同样的方法。我们将从最基本的开始，解决它，然后增加更多的复杂性。最后，您将能够决定服务器渲染的复杂性权衡对于您的特定应用程序是否值得。**

如果对你来说这是一个新概念，那么在深入细节之前，抓住所有部分如何组合在一起的大局是很重要的。

这是(初始)过程

1)用户在他们的 web 浏览器中键入您的 URL，然后点击 enter。

2)您的服务器发现存在对路径“/”的 GET 请求。

3)它呈现应用程序的主要组件，并将其包装在标准的 html 文档(DOCTYPE、HTML、head、body 等)中，然后将整个内容作为响应发送回来。

[![SSR response](img/d7fa9380a71d0869f9a0f1c7375dd457.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LPFVOgBg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tylermcginnis.com/static/891375429d05b32d5742de8078102a0d-77d4f.png)

4)浏览器看到它从服务器获得了一个 HTML 文档，并且它的[渲染引擎开始工作](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)。它很快就完成了页面的呈现。

5)此时，**页面可见**，浏览器开始下载任何脚本。

[![Download waterfall](img/c0025993ea7cd8960a641cfeca3d8df7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fW9qeV1U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tylermcginnis.com/static/90cd5d58ebc514b289ec0db73f6a4fa4-fed13.png)

6)一旦脚本被下载，React 接管并且页面是交互式的。

请注意，使用服务器呈现，浏览器从服务器获得的响应是准备好呈现的页面的 HTML。这与客户端渲染有很大的不同，客户端渲染只是返回一个带有巨大 JS 包的空白 HTML 文档。

通过发送回一个完成的 HTML 文档，浏览器能够向用户显示一些 UI，而不必等待 JavaScript 完成下载。

* * *

现在我们已经有了大致的了解，让我们开始为服务器渲染的 React 路由器应用程序创建基础。

分解我们的列表，我们知道有三样东西我们需要预先准备好。

1)一个 React 组件——目前甚至只是一个呈现“Hello World”的基本组件。2)一个服务器，它将我们的基本 React 组件封装在某种 HTML 结构中，然后返回给我们。3)一个 React 应用程序，它将从服务器呈现 HTML 的地方开始，并在需要的地方向现有标记添加任何事件侦听器。

> 这里需要注意的是，出于 React 的考虑，您在服务器上呈现的内容(上面的#2)需要与在客户机上呈现的内容(上面的#3)相同。如果没有，React 将抛出一个警告。

和往常一样，在处理 React 时，我们将需要在某个时候讨论 webpack。我们不会使用 Create React 应用程序，因此我们必须推出自己的配置。为了使本教程尽可能集中，我将粘贴 webpack.config.js 文件和下面的 package.json，然后突出显示重要的部分。

```
// webpack.config.js

var path = require('path')
var webpack = require('webpack')
var nodeExternals = require('webpack-node-externals')

var browserConfig = {
  entry: './src/browser/index.js',
  output: {
    path: path.resolve(__dirname, 'public'),
    filename: 'bundle.js',
    publicPath: '/'
  },
  module: {
    rules: [
      { test: /\.(js)$/, use: 'babel-loader' },
    ]
  },
  plugins: [
    new webpack.DefinePlugin({
      __isBrowser__: "true"
    })
  ]
}

var serverConfig = {
  entry: './src/server/index.js',
  target: 'node',
  externals: [nodeExternals()],
  output: {
    path: __dirname,
    filename: 'server.js',
    publicPath: '/'
  },
  module: {
    rules: [
      { test: /\.(js)$/, use: 'babel-loader' }
    ]
  },
  plugins: [
    new webpack.DefinePlugin({
      __isBrowser__: "false"
    })
  ]
}

module.exports = [browserConfig, serverConfig] 
```

注意我们有两个不同的配置:一个用于浏览器，一个用于服务器。

**浏览器配置**将获取位于`/src/browser/index.js`的代码，通过`babel-loader`运行它(它将通过`env`和`react`预置运行它)，然后在`/public/bundle.js`抛出修改后的捆绑代码。`__isBrowser__`行将向全局名称空间添加一个属性(`__isBrowser__`)，这样我们就知道我们正在浏览器上进行渲染。

**服务器配置**类似。它将获取位于`/src/server/index.js`的代码，通过同一个`babel-loader`运行它，然后它将在`./server.js`将其拆分。`externals`线使它不与服务器`node_modules`捆绑在一起。`target`告诉 webpack 在“类似 Node.js 的环境”中进行编译，并帮助`externals`知道忽略什么(内置在 path、fs 等节点模块中)。

TL；最终的客户端代码将放在`public/bundle.js`处，而最终的服务器代码将放在根位置`server.js`处。

```
// package.json

{
  "name": "rrssr",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "webpack -w & nodemon server.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "MIT",
  "description": "",
  "babel": {
    "presets": [
      "env",
      "react"
    ],
    "plugins": [
      "transform-object-rest-spread"
    ]
  },
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-plugin-transform-object-rest-spread": "^6.26.0",
    "babel-preset-env": "^1.6.1",
    "babel-preset-react": "^6.24.1",
    "nodemon": "^1.12.5",
    "webpack": "^3.10.0",
    "webpack-node-externals": "^1.6.0"
  },
  "dependencies": {
    "cors": "^2.8.4",
    "express": "^4.16.2",
    "isomorphic-fetch": "^2.2.1",
    "react": "^16.2.0",
    "react-dom": "^16.2.0",
    "react-router-dom": "^4.2.2",
    "serialize-javascript": "^1.4.0"
  }
} 
```

当我们在命令行中运行`npm run start`时，将会运行`webpack -w`和`nodemon server.js`。`webpack -w`将观察我们的代码，并在代码发生变化时重新编译，而`nodemon server.js`将在我们的服务器代码发生变化时重新启动我们的服务器。

* * *

现在，让我们开始工作。根据我们的`webpack.config.js`文件，在我们的`src`文件夹中，我们将有一个`server`和一个`browser`文件夹。让我们也为两者共享的所有功能添加一个`shared`文件夹。

```
webpack.config.js
package.json
/src
  /browser
  /server
  /shared 
```

如果你还记得，当我们分解最初的 SSR 流程时，我们首先需要三个项目。

1)一个 React 组件——目前甚至只是一个呈现“Hello World”的基本组件。2)一个服务器，它将我们的基本 React 组件封装在某种 HTML 结构中，然后返回给我们。3)一个 React 应用程序，它将从服务器呈现 HTML 的地方开始，并在需要的地方向现有标记添加任何事件侦听器。

我们可以很容易地处理#1。让我们在`shared`文件夹中创建一个 App.js 组件，并让它呈现“Hello World”。

```
// src/shared/App.js

import React, { Component } from 'react'

class App extends Component {
  render() {
    return (
      <div>
        Hello World
      </div>
    )
  }
}

export default App 
```

完了，完了。现在，来看第二个问题。

> # 2——一个服务器，它将我们的基本 React 组件封装在某个 HTML 结构中后返回。

首先，让我们在`src/server`文件夹中创建一个`index.js`文件。我们将使用 express，因此让我们先进行基本设置。

```
import express from "express"
import cors from "cors"

const app = express()

app.use(cors())

// We're going to serve up the public
// folder since that's where our
// client bundle.js file will end up.
app.use(express.static("public"))

app.listen(3000, () => {
  console.log(`Server is listening on port: 3000`)
}) 
```

现在，我们希望这样做，每当我们的服务器接收到一个`GET`请求时，我们就把 HTML 框架连同它里面的`App`组件的标记一起发送回去。为此，我们将使用 React 的`renderToString`方法。它接受一个 React 元素并返回一个 HTML 字符串。

```
import express from "express"
import cors from "cors"
import { renderToString } from "react-dom/server"
import App from '../shared/App'
import React from 'react'

const app = express()

app.use(cors())

// We're going to serve up the public
// folder since that's where our
// client bundle.js file will end up.
app.use(express.static("public"))

app.get("*", (req, res, next) => {
  const markup = renderToString(
    <App />
  )

  res.send(`
    <!DOCTYPE html>
    <html>
      <head>
        SSR with RR
      </head>

      <body>
        <div id="app">${markup}</div>
      </body>
    </html>
  `)
})

app.listen(3000, () => {
  console.log(`Server is listening on port: 3000`)
}) 
```

最后，我们还希望包含一个`<script src='/bundle.js'></script>`标签，因为当浏览器解析这个 HTML 文件时，我们希望它获取包含所有客户端代码的`bundle.js`文件。

```
<head>
  SSR with RR
  <script src="/bundle.js" defer></script>
</head> 
```

现在，无论何时向我们的服务器发出 GET 请求，我们都会得到一些 HTML，其中包括我们的`<App />`组件和一个到我们的`bundle.js`文件的链接。

> #3.React 应用程序将从服务器呈现 HTML 的地方继续前进，并在需要的地方向现有标记添加任何事件侦听器。

这个听起来比实际更难。通常，当您想要告诉浏览器您的 React 应用程序时，您调用`ReactDOM.render`并向其传递您想要挂载的元素和 DOM 节点。我们需要做的服务器渲染是类似的，但不是调用`ReactDOM.render`，我们想调用`ReactDOM.hydrate`。`.hydrate`要做的是告诉 React 您已经在服务器上创建了标记，而不是在客户机上重新创建它，它应该保存它并只将任何需要的事件处理程序附加到现有的服务器呈现的标记上。

让我们在`src/browser`中创建一个新的`index.js`文件，并在那里调用`hydrate`。

```
// src/browser/index.js

import React from 'react'
import { hydrate } from 'react-dom'
import App from '../shared/App'

hydrate(
  <App />,
  document.getElementById('app')
); 
```

此时，假设您已经在终端中运行了`npm run start`，当您访问`localhost:3000`时，您应该会看到“Hello World”。“Hello World”最初在服务器上呈现，然后当它到达客户端并加载了`bundle.js`文件时，React 接管了它。

酷毙了。还有，虎头蛇尾。

让我们把事情混合起来，这样我们就能真正看到这是如何工作的。如果我们想让`App`渲染`Hello {this.props.data}`而不是渲染“Hello World”呢？那是一个足够简单的`App.js`内部的变化

```
class App extends Component {
  render() {
    return (
      <div>
        Hello {this.props.data}
      </div>
    )
  }
} 
```

现在，每当我们创建我们的`App`元素时，我们需要向它传递一个`data` prop - React 101。

我们在哪里创建`App`元素？有两个地方。第一个位置在`server/index.js`内部，用于我们服务器渲染的时候，第二个位置在`browser/index.js`内部，用于浏览器获取它的时候。让我们修改这两个并添加一个`Tyler`的`data`道具。

```
// browser/index.js

hydrate(
  <App data='Tyler' />,
  document.getElementById('app')
); 
```

```
// server/index.js

const markup = renderToString(
  <App data='Tyler' />
) 
```

太好了。现在我们在用户界面上看到了“你好，泰勒”。还记得我前面提到的，你在服务器上渲染的内容需要和在客户端渲染的内容一致吗？如果我们改变其中一个数据属性，就可以看到这一点。

```
hydrate(
  <App data='Mikenzi' />,
  document.getElementById('app')
); 
```

现在，当你刷新应用程序时，你最初会看到“Hello Tyler”(这是服务器上呈现的内容)，然后当 React 接管时，你会看到“Hello Mikenzi”。在控制台中，您会看到一个警告`Text content did not match. Server: "Tyler" Client: "Mikenzi"`。

以下是 React 医生对此的看法

> React 期望呈现的内容在服务器和客户机之间是相同的。它可以修补文本内容中的差异，但是您应该将不匹配视为错误并修复它们。在开发模式下，React 会在水合过程中发出不匹配警告。不保证在不匹配的情况下会修补属性差异。出于性能原因，这一点很重要，因为在大多数应用程序中，不匹配的情况很少发生，因此验证所有标记会非常昂贵。

当您只是呈现一个没有数据的组件时，让服务器呈现的内容和客户端呈现的内容完全相同并不困难——就像我们刚刚呈现`<App />`时看到的那样。当你添加数据时，事情变得有点复杂。您需要确保在客户机和服务器上用相同的数据(或道具)呈现组件。让我们看看我们是如何做到这一点的(不在服务器和客户机上硬编码`data` prop)。

我们知道，由于应用程序将首先在服务器上呈现，我们的应用程序需要的任何初始数据都必须来自服务器。考虑到这一点，为了确保服务器和客户端是相同的，我们需要弄清楚如何将来自服务器的相同数据传送到客户端。嗯，有一个非常“老派”的解决方案，非常有效。让我们把它放在全局名称空间上，这样客户机就可以引用它。

```
...

import serialize from "serialize-javascript"

app.get("*", (req, res, next) => {
  const name = 'Tyler'
  const markup = renderToString(
    <App data={name}/>
  )

  res.send(`
    <!DOCTYPE html>
    <html>
      <head>
        SSR with RR
        <script src="/bundle.js" defer></script>
        <script>window.__INITIAL_DATA__ = ${serialize(name)}</script>
      </head>

      <body>
        <div id="app">${markup}</div>
      </body>
    </html>
  `)
}) 
```

现在，在客户端，我们可以从`window.__INITIAL_DATA__`获取名称。

```
hydrate(
  <App data={window.__INITIAL_DATA__} />,
  document.getElementById('app')
); 
```

🕺:我们已经通过使用`window`对象解决了从服务器到客户端共享初始数据的问题。

* * *

现在让我们实际上开始建立一些实质性的东西。很可能你永远不会有静态的初始数据。您的数据很可能来自某个地方的 API。让我们修改我们的服务器，使它在返回 HTML 之前获取一些数据。最终目标是建造像这样的东西。我们将使用 Github API 来获取特定语言的流行库。我们将在没有任何路由的情况下开始，然后我们将看看如何使用 React 路由器将其添加进来。

我们要做的第一件事是创建一个函数，它接受一种语言，并使用 Github API 获取该语言最流行的 repos。因为我们将在服务器和客户机上都使用这个函数，所以让我们在`shared`文件夹中创建一个`api.js`文件，并调用函数`fetchPopularRepos`。

```
// shared/api.js

import fetch from 'isomorphic-fetch'

export function fetchPopularRepos (language = 'all') {
  const encodedURI = encodeURI(`https://api.github.com/search/repositories?q=stars:>1+language:${language}&sort=stars&order=desc&type=Repositories`)

  return fetch(encodedURI)
    .then((data) => data.json())
    .then((repos) => repos.items)
    .catch((error) => {
      console.warn(error)
      return null
    });
} 
```

现在我们需要确定何时调用这个函数。这个想法是，当对我们的服务器发出 GET 请求时，我们不是立即调用`renderToString`,而是首先获取流行的存储库，然后在向 React 应用程序提供数据后调用它。

```
// server/index.js

...

import { fetchPopularRepos } from '../shared/api'

app.get("*", (req, res, next) => {
  fetchPopularRepos()
    .then((data) => {
      const markup = renderToString(
        <App data={data} />
      )

      res.send(`
        <!DOCTYPE html>
        <html>
          <head>
            SSR with RR
            <script src="/bundle.js" defer></script>
            <script>window.__INITIAL_DATA__ = ${serialize(data)}</script>
          </head>

          <body>
            <div id="app">${markup}</div>
          </body>
        </html>
      `)
    })
}) 
```

现在，当发出请求时，我们正在获取我们需要的数据，但是我们也希望修改`App`组件，以便能够正确地处理新数据。让我们制作一个名为`Grid`的新组件来处理所有回购的映射，而不是在`App`中处理它。

```
// shared/Grid.js
import React, { Component } from 'react'

class Grid extends Component {
  render() {
    const repos = this.props.data

    return (
      <ul style={{display: 'flex', flexWrap: 'wrap'}}>
        {repos.map(({ name, owner, stargazers_count, html_url }) => (
          <li key={name} style={{margin: 30}}>
            <ul>
              <li><a href={html_url}>{name}</a></li>
              <li>@{owner.login}</li>
              <li>{stargazers_count} stars</li>
            </ul>
          </li>
        ))}
      </ul>
    )
  }
}

export default Grid 
```

```
// shared/App.js
import React, { Component } from 'react'
import Grid from './Grid'

class App extends Component {
  render() {
    return (
      <div>
        <Grid data={this.props.data} />
      </div>
    )
  }
} 
```

坚实。现在，当我们的应用程序被请求时，服务器获取应用程序需要的数据，我们得到的 HTML 响应包含了初始 UI 所需的所有内容。

* * *

在这一点上，我们已经做了很多，但我们的应用程序还有很长的路要走，尤其是在路由方面。

React 路由器是一种声明式的、基于组件的路由方法。然而，当我们用 React Router 处理服务器端渲染时，我们需要放弃这种范式，将所有路由移动到一个[中央路由配置](https://tylermcginnis.com/react-router-route-config/)。这样做的原因是因为客户端和服务器都需要知道我们的路由。客户端，因为它显然需要知道当用户在我们的应用程序中导航时要呈现哪些组件；服务器，因为它需要知道当用户请求特定路径时要获取哪些数据。

现在让我们创建中心路由配置。在我们的`shared`文件夹中创建一个名为`routes.js`的新文件。我们将用一组对象来表示我们的路线。每个对象代表一条新路线。最后，我们将映射我们的 routes 数组，并为每个项目创建一个`<Route>`。在我们的应用程序中，我们将有两条路线- `/`和`/popular/:id`。`/`将呈现(即将创建的)`Home`组件，而`/popular/:id`将呈现我们的`Grid`组件。

```
// shared/routes.js
import Home from './Home'
import Grid from './Grid'

const routes =  [
  {
    path: '/',
    exact: true,
    component: Home,
  },
  {
    path: '/popular/:id',
    component: Grid,
  }
]

export default routes 
```

在我们继续之前，让我们赶紧创建`Home`组件。

```
// shared/Home.js
import React from 'react'

export default function Home () {
  return (
    <div>
      Select a Language
    </div>
  )
} 
```

我前面提到过，服务器需要访问中央路由配置的原因是“它需要知道当用户请求特定路径时要获取哪些数据”。这意味着我们将把特定路由需要的任何数据请求放在路由对象本身中。这将允许服务器说“看起来用户正在请求`/popular/javascript`路线。在我们发送回响应之前，有没有需要获取的数据？有吗？好的，拿来。”。

```
// shared/routes.js
import Home from './Home'
import Grid from './Grid'
import { fetchPopularRepos } from './api'

const routes =  [
  {
    path: '/',
    exact: true,
    component: Home,
  },
  {
    path: '/popular/:id',
    component: Grid,
    fetchInitialData: (path = '') => fetchPopularRepos(
      path.split('/').pop()
    )
  }
]

export default routes 
```

同样，通过向我们的`/popular/:id`路由添加一个`fetchInitialData`属性，当用户从服务器发出一个带有该路径的`GET`请求时，我们将继续调用`fetchInitialData`并向其传递该路径，我们将得到一个承诺，该承诺最终将解析我们需要呈现的数据。

让我们回到我们的服务器，看看这些变化会是什么样子。

我们需要做的第一件事是找出哪个路由(如果有的话)匹配当前请求的服务器 URL。例如，如果用户请求`/`页面，我们需要找到匹配`/`的路线。幸运的是，React Router 导出了一个`matchPath`函数，它在内部使用这个函数来匹配位置和路线。

```
// server/index.js
...
import { matchPath } from "react-router-dom"
import routes from '../shared/routes'

app.get("*", (req, res, next) => {
  const activeRoute = routes.find(
    (route) => matchPath(req.url, route)
  ) || {}

})

... 
```

现在，`activeRoute`将是用户请求的任何页面的路径(`req.url`)。

下一步是查看该路由是否需要任何数据。我们将检查`activeRoute`是否有一个`fetchInitialData`属性。如果是，我们将调用它并把当前路径传递给它，如果不是，我们将继续。

```
app.get("*", (req, res, next) => {
  const activeRoute = routes.find((route) => matchPath(req.url, route)) || {}

  const promise = activeRoute.fetchInitialData
    ? activeRoute.fetchInitialData(req.path)
    : Promise.resolve()

  promise.then((data) => {

  }).catch(next)
}) 
```

现在我们有了一个承诺，它将通过数据来解决，或者什么都不用做。正如我们之前所做的，我们希望获取它，并将其传递给我们的组件，同时将其放在窗口对象上，以便客户端稍后可以获取它。

```
app.get("*", (req, res, next) => {
  const activeRoute = routes.find((route) => matchPath(req.url, route)) || {}

  const promise = activeRoute.fetchInitialData
    ? activeRoute.fetchInitialData(req.path)
    : Promise.resolve()

  promise.then((data) => {
    const markup = renderToString(
      <App data={data} />
    )

    res.send(`
      <!DOCTYPE html>
      <html>
        <head>
          SSR with RR
          <script src="/bundle.js" defer></script>
          <script>window.__INITIAL_DATA__ = ${serialize(data)}</script>
        </head>

        <body>
          <div id="app">${markup}</div>
        </body>
      </html>
    `)
  }).catch(next)
}) 
```

越来越近。现在，我们不再总是获取流行的回购，而是只在被渲染的路线具有`fetchInitialData`属性时才获取它们。这意味着只有当用户请求匹配`/popular/:id`的路径时，我们才会获取数据。

在您的浏览器中尝试一下。前往`localhost:3000/popular/javascript`。您会注意到最受欢迎的 JavaScript repos 正在被请求。您可以将语言更改为 github API 支持的任何语言，您将获得该语言最受欢迎的回复。这样做的原因是因为在我们的 routes 数组中，我们要将`req.path`传递到`fetchInitialData`。然后它解析路径中的语言，然后用该语言调用`fetchPopularRepos`。

```
// shared/routes.js
  {
    path: '/popular/:id',
    component: Grid,
    fetchInitialData: (path = '') =>
      fetchPopularRepos(path.split('/').pop())
  } 
```

* * *

既然我们已经根据用户请求的路由在服务器上获取了正确的数据，那么让我们也添加一些客户端路由。

和往常一样，我们需要将我们的主组件(`App`)包装在客户端 React Router 的`BrowserRouter`组件中。我们将在`browser/index.js`内部进行，因为那里是我们渲染`App`的地方。

```
import React from 'react'
import { hydrate } from 'react-dom'
import App from '../shared/App'
import { BrowserRouter } from 'react-router-dom'

hydrate(
  <BrowserRouter>
    <App data={window.__INITIAL_DATA__} />
  </BrowserRouter>,
  document.getElementById('app')
); 
```

现在，因为我们已经将对客户机的控制交给了 React 路由器，所以我们还需要在服务器上做同样的事情，以便它们匹配。因为我们在服务器上，渲染一个叫`BrowserRouter`的组件没有意义。相反，我们将使用 React 路由器的`StaticRouter`组件。它被称为`StaticRouter`,因为位置实际上从未改变。它需要两个道具:`location`和`context`。`location`是用户请求的当前位置(`req.url`),`context`需要是一个可以包含关于渲染的任何信息的对象——我们现在将使用一个空白的上下文对象。

```
// server/index.js
...
import { StaticRouter, matchPath } from "react-router-dom"
...

const markup = renderToString(
  <StaticRouter location={req.url} context={{}}>
    <App data={data}/>
  </StaticRouter> )

... 
```

现在，让我们渲染一些客户端路线。我们已经有了我们的`routes`数组，所以我们只需要映射它。一个警告是，我们还希望[将 React 路由器](https://tylermcginnis.com/react-router-pass-props-to-components/)呈现的组件传递给`fetchInitialData`属性(如果它存在的话),这样，如果客户端还没有来自服务器的数据，它也可以调用它。为此，我们将使用`Route`的`render`方法，这样我们就可以自己创建元素并给它传递任何道具。

```
// shared/App.js
import React, { Component } from 'react'
import routes from './routes'
import { Route } from 'react-router-dom'

class App extends Component {
  render() {
    return (
      <div>
       {routes.map(({ path, exact, component: C, ...rest }) => (
          <Route
            key={path}
            path={path}
            exact={exact}
            render={(props) => (
              <C {...props} {...rest} />
            )}
          />
        ))}
      </div>
    )
  }
} 
```

在我们继续之前，让我们给我们的`App`添加一个导航栏和一个 [catch all - 404](https://tylermcginnis.com/react-router-handling-404-pages/) 路线。

```
// shared/Navbar.js
import React from 'react'
import { NavLink } from 'react-router-dom'

export default function Navbar () {
  const languages = [{
    name: 'All',
    param: 'all'
  }, {
    name: 'JavaScript',
    param: 'javascript',
  }, {
    name: 'Ruby',
    param: 'ruby',
  }, {
    name: 'Python',
    param: 'python',
  }, {
    name: 'Java',
    param: 'java',
  }]

  return (
    <ul>
      {languages.map(({ name, param }) => (
        <li key={param}>
          <NavLink activeStyle={{fontWeight: 'bold'}} to={`/popular/${param}`}>
            {name}
          </NavLink>
        </li>
      ))}
    </ul>
  )
} 
```

```
// shared/NoMatch.js
import React from 'react'

export default function NoMatch () {
  return (
    <div>
      Four Oh Four
    </div>
  )
} 
```

```
import React, { Component } from 'react'
import routes from './routes'
import { Route, Switch } from 'react-router-dom'
import Navbar from './Navbar'
import NoMatch from './NoMatch'

class App extends Component {
  render() {
    return (
      <div>
        <Navbar />

        <Switch>
         {routes.map(({ path, exact, component: C, ...rest }) => (
            <Route
              key={path}
              path={path}
              exact={exact}
              render={(props) => (
                <C {...props} {...rest} />
              )}
            />
          ))}
          <Route render={(props) => <NoMatch {...props} />} />
        </Switch>
      </div>
    )
  }
}

export default App 
```

## 👌👌👌

看起来不错。如果我们转到`/`路线，我们将得到预期的`Navbar`和`Home`组件，但是，如果我们单击其中一个`Link`，我们将得到一个错误- `Cannot read property 'map' of undefined`。

实际上，之前我们将`data`作为道具传递给`App`，然后，我们将它传递给`Grid`。因为我们不再渲染`App`内的`Grid`(因为我们正在渲染`Route` s ),所以`data`不会到达`Grid`,因此`Grid`内的`props.data`是未定义的。那是一口。基本上`Grid`不再接收它需要的数据。

有几种不同的方法可以解决这个问题。当我们在`render`方法中呈现数据时，我们*可以*将数据传递给组件。

```
<C {...props} {...rest} data={this.props.data} /> 
```

那行得通。但是它会传递给每个组件，甚至是那些不需要它的组件。我们可以想象一下，只有当它是`Grid`组件时才通过它，但这似乎过于复杂。相反，我们将使用我们之前谈到的`context`道具。我们粘贴在传递给`context`的对象上的任何东西，我们以后都能够在任何组件中以`props.staticContext`的身份访问。所以与其把`data`传给`App`，不如用`context`来代替。

```
// server/index.js
...

promise.then((data) => {
  const context = { data }

  const markup = renderToString(
    <StaticRouter location={req.url} context={context}>
      <App />
    </StaticRouter>
  )

... 
```

请注意，我们不再将任何东西作为道具传递给`App`。现在，为了访问流行的回购，我们将从`props.staticContext.data`中获得它。让我们前往我们的`Grid`组件，在那里我们需要数据并进行更改。

```
class Grid extends Component {
  render() {
    const repos = this.props.staticContext.data

    return (
      <ul style={{display: 'flex', flexWrap: 'wrap'}}>
        {repos.map(({ name, owner, stargazers_count, html_url }) => (
          <li key={name} style={{margin: 30}}>
            <ul>
              <li><a href={html_url}>{name}</a></li>
              <li>@{owner.login}</li>
              <li>{stargazers_count} stars</li>
            </ul>
          </li>
        ))}
      </ul>
    )
  }
} 
```

我们的应用程序现在正处于一个有趣的时刻。如果你在浏览器中加载`http://localhost:3000/popular/javascript`,它会工作，但也会抛出一些错误。这样做的原因是因为我们在服务器上渲染，这工作得很好。然后当 React 转到“捡起来”时，它抛出一个`Cannot read property 'data' of undefined`错误。这样做的原因是因为，就像我们之前在服务器上做的一样，我们正在将一个`data`道具传递给客户端的`App`组件。

```
// browser/index.js

hydrate(
  <BrowserRouter>
    <App data={window.__INITIAL_DATA__} />
  </BrowserRouter>,
  document.getElementById('app')
); 
```

这是行不通的，原因和它在服务器上行不通一样。`App`不再将数据传递给`Grid`组件。我们可以从`Grid`组件内部的`window`对象中获取数据，而不是向下传递数据。

```
// browser/index.js

hydrate(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById('app')
); 
```

```
class Grid extends Component {
  constructor(props) {
    super(props)

    let repos
    if (__isBrowser__) {
      repos = window.__INITIAL_DATA__
      delete window.__INITIAL_DATA__
    } else {
      repos = props.staticContext.data
    }

    this.state = {
      repos,
    }
  }
  render() {
    const { repos } = this.state

    return (
      <ul style={{display: 'flex', flexWrap: 'wrap'}}>
        {repos.map(({ name, owner, stargazers_count, html_url }) => (
          <li key={name} style={{margin: 30}}>
            <ul>
              <li><a href={html_url}>{name}</a></li>
              <li>@{owner.login}</li>
              <li>{stargazers_count} stars</li>
            </ul>
          </li>
        ))}
      </ul>
    )
  }
} 
```

看起来不错。现在，如果我们在浏览器上渲染，我们将从`window.__INITIAL_DATA__`获取数据，如果不是，我们将从`staticContext`获取数据。

### 我向你保证我们已经很接近了。

至此，我们的服务器已经全部完成。它正确地获取请求的路径，获取该路径的任何数据，然后发送回一个良好的服务器呈现响应。是客户有问题。例如，加载回家路线`localhost:3000`，然后点击“JavaScript”链接。你会注意到你得到一个错误。知道为什么会这样吗？记住，我们同时处理服务器端和客户端渲染。服务器端渲染只在初始页面加载时进行，之后，React Router 会接管。因此，当我们第一次请求应用程序时，一切都很好。然后，React 路由器接管，我们尝试访问`/popular/javascript`，由于我们没有正确的数据，应用程序中断。好消息是要解决这个错误，我们可以像往常一样做——如果我们还没有从服务器获取数据，就在`componentDidMount`获取数据。

```
class Grid extends Component {
  constructor(props) {
    super(props)

    let repos
    if (__isBrowser__) {
      repos = window.__INITIAL_DATA__
      delete window.__INITIAL_DATA__
    } else {
      repos = this.props.staticContext.data
    }

    this.state = {
      repos,
      loading: repos ? false : true,
    }

    this.fetchRepos = this.fetchRepos.bind(this)
  }
  componentDidMount () {
    if (!this.state.repos) {
      this.fetchRepos(this.props.match.params.id)
    }
  }
  fetchRepos (lang) {
    this.setState(() => ({
      loading: true
    }))

    this.props.fetchInitialData(lang)
      .then((repos) => this.setState(() => ({
        repos,
        loading: false,
      })))
  }
  render() {
    const { repos, loading } = this.state

    if (loading === true) {
      return <p>LOADING</p>
    }

    return (
      <ul style={{display: 'flex', flexWrap: 'wrap'}}>
        {repos.map(({ name, owner, stargazers_count, html_url }) => (
          <li key={name} style={{margin: 30}}>
            <ul>
              <li><a href={html_url}>{name}</a></li>
              <li>@{owner.login}</li>
              <li>{stargazers_count} stars</li>
            </ul>
          </li>
        ))}
      </ul>
    )
  }
} 
```

现在，当组件挂载时，如果我们还没有数据(如果 React Router 将我们带到这个页面，我们就不会有数据)，我们将获取它，然后调用`setState`。我们还在我们的州中添加了一个`loading`属性来稍微改善一下 UX。

## 一个。更多。问题。

现在，当我们从`/`导航到`/popular/javascript`时，一切正常。但是当我们从一种语言转向另一种语言时会发生什么呢？说从`/popular/javascript`到`/popular/ruby`？你会发现什么都没发生。同样，这只是一个反应的事情。道具在变，但是组件不会重新安装，所以`componentDidMount`不会被再次调用。我们可以使用 React 的`componentWillReceiveProps`生命周期方法来解决这个问题。

```
// shared/Grid.js

componentWillReceiveProps (nextProps) {
  const { match, fetchInitialData } = this.props

  if (nextProps.match.params.id !== match.params.id) {
    this.fetchRepos(nextProps.match.params.id)
  }
} 
```

现在，当下一种语言(`nextProps.match.params.id`)与前一种语言(`match.params.id`)不匹配时，我们将继续调用`fetchRepos`传递新语言。

就这样，我们结束了！第一个请求将由服务器呈现，随后的每个路径更改将由反应路由器拥有。

现在，你告诉我，这种复杂性值得给你的应用带来好处吗？🤷‍

> 你可以在这里找到最终代码-[github.com/tylermcginnis/rrssr](https://github.com/tylermcginnis/rrssr)。

* * *

这篇文章最初发表在 TylerMcGinnis.com 大学，是他们 T2 反应路由器课程的一部分。
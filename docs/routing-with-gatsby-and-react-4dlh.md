# 使用 GatsbyJS 静态站点生成器的单源真实路由

> 原文：<https://dev.to/hugecoderguy/routing-with-gatsby-and-react-4dlh>

# 盖茨比的美

[Gatsby 是一个用现代工具构建静态站点的漂亮工具](https://www.gatsbyjs.com/)。
它利用 React 的组件架构，并巧妙地在构建时在服务器端呈现
你的网站，生成一个静态网站，随时可用。

您所要做的就是从项目中自动生成的`src/pages`目录下的任何`*.js`文件中导出一个组件，当它基于该文件的名称构建您的静态网站时，它会创建一个路径。仅此一点就足以让
在短时间内建起一个静态网站！

Gatsby 还提供了一系列构建钩子，你可以为其编写函数，以生成页面、GraphQL 节点、用组件包装你的应用程序，以及介于两者之间的任何东西。

# 盖茨比的问题

当创建一个网站时，在创建
路线时有一个单一的真实来源是很好的。端点是互联网的主干，如果没有一个单一的模块可以指出真相，那么在一个
快速增长的应用程序中管理它们会变得令人厌倦。

关于路由，盖茨比最大的长处也是它最大的弱点；
`src/pages`目录，听起来很神奇，它不提供引用基于文件名构建的路径的方法。这意味着为了链接到你网站中的另一个页面，你必须*硬编码*对另一个页面的字符串引用。恶心！

# 我的解决方案

如果你想找到最终的解决方案，[这里有一个回购](https://github.com/ckreiling/gatsby-router)的链接。

我们将使用 [Gatsby 的`createPages`](https://www.gatsbyjs.org/docs/node-apis/#createPages) build hook 来创建我们的路线。Gatsby 使用 Redux 来维护状态，并将绑定的动作创建者传递给所有的构建钩子。我们将在`creatPages`构建钩子中使用`createPage`动作创建器。我将带您走过每一步，从用 Gatsby 的构建工具生成一个新的静态站点开始。

如果您还没有安装它，请在命令行中运行以下命令:

```
npm install -g gatsby 
```

## 生成网站

这是容易的部分！要在`gatsby-routing`目录中创建一个新的 Gatsby 站点，运行以下命令:

```
gatsby new gatsby-routing 
```

## 设置路线

让我们创建一个目录`src/routes`，并将`index.js`和`page-2.js`从`src/pages`移动到新的路由目录中。将`index.js`更名为`home.js`。
之后，创建一个名为`index.js`的新文件，并将下面的代码
放入其中:

```
// src/routes/index.js
const path = require('path')

const routes = {
  home: {
    path: '/',
    component: path.resolve(__dirname, 'home.js')
  },
  page2: {
    path: '/page-2',
    component: path.resolve(__dirname, 'page-2.js')
  }
}

export { routes } 
```

我们必须使用像`path`这样的节点 API，而不是 ES6 `import`，因为我们将在 Gatsby 的`gatsby-node.js`文件中
需要这个文件。让我们设置这些路线
，以便它们准备好呈现-打开`gatsby-node.js`并将下面的
代码放入其中。

```
const { routes } = require('./src/routes')

exports.createPages = ({ actions }) => {
  const { createPage } = actions
  Object.keys(routes).forEach(route => createPage(routes[route]))
} 
```

所以在`createPages`中，我们使用`createPage`动作创建器来创建新页面，
使用我们导入的`routes`对象中的对象。`createPage`接受
一个带有路径字符串和 React 组件的对象来呈现该路径，
和一个可选的上下文，您可以将它作为道具传递给 React 组件。

从项目的根目录运行`gatsby develop`来看看效果！

## 转换链接以引用我们的路线

如果您查看`src/routes`目录中的组件，您会注意到
我们仍然在链接中硬编码路径。让我们现在就解决这个问题！

首先，让我们取消路由嵌套，创建一个对象，它的键与我们的
路由相同，但值只有路径字符串。你的`src/routes/index.js`
现在应该是这样的:

```
// src/routes/index.js
const path = require('path')

const routes = {
  home: {
    path: '/',
    component: path.resolve(__dirname, 'home.js')
  },
  page2: {
    path: '/page-2',
    component: path.resolve(__dirname, 'page-2.js')
  }
}

// Same keys as 'routes', but the value is only the path.
const paths = Object.keys(routes).reduce((acc, route) => {
  acc[route] = routes[route].path
  return acc
}, {})

export { routes, paths } 
```

现在让我们导入新的`paths`对象，并用它来链接我们的 Gatsby
站点！

```
// src/routes/home.js
import React from 'react'
import { Link } from 'gatsby'

import Layout from '../components/layout'

import { paths } from './'

const IndexPage = () => (
  <Layout>
    <h1>Hi people</h1>
    <p>Welcome to your new Gatsby site.</p>
    <p>Now go build something great.</p>
    <Link to={paths.page2}>Go to page 2</Link>
  </Layout> )

export default IndexPage

// src/routes/page-2.js
import React from 'react'
import { Link } from 'gatsby'

import Layout from '../components/layout'

import { paths } from './'

const SecondPage = () => (
  <Layout>
    <h1>Hi from the second page</h1>
    <p>Welcome to page 2</p>
    <Link to={paths.home}>Go back to the homepage</Link>
  </Layout> )

export default SecondPage 
```

就这样，我们不再在任何组件中硬编码我们站点的路径！现在，创建一条新路线就像创建一个新组件
并将其添加到`src/routes/index.js`一样简单。

# 总结

如果你是 JavaScript 新手，或者想学习 React，Gatsby 是一个很好的起点。我正在构建一个大型的应用程序，不能承受将 URL 硬编码到我的应用程序中。我希望这能帮助其他在
遇到类似情况的人！

这是我的第一篇官方博客，我希望得到一些反馈:)
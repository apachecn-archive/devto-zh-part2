# 如何使用 Next & Express 构建服务器渲染的 React 应用程序

> 原文：<https://dev.to/bnevilleoneill/how-to-build-a-server-rendered-react-app-with-next--express-fcl>

[![](../Images/218ddfc3e274f4175c0c1affe43f7a83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2CpEoEMF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AiPaRQckAecqGEZA04z7k5A.png)

不言而喻，React 是构建高质量 web 应用程序的绝佳选择。然而，随着事情变得越来越复杂，您需要了解客户端路由、页面布局等等。在某些时候，你会希望你的网页加载速度更快。很多时候，这是事情变得困难的地方。

[Next.js](https://learnnextjs.com/) 是一个通用的 JavaScript 框架，可以在浏览器和服务器上运行。它为开发人员提供了一种简单的入门方式，而且由于它使用 React 进行模板化，对于有 React 经验的开发人员来说，这也是一种快速提高工作效率的直接方式。

它的一个优点是它出色地处理了服务器端渲染，并且它与 [Express](https://expressjs.com/) 集成得很好。哦，我多么喜欢快递。

但是我们太超前了。先说说为什么需要服务器端渲染吧。然后我们开始建造东西。

### 到底什么是服务器端渲染？

服务器端呈现是将 HTML 显示在屏幕上的传统方法。这指的是使用服务器环境让你的 HTML 到达浏览器。

如果这是一种由来已久的传统方法，又何必大惊小怪呢？

还记得引起一些关注的 [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) (模型、视图、控制器)概念的引入吗？基本上，有些分歧最终导致了用于呈现视图的 JavaScript 框架的兴起。

那么这有什么关系呢？

很快，一个问题出现了:JavaScript 框架只在浏览器中显示了一堆 div，使用 DOM 操纵来完成浏览器周围的工作。这意味着用户必须等待更长时间才能看到任何东西。如果爬虫不能快速看到页面的内容，也会影响 SEO。

一种解决方案是在将输出返回给服务器之前，从服务器呈现 JavaScript 文件。

现在你知道了。

### 下一步入门

入门很简单。我们需要做的就是新建一个目录，启动一个新的节点项目，安装 Next，React，ReactDOM。

```
#make a directory
mkdir logrocket-next
#change to the new directory
cd logrocket-next
#init a new node project
npm init -y 
#install react, react-dom and next
npm install --save react react-dom next 
```

Enter fullscreen mode Exit fullscreen mode

接下来，打开 package.json，将脚本部分替换为:

```
"scripts": {
        "dev": "next",
        "build": "next build",
        "start": "next start"
    } 
```

Enter fullscreen mode Exit fullscreen mode

运行 npm run dev 命令，您应该会得到类似如下的错误:

```
next > Couldn't find a `pages` directory. Please create one under the project root 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 Next 使用 pages 目录和其中的文件来映射它的路由。这意味着如果我们的 pages 文件夹中有一个名为 index.js 的文件，Next 将尝试使用其中的组件作为我们的入口点。让我们创建 pages 文件夹和 index.js 文件。

```
#create pages directory
mkdir pages
#create file for base route
touch pages/index.js 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们向 pages/index.js 文件添加一些代码:

```
const Index = () => (
  <div>
    <p>Hello Next.js, this is your friend Brian from logrocket</p>
  </div>
)
export default Index 
```

Enter fullscreen mode Exit fullscreen mode

保存上面的文件，并在您的终端中运行命令 npm run dev。如果你打开你的浏览器，你会看到“Hello Next.js，这是你在 logrocket 的朋友 Brian”打印出来的文本。

注意到这有多简单了吗？不需要将 React 挂载到 div，不需要导入 React，不需要设置路由。在我们常用的 React 应用程序中，我们需要进行其他配置，以允许代码分割和服务器端呈现。但是，嘿，看看你的页面来源。你会惊讶的。都是开箱即用的。

[![](../Images/19cd777c2f0d6ffa9ede505274668f63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ChrR6qVG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AnSmdVGYLcOciqx8Y.png)

注意上图中，有具体提到`[/_next/-/page/index.js](http://localhost:3000/_next/-/page/index.js)`？代码分割做得很好。另外，请注意，包含您的文本的 div 是完全呈现的。这是发生在服务器端的渲染。

### 接下来还有快递

我敢打赌，你认为这就是接下来的魔术。Next 更进一步，允许在更困难的情况下使用 Express 进行更好的服务器端渲染。

首先，在你的应用程序中添加快递:

```
npm install --save express 
```

Enter fullscreen mode Exit fullscreen mode

然后在您的应用程序中创建一个名为 ssr-server.js 的文件，并添加以下内容:

```
const express = require('express')
const next = require('next')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare()
.then(() => {
  const server = express()

  server.get('*', (req, res) => {
    return handle(req, res)
  })

  server.listen(3000, (err) => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
})
.catch((ex) => {
  console.error(ex.stack)
  process.exit(1)
}) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码发生了什么？我们需要 Express 和 Next 库。我们创建下一个库的实例，根据环境传入一个布尔值，该环境检测是否在开发模式下启动 Next.js。

我们继续调用 getRequestHandler()函数，最后我们准备应用程序。prepare 函数返回一个承诺，所以我们可以对它做一个`.` `then`管道。在`.` `then`调用中，我们启动了 Express，并使用通配符 route 捕获所有路由，并将其返回给处理函数。

现在将您的 npm 开发脚本更新为:

```
{ "scripts": { "dev": "node ssr-server.js" } } 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行 npm run dev，你的页面看起来会和原来一样。如果我最终得到和之前一样的结果，这又有什么帮助呢？让我展示给你看。

虽然我们上面所做的似乎没有增加太多的差异，但当我们添加更多的路由时，这是有意义的，因为它有助于实现干净的 URL。值得注意的是，如果在 Next 中实现，这将返回 404 个页面(当没有通过 Next 链接导航时，也就是说，如果我在浏览器中手动输入 URL，或者我是从另一个站点被定向的)。

请看下面的这条路线，它帮助我们获得干净的 URL，正如上一段所讨论的:

```
server.get('/p/:id', (req, res) => {
    const actualPage = '/post'
    const queryParams = { id: req.params.id } 
    app.render(req, res, actualPage, queryParams)
}) 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，在 Next 中使用查询字符串很容易，但是像往常一样，您希望保持 URL 的整洁，所以您选择了类似于:/p/2 而不是/p？id=2。

在上面的代码中，我们使用流行的快速路由来定义这样的路由，然后我们将应该加载的页面和 id 作为查询参数传递给主 Next 应用程序。在这里调用/p？id=2 发生在引擎盖下，没有人能看到发生了什么。

普通用户看到的 URL 是/p/2/。

### 服务和导出您的应用程序

当你完成构建下一个应用程序时，问题就变成了:“我如何在生产中服务它？”

简单。

首先，我们必须构建应用程序。那我们就可以上菜了。幸运的是，Next 提供了一条简单的出路。还记得我们在 package.json 中的脚本部分吗？我们把一切都安排好了。

我们需要做的就是:

```
#build the app
npm run build
#serve the app
npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

哇，太酷了，如果你想将应用程序导出为静态 HTML 文件怎么办？问得好。首先，在你的应用程序的根目录下创建一个名为 next.config.js 的文件，并添加以下内容:

```
module.exports = {
  exportPathMap: function () {
    return {
      '/': { page: '/' }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> ***注意*** *:在上面的设置中，只有索引页面会被导出，因为它是我们在路径图中指定的唯一路径。*

如果我们想添加更多的页面呢？朋友们，这是个好问题。

您可以像`'/about': { page: '/about' }`一样在路径图的返回对象中添加一个新的键和值。

然后将以下内容添加到包的脚本部分

```
"export": "next export" 
```

Enter fullscreen mode Exit fullscreen mode

最后，构建并导出您的应用程序。

```
#build app
npm run build
#export app
npm run export 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

在本教程中，我们已经能够看到使用 Next.js 构建服务器渲染的应用程序是多么容易。这是使用 React 做事情的一种极好的方式。如果你在 React 中完成了同样的过程，你会同意我的观点。

你有什么评论或意见吗？在评论区说吧。

* * *

### Plug: LogRocket，一款用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[用 Next.js & Express.js](https://blog.logrocket.com/how-to-build-a-server-rendered-react-app-with-next-express-d5a389e7ab2f/) 构建一个服务器渲染的 React 应用最先出现在 [LogRocket 博客](https://blog.logrocket.com)上。
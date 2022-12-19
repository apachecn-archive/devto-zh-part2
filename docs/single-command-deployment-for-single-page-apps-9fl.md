# 单页面应用的单命令部署

> 原文：<https://dev.to/slobodan/single-command-deployment-for-single-page-apps-9fl>

# 单页应用的单命令部署

开发一个单页面的应用程序是很难的。从一开始，您就需要做出许多决定——比如选择一个框架、设置文件夹结构、配置 linter 等等。一般来说，由于围绕你最喜欢的框架和 web 开发的工具生态系统，其中一些任务变得更加容易。例如，Create React App、Angular CLI 和 Create Choo App 等工具将帮助您在几秒钟内设置好您喜欢的框架。

[![Photo by [Jonatan Pie](https://unsplash.com/photos/3l3RwQdHRHg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on Unsplash](img/f03676be35547f3563a69a1787752372.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KWjZ_cuf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y8oyzi5ps873zrus7k7e.jpeg)

通常，当您开始新项目时，您甚至没有足够的时间来考虑部署。在某些时候，你需要公开你的应用程序，因为你想把它展示给你的客户、朋友，或者在你找第一份工作的时候把它添加到你的文件夹中。

但是，如何快速选择部署应用程序的最佳位置呢？部署工具也有很多。如果你用一些新的闪亮的东西，它会为生产规模，或者你会被迫作出另一个决定改变它吗？你可以使用 Github 页面，但是你需要的服务人员的 HTTPS 呢？

亚马逊提供了一些可以扩展的东西，一个简单的静态网站托管存储服务(S3)和作为 CDN 的 CloudFront 的组合是一种便宜但可扩展的方式来提供你的单页应用程序。尽管这两项都需要一些时间来准备，如果你不熟悉 Amazon Web Services 的话，甚至需要更多的时间。

不过，有一种更简单的方法——引入 [Scotty.js](https://github.com/stojanovic/scottyjs) ，这是一个简单的 CLI 工具，可以帮助你用一个命令将你的网站或单页应用程序部署到亚马逊 S3 和 CloudFront。

## 斯科特，把我传送上去

Scotty 背后的主要思想是用一个命令将你的静态网站或单页应用程序部署到亚马逊生态系统。

它将部署你的静态网站，设置 HTTPS 的 CDN，甚至在一分钟左右的时间内将网站 URL 复制到你的剪贴板，这取决于你的互联网速度和网站/应用程序的大小。

对于单页应用程序，它还将配置重定向，因此 pushState 可以开箱即用。

[![Beam me up, Scotty](img/2129ba1709e08a30e8d7c30027e40381.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bqMXBIGL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dik92plfaxjq6sq9qqtc.gif)

让我们通过一个简单的 React 应用程序来看看它的运行情况。

## 创建 React App

在部署之前，我们需要应用程序，所以让我们使用 Create React App 创建一个简单的应用程序。

首先，从您的终端
运行`create-react-app`命令，创建一个示例应用程序

```
create-react-app scotty-cra-example 
```

Enter fullscreen mode Exit fullscreen mode

如果你没有安装 create-react-app 命令，你可以从 https://www.npmjs.com/package/create-react-app 的 NPM 这里得到它。

或者，如果您使用的是 NPM v5，您可以运行 Create React App 命令，而不需要使用新的 npx 命令进行全局安装:

```
npx create-react-app -- scotty-cra-example 
```

Enter fullscreen mode Exit fullscreen mode

了解更多`npx`这里:[https://medium . com/@ maybe Katz/introducing-npx-an-NPM-package-runner-55 f 7d 4 BD 282 b](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)。

让我们添加 React 路由器来演示推状态支持是如何工作的。为此，进入您的新项目并安装 React Router 作为依赖项:

```
cd scotty-cra-example

npm install react-router-dom --save 
```

Enter fullscreen mode Exit fullscreen mode

现在一切都已安装完毕，让我们将 React Router 添加到项目中—在您最喜欢的编辑器中打开“src/App.js”文件，并将其更新为 React Router 的基本示例([https://React training . com/React-Router/web/example/basic):](https://reacttraining.com/react-router/web/example/basic):)

```
import React from 'react'
import {
  BrowserRouter as Router,
  Route,
  Link
} from 'react-router-dom'
import logo from './logo.svg'
import './App.css'

const BasicExample = () => (
  <div className="App">
    <div className="App-header">
      <img src={logo} className="App-logo" alt="logo" />
      <h2>Welcome to React</h2>
    </div>
    <p className="App-intro">
      <Router>
        <div>
          <ul>
            <li><Link to="/">Home</Link></li>
            <li><Link to="/about">About</Link></li>
            <li><Link to="/topics">Topics</Link></li>
          </ul> 
          <hr/>

          <Route exact path="/" component={Home}/>
          <Route path="/about" component={About}/>
          <Route path="/topics" component={Topics}/>
        </div>
      </Router>
    </p>
  </div> )

const Home = () => (
  <div>
    <h2>Home</h2>
  </div> )

const About = () => (
  <div>
    <h2>About</h2>
  </div> )

const Topics = ({ match }) => (
  <div>
    <h2>Topics</h2>
    <ul>
      <li>
        <Link to={`${match.url}/rendering`}>
          Rendering with React
        </Link>
      </li>
      <li>
        <Link to={`${match.url}/components`}>
          Components
        </Link>
      </li>
      <li>
        <Link to={`${match.url}/props-v-state`}>
          Props v. State
        </Link>
      </li>
    </ul> 
    <Route path={`${match.url}/:topicId`} component={Topic}/>
    <Route exact path={match.url} render={() => (
      <h3>Please select a topic.</h3>
    )}/>
  </div> )

const Topic = ({ match }) => (
  <div>
    <h3>{match.params.topicId}</h3>
  </div> )

export default BasicExample 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你使用`npm start`启动你的应用程序，它应该可以工作，看起来与这张截图相似:

[![Basic React app with React Router on localhost](img/c4b0dd4f98717d401b7b6772753deb25.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4fjVP07N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t8r63elwffx720ftl5o4.png)

是时候使用 npm 运行构建节点脚本来构建您的应用程序了。这将在项目的根目录下创建一个名为“build”的文件夹。

## 部署 app

首先从 NPM 安装 Scotty.js 作为一个全局包，运行:

```
npm install scottyjs -g 
```

Enter fullscreen mode Exit fullscreen mode

Scotty 的先决条件是:

*   带有 NPM 的 Node.js (v4+)
*   AWS 帐户
*   AWS 凭证—参见设置教程[此处](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)

然后在你的终端上运行下面的命令(确保你首先导航到项目文件夹):

```
scotty --spa --source ./build 
```

Enter fullscreen mode Exit fullscreen mode

这个命令告诉 Scotty 您的应用程序是单页应用程序(SPA ),并且您的项目的源代码在“build”文件夹中。

> Bucket 名称对所有用户都是全局的，这意味着您需要为您的应用程序想出一个唯一的名称——重用“scotty-cra-example”是行不通的。

从您的终端运行此命令将部署应用程序，并为您提供两个 URL，如下所示:

[![](img/0fbd3e83ac65682ee5a6837b5e802458.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hmmiw-q0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rploeywc2g0azebji4nv.gif)

第一个，也添加到您的剪贴板，是一个到 AWS S3 的 HTTP 链接。第二个是同样支持 HTTPS 的 CloudFront URL。

## CDN 和 HTTPS

Scotty 将在 CloudFront CDN 上设置您的项目，这意味着它将被缓存并分发到不同的区域以减少延迟。

它还将免费设置 HTTPS，因此您的应用程序将随时可供服务人员或任何需要安全连接的人使用。

> 直播 app:[https://d1reyqfbyftmjg.cloudfront.net](https://d1reyqfbyftmjg.cloudfront.net)

## 它是如何工作的

斯科特背后没有魔法。它在幕后使用了 Node.js 的 AWS SDK。首先，它检查你是否已经有了一个默认区域。不幸的是，AWS 没有通过 AWS SDK 给我们一个默认区域。Scotty 有一个小型的 LevelDB 数据库来存储这些信息。如果该区域不存在并且没有提供，Scotty 会要求您选择它。

[![](img/168ad3759e3e5fa5fee8f3a72f80b450.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Cdskwgnv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fwss1zlpmfkiyc8oikcs.png)

下一步是创建一个 bucket。如果没有提供 bucket name，Scotty 将使用当前文件夹的名称。请记住，bucket 名称对于所有用户都是全局的，因此，您需要为您的 bucket 取一个唯一的名称。

创建 bucket 后，Scotty 将使用 AWS SDK 将您的项目上传到 AWS S3。如果没有提供源标志，当前文件夹将被用作源。

最后一步，如果你的项目是一个网站或单页应用，Scotty 将在 HTTPS 的支持下建立 CloudFront CDN。SPA 和 website 的区别在于，Scotty 将所有不存在的页面重定向回 index.html，这使得 pushState 可以开箱即用。

* * *

下一步是什么？

试试 Scotty，让我知道有什么可以改进的地方。很高兴收到拉取请求，因为欢迎新功能和改进。

Github 库:[https://github.com/stojanovic/scottyjs](https://github.com/stojanovic/scottyjs)

Scotty 目前的想法是只为 AWS 保留一个小库，并提供一种以无服务器方式部署前端应用程序和网站的简单方法。

但是，还缺少一些东西，比如设置自定义域名和配置文件以便于协作。

希望你会喜欢👽

如果你想了解更多关于 AWS 上无服务器架构的知识，可以看看我在曼宁出版社出版的新书:[带 Node 和 Claudia.js 的无服务器应用](https://www.manning.com/books/serverless-apps-with-node-and-claudiajs)。
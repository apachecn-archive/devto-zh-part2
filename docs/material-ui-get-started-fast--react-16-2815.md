# Material-UI:入门(快！)⚡反应 16+

> 原文：<https://dev.to/kireerik/material-ui-get-started-fast--react-16-2815>

如何入门 Material-UI？

在我看来，这是简单而正确的方法。

请按照这里的[指示](https://github.com/kireerik/razzle-material-ui-styled-example#getting-started)去做:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[razzle-material-ui-styled-example](https://github.com/kireerik/razzle-material-ui-styled-example)

### razzle Material-使用 Express with compression 的带样式组件的 UI 示例

<article class="markdown-body entry-content container-lg" itemprop="text">

# Razzle 材质-UI 风格的例子

[![Node.js](img/c4db920c2cd6b8e47f984dd8c40711fc.png)](https://nodejs.org/en/ "Node.js")[![Yarn](img/63934951f6029676f733f47485b9e4bc.png)](https://yarnpkg.com/lang/en/ "Yarn")[![Razzle](img/e452bbd362620bbfbd2a3dadceebebff.png)](https://github.com/jaredpalmer/razzle "Razzle")[![Express](img/7ba5ab7c31a19896ee9962a03dddc6bc.png)T11】](https://expressjs.com/ "Express")

[![React](img/9c407167aba1c6bb992e3f839914673e.png)](https://facebook.github.io/react/ "React")[![Styled Components](img/1622fea020133a6c4a8de4604a130e8e.png)](https://www.styled-components.com/ "Styled Components")[![Material-UI](img/6de2d2d21ca835b9c3cb32ef09af4640.png)](http://www.material-ui.com/#/ "Material-UI")

[![GitPunch](img/ce50b600f936ebe5978fb9e93d3d61eb.png)](https://gitpunch.com/ "GitPunch") 

## 特征

*   [![Razzle](img/e452bbd362620bbfbd2a3dadceebebff.png)T2】](https://github.com/jaredpalmer/razzle "Razzle")
    *   **S** 服务器 **S** ide **R** endering
    *   **H** ot **M** 模块 **R** 客户端和服务器端的替换[T7](https://facebook.github.io/react/ "React")T9】反应组件
    *   最新 JavaScript([**E**CMA](https://en.wikipedia.org/wiki/Ecma_International)**S**script 6(2015))支持
    *   单路由静态站点生成
*   [![Express](img/7ba5ab7c31a19896ee9962a03dddc6bc.png)](https://expressjs.com/ "Express") 服务器用 gzip [压缩](https://github.com/expressjs/compression)
    *   使用 [HTMLMinifier](https://github.com/kangax/html-minifier) 缩小 HTML 和内联 CSS 和 JS
    *   [![Styled Components](img/1622fea020133a6c4a8de4604a130e8e.png) ](https://www.styled-components.com/ "Styled Components") [样式组件](https://www.styled-components.com/)
    *   [![Material-UI](img/6de2d2d21ca835b9c3cb32ef09af4640.png) ](http://www.material-ui.com/#/ "Material-UI") [素材-UI](http://www.material-ui.com/#/)

## 入门指南

*   初始步骤
    *   安装 [![Node.js](img/c4db920c2cd6b8e47f984dd8c40711fc.png)](https://nodejs.org/en/ "Node.js") 和 [![Yarn](img/63934951f6029676f733f47485b9e4bc.png)](https://yarnpkg.com/lang/en/ "Yarn") 。
    *   [下载](https://github.com/kireerik/razzle-material-ui-styled-example/archive/master.zip)或者[克隆](https://raw.githubusercontent.com/kireerik/razzle-material-ui-styled-example/master/github-windows://openRepo/https://github.com/kireerik/razzle-material-ui-styled-example)这个资源库。
    *   在项目文件夹中打开命令提示符。

> 安装依赖项:
> 
> ```
> yarn install
> ```
> 
> Enter fullscreen mode Exit fullscreen mode

启动服务器:

> ```
> yarn start
> ```
> 
> Enter fullscreen mode Exit fullscreen mode
> 
> 此命令启动一个构建过程，并在生产模式下启动服务器。

访问 [http://localhost/](http://localhost/) 访问 web 应用程序。

### 发展

在开发模式下启动服务器:

```
yarn dev
```

Enter fullscreen mode Exit fullscreen mode

### 单路由静态站点生成

生成静态站点:

```
yarn static
```

Enter fullscreen mode Exit fullscreen mode

打开`static`文件夹中的`index.html`以访问网络应用程序。的…

</article>

[View on GitHub](https://github.com/kireerik/razzle-material-ui-styled-example)

> 等等！你在某种基于 Unix 的系统上吗？麦克。Linux？如果是这样的话，你可能想改变 [`.env`](https://github.com/kireerik/razzle-material-ui-styled-example/blob/6ad048dbb9fb6565070c9bd48bb0617a052b2572/.env#L2) 文件中的`PORT`，我已经把它设置为在 Windows 上工作的`80`。Razzle 的默认值是`3000`，所以如果你喜欢的话，你可以删除那行。

该项目已经启动并运行。

[![development mode](img/90f8c4674f28d45ce1daf2069fc09d1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dA01KP_8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/umu057rj5xoq0j7q0f5h.gif)

去野外实验吧！

我不是在开玩笑。立即关闭这篇文章，尝试该项目！

开个玩笑！😄你可以以后继续读...

> 你懒吗？[改为在 Codenvy](https://codenvy.io/dashboard/#/load-factory?id=factoryffyherfcfvcdm83r) 上启动项目！一次点击和项目启动和登录后运行。

你还在读书吗？好吧，我会握着你的手带你走一遍。

## 用法

这里的事情会变得很无聊。我已经告诉你自己去试一试。

好吧，玩笑归玩笑。我们是认真的。

### 基本

[![folder structure](img/37bfb865750df9239d940b75595b6954.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NXwklnl5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x569tgx1153ux0mgd53o.png)

文件夹结构

*   ⬅Static 的公开文件在这里。
*   科学研究委员会
    *   *应用/**[main . js](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/application/Main.js)***⬅the main react . js 组件。
    *   ***[server . js](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/server.js)***⬅serves 缩小版 HTML。
    *   client.js、index.js、static.js ⬅"Lower 级别”脚本(将在后面讨论)。

你可能已经发现了。`Main.js`包含 React.js 组件。所以基本上你可以在这里改变你网站的主要内容[。](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/application/Main.js#L47-L55)

[![source code](img/da682bd0b61e44925453d9c7c5a22a8a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yjBbKjdr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1k1kehgi3glmbar37m0p.png)

`server.js` [呈现 React.js 应用程序，收集样式](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/server.js#L30)，并在您访问站点时以 HTML 的形式提供所有这些内容。

检查源代码！是的，梦想实现了。这是一个适当缩小的一行程序。你可以[在这里](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/server.js#L34-L56)修改你的主 HTML，主要是你的页眉。

> [如何实际启动项目](https://github.com/kireerik/razzle-material-ui-styled-example#getting-started)详见已经链接的自述。示例项目的[特征](https://github.com/kireerik/razzle-material-ui-styled-example#features)也在此列出。

#### 📝密码

用[样式化组件](https://www.styled-components.com/) :
在`Main.js`中样式化组件容易吗

```
import styled from 'styled-components'

const Div = styled.div`
    text-align: center;
`

export const Main = () => <Div></Div> 
```

Enter fullscreen mode Exit fullscreen mode

这就是服务器端渲染如何与`server.js` :
中的样式化组件一起工作

```
import {renderToString} from 'react-dom/server'
import {ServerStyleSheet} from 'styled-components'

import Application from './application/Main'

const sheet = new ServerStyleSheet()
, html = renderToString(sheet.collectStyles(<Application />))
, css = sheet.getStyleTags()

// server response to send:
    `<!DOCTYPE HTML>
    <html lang="en">
        <head>` + css + `</head>
        <body>
            <div id="root">` + html + `</div>
        </body>
    </html>` 
```

Enter fullscreen mode Exit fullscreen mode

它还包括一些额外的东西，比如静态资源的压缩和 HTML 的缩小:T2

```
import {minify} from 'html-minifier'

minify(
    `<!DOCTYPE HTML><html lang="en">...</html>`
, {
    collapseWhitespace: true
    , removeComments: true
    , minifyCSS: true
    , minifyJS: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 高级

`src/index.js`是**服务器**的主要入口。这是 Razzle 要求的。

`src/client.js`是**浏览器**的主要入口点。这也是 Razzle 所要求的。

可选的`.env`包含一些环境变量定义。

您可以在`scripts`对象下的`package.json`中找到构建脚本。

#### 📝密码

这就是主入口点(`index.js`)启动服务器(`server.js` ):

```
import express from 'express'
import server from './server'

export default express()
    .use((request, response) => server.handle(request, response))
    .listen(process.env.PORT) 
```

Enter fullscreen mode Exit fullscreen mode

下面是 index.js 的完整[最简单版本。](https://github.com/kireerik/razzle-material-ui-styled-example/blob/d85a4f762220cfd33d45797f192858307b288d4e/src/index.js)

[client.js](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/client.js) 入口点融合了浏览器中已经呈现的应用程序:

```
import {hydrate} from 'react-dom'

import Application from './application/Main'

hydrate(<Application />, document.getElementById('root')) 
```

Enter fullscreen mode Exit fullscreen mode

在 [package.json](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/package.json#L2-L7) 中，`start`脚本使用了一种构建和运行方法:

```
{  "scripts":  {  "start":  "razzle build && cross-env NODE_ENV=production node build/server.js"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

[![yarn start.gif](img/5ad09f33df885d7d4fe97f99168d68cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Mk0tjnYP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ac6c8kl2xu1vals19v7u.gif)

#### 材料-界面

服务器端渲染使得正确使用 Material-UI 有点复杂。设置`userAgent`不一定简单(至少对我来说)。不过，[文档](http://www.material-ui.com/#/get-started/server-rendering)也没那么差。

以下带有链接的代码有望给出一些上下文:
[server.js](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/server.js#L30) :

```
renderToString(<Application userAgent={request.headers['user-agent']} />) 
```

Enter fullscreen mode Exit fullscreen mode

[client.js](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/client.js#L6) :

```
hydrate(<Application userAgent={navigator.userAgent} />, document.getElementById('root')) 
```

Enter fullscreen mode Exit fullscreen mode

[Main.js](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/application/Main.js#L29) :

```
class Main extends Component {
    constructor(properties) {
        super(properties)

        this.muiTheme = getMuiTheme({
            userAgent: properties.userAgent
        })
    }

    render = () =>
        <MuiThemeProvider muiTheme={this.muiTheme}></MuiThemeProvider> } 
```

Enter fullscreen mode Exit fullscreen mode

> [阅读栈溢出的解决方案](https://stackoverflow.com/questions/37816998/react-server-side-rendering-with-express-warning-on-the-client-checksum-styl/43958663#43958663)，如果你觉得有用，按下向上投票按钮。

#### 单路由静态站点生成

可选的 [static.js](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/static.js) 是从主服务器入口点 ( `index.js`)调用的[。它抓取已经生成的静态资源，并将它们复制到`static`文件夹中。另外，它访问 URL 并保存 HTML 响应:](https://github.com/kireerik/razzle-material-ui-styled-example/blob/master/src/index.js#L25) 

```
import http from 'http'
import fs from 'fs-extra'

const staticDirectory = process.env.RAZZLE_STATIC_PATH || 'static'

http.get({url: 'http://' + process.env.HOST + ':' + process.env.PORT
    , headers: {'User-Agent': 'all'}
}, response =>
    response.on('data', html =>
        fs.writeFile(staticDirectory + '/' + 'index.html', html)
    )
) 
```

Enter fullscreen mode Exit fullscreen mode

[![static site generation](img/c8bff37748c9fbd24df9cd9ddc7ceb53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6GBUp9ca--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/81237x87taxesjq8lfqw.gif)

静态站点生成脚本有些复杂:

```
{  "scripts":  {  "static":  "cross-env RAZZLE_STATIC=true razzle build && cross-env NODE_ENV=production node build/server.js"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

1.  一个常规的 Razzle 构建是通过将静态设置(`RAZZLE_STATIC=true`)嵌入到生成的服务器端代码中来创建的。
2.  该构建以`production`模式开始。
3.  静态生成器只在这一次执行。它执行前面描述的步骤并关闭服务器。

## 关于例子

### 动机

我想创建一个更加流行和吸引人的版本[搜索回来了！](https://searchisback.com/)。为什么？因为我认为我可以做得更好，我仍然认为我可以。
我很快意识到，我需要结合一些技术来实现我的愿景。

> 所以”...在测试了一堆示例项目后，我最终找到并使用了 Razzle。”([来源](https://github.com/jaredpalmer/razzle/issues/289#issuecomment-306586593))

我还需要在 Material-UI 和 Muse UI 之间做出选择。(在这一点上，使用材料设计对我来说不是问题。)我也考虑过其他库。但是我很快就喜欢上了 Material-UI 的[复选框动画](http://www.material-ui.com/#/components/checkbox)。不管怎样，它真的很坚固。

[![checkbox animation](img/8fc8e5061ad10b11f59e54dd3a124e47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VWhWldvE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mj567oylnus5hx27ghvg.gif)

首先，我想用 Muse UI，这是一个 Material-UI 的端口，因为我认为我会喜欢 Vue.js 胜过 React.js。

但是，当时它的文档只有中文，所以我选择了原始库。

第一次使用 React.js 将 CSS 和 HTML 结合到 JavaScript 中感觉有点奇怪。后来，当我将 Vue.js 用于一些小型客户端项目时，我意识到我实际上更喜欢 React 方式，尤其是对于样式化的组件。

最终，我创建了这个示例项目。所以我可以从我的真品开始，名为[脸书搜索器](https://facebooksearcher.com/):

 [![Facebook Searcher](img/df7fb4acb8e0796ea46825d9eb26054b.png)
T4】](https://facebooksearcher.com/)

这是一个简单的搜索表单，它建立了一个链接，所以你可以通过常规方式对脸书上的人进行定制搜索。你已经可以使用已实现的字段，它已经比它的竞争对手提供了更多的自由。

例如，你可以列出对两性都感兴趣的脸书用户(脸书支持)。它仍在开发中，一些功能正在等待实施。所以，如果你想以任何方式支持这个项目，请随时告诉我！

#### [其他原因](https://github.com/michaelrambeau/bestofjs/issues/91#issuecomment-307631163)

### 什么是[拉子](https://github.com/jaredpalmer/razzle)？

一个与框架无关的构建工具，用于服务器呈现的通用 JavaScript 应用程序。

#### 为什么要戏弄？

Razzle 使服务器端渲染变得简单。这对我来说是必须的。另外，它不会强迫你使用你不想使用的解决方案。

> 所以它”...是[...]极简主义..."([来源](https://github.com/jaredpalmer/razzle/issues/289#issuecomment-306586593))

热模块重载也是一个很好的特性。定制或扩展默认值也很简单。

> 所有这些使 Razzle”...一个几乎与框架无关的构建系统……”哪个"...将 100%与 Angular、Vue、Rax、Preact、Inferno、React-XP、RN-Web 一起工作...”——贾里德·帕尔默([来源](https://github.com/jaredpalmer/razzle/issues/289#issuecomment-306593963))

#### [谁在使用它？](https://github.com/jaredpalmer/razzle/issues/332)

### 接下来是什么？

[脸书搜索器](https://facebooksearcher.com)首先被“部署”给一个老派的主机提供商。我在那里有安全的 Shell 访问，所以我能够安装 Node.js。

我使用进程管理器 2 来确保它无论如何都在运行。当然，这没有静态网站快。另外，我没有一个好的部署和更新框架。过了一段时间，我开始手动保存 HTML，并以这种方式在 Netlify 上发布应用程序。它更加可靠，应用程序加载速度也更快。

后来，我转而使用 Firebase 主机，它更快，更可靠。我在这里实现了静态站点生成特性。手工做肯定是不可持续的。现在，我只需一条命令就可以构建和部署脸书搜索器。我现在可以集中精力实现剩下的特性了。

[![firebase deploy.png](img/6541b15ec19a9541525c60f6dc353802.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HjrPXhBF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g4pfcbb29q2h8rgf0a0j.png)

将我们的应用程序部署到 Firebase 主机很容易。而且，我们仍然可以将其部署为 Firebase 函数来代替([基本示例](https://github.com/jaredpalmer/razzle/tree/master/examples/with-firebase-functions))。也许这个话题本身就值得再写一篇文章。

目前，我的项目没有生根。如果你的项目需要它，你可以添加一个路由器或者你可以考虑使用 [After.js](https://github.com/jaredpalmer/after.js) 来代替，它基本上是 Razzle 的路由和相关功能。

我也期待着更新项目到 1。Material-UI 的版本，一旦他们发布它并添加回我非常喜欢的复选框动画。

## 带走

通过实验和/或阅读这篇文章，你可以以一种复杂的方式快速而容易地使用 Material-UI。所以继续努力，创造一些美好的东西吧！🎉

你会使用这种设置吗？你喜欢别的吗？

⬇ **回复**，**提问**，**在下面的评论中分享你的经历**！

你喜欢这篇文章吗？

⬇ 💖**心**文章！

**与**你的**朋友**和大学分享这个循序渐进的指南**！**

从地址栏复制链接。直接给你的同事发信息。👋打个招呼，把文章发给他们。

### 💌时事通讯

埃里克·恩吉

> **[立即订阅](https://my.sendinblue.com/users/subscribe/js_id/32wxl/id/2)** ，将可操作的技术文章放入您的收件箱。

埃里克只有在有重要事情要说的时候才会给你发邮件。就像你一样，他通常很忙。所以这只会时不时发生，你可以随时退订。

关注开发版上的[埃里克·恩吉和媒体版](https://dev.to/kireerik)上的[以获得关于新文章的通知。](https://medium.com/@erikengi)

### 与埃里克·恩吉一起工作

你或者你的公司有没有有价值的项目和产品？想要通过世界级的开发人员来扩展您的团队吗？

[立即雇佣](https://docs.google.com/forms/d/e/1FAIpQLSdRPRPFiYihG03lNUaKa1hU6z_BePrDwVCyV65S3ZLhvADyIw/viewform)–埃里克可以被雇佣

Erik 可以全职为你工作，也可以在你的公司工作。您或您的团队需要编程帮助吗？[预订 1:1 会议](https://docs.google.com/forms/d/e/1FAIpQLSdRPRPFiYihG03lNUaKa1hU6z_BePrDwVCyV65S3ZLhvADyIw/viewform?entry.600696459=mentorship)与埃里克！

* * *

*最初发表于 [codementor.io](https://www.codementor.io/kireerik/material-ui-get-started-fast-react-16-fjan7erke) 。*
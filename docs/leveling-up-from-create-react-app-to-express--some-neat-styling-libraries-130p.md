# 升级:从 Create-React-App 到 Express (&一些简洁的样式库)

> 原文：<https://dev.to/dan_mcm_/leveling-up-from-create-react-app-to-express--some-neat-styling-libraries-130p>

两个月后——是我写第二篇文章的时候了！你可以在这里 阅读我之前关于可视化斐波那契[的文章。](https://dev.to/daniel40392/visualizing-fibonacci-for-the-music-lover-in-you-2609)

[![Website Screenshot](img/39af7b451b018dc7a6b69f719f476c8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KWWUkZhD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mv5l79mtnvqpz8r65ahh.png) 
[链接到直播站点](https://cat-ui.herokuapp.com/)

## 项目

我是一名专业的软件工程师，直到今天，我仍然觉得大声说出来让我有点害怕。为了巩固我对自己技能和能力的信心，我一直在从事一些个人兼职项目，在这些项目中，我将构建应用程序的基本方法应用到一些有趣的兼职项目中。这是迄今为止关于我的经历的充满希望的系列文章中的第一篇。

我之前开发了几个 create-react-app 静态站点生成项目，我通过 GitHub pages 托管这些项目，作为一种简单的方式来发展我对 react 组件库的理解。对于这个项目，我决定采用 create-react-app 样板文件，将它绑定到 Express web 框架中，并在网上免费托管它(复制一个 GitHub 页面类型流),这样我就可以很容易地将它链接到我的 GitHub 存储库，供任何希望查看我的个人作品集的人使用(总价为...免费！).

我想要一种简单的方法来与一些公共 API 通信，但是我不想在我的客户端代码上公开我要通信的 API，我想把它保存在服务器端，以模拟与我自己的运行在机密端点上的 API 的通信。我发现了几个返回一些随机图像的 Cat APIs，所以我想我应该使用它们，看看我能得到什么。你可以在这里查看 API:

*   [卡特彼勒 API](http://thecatapi.com/)
*   [卡特彼勒即服务](https://cataas.com/)

终端应用程序简单地显示一只猫的图像，通过调用我们的后端 express 服务器(它调用 cat api ),并返回图像 url 以显示在主 web 页面上，该图像在单击/点击时会发生变化。我添加了一个额外的 Spotify 播放器，其中有一些随机的猫叫，作为一个额外的功能。你可以点击查看网站[的现场版本。](https://cat-ui.herokuapp.com/)

## 初始设置

我使用 [create-react-app](https://github.com/facebook/create-react-app) 作为我项目的起点。这是一个非常友好的样板文件，它将为您提供开始制作一个基本的 React 应用程序所需的一切。在一个更专业的环境中，它有一些与它的 webpack 配置的定制相关的缺点，但对于这个项目的目的来说，它已经足够了。一旦安装完毕，只需运行三个命令就能启动你的应用:

```
create-react-app my-app
cd my-app
npm start 
```

Enter fullscreen mode Exit fullscreen mode

太棒了，现在我们可以运行应用程序了！

为了处理应用程序的一些样式方面，我安装了两个额外的包:

*   [styled-定制元素样式的组件](https://www.styled-components.com/)。
*   [网格风格](http://jxnblk.com/grid-styled/)通过 flexbox 实现响应式设计风格

如果你是 React 和设计一些基本 UI 前端工作的新手，我强烈推荐你查看这两个库。下面是这些库的主要用途的一点总结:

### 样式组件

样式化的常见问题，至少在我从事 web 开发期间，是 css 应用程序样式化的全局性质(至少在 create-react-app 框架中)使得很难在组件级别上定制 CSS。

样式组件库允许您依赖组件定义的样式。我自己的代码中的一个例子是这样的:

```
import styled from "styled-components";

const CatPic = styled.img`
  margin: auto;
  background-color: white;
  max-width: 60vw;
  max-height: 60vh;
  margin: 2em;
  border-radius: 10px;
  &:hover {
    cursor: pointer;
  }
  border: 0.2em solid lightgrey;
`; 
```

Enter fullscreen mode Exit fullscreen mode

当在我的 React 组件中使用它而不是使用一个`<img/>`标签时，我只需要使用一个`<CatPic/>`标签，它就会导入上面定义的自定义样式。

### 网格样式化

Grid styled 为我提供了一个简单的解决方案，让我的主要 Cat 组件在全分辨率下占据 3/4 的屏幕，让我的 Spotify 组件占据 1/4 的屏幕。然后，当分辨率更改为手机风格的大小时，它会翻转并允许全幅显示 Cat 组件和全幅显示 spotify 组件。这是一个简单的移动响应设计的天赐良机。不再自动计算像素宽度！

该布局的简化版本如下所示:

```
<Flex flexWrap="wrap">
  <Box p={3} width={[1, 3 / 4]}>
    ... cat component here
  </Box>
  <Box p={3} width={[1, 1 / 4]}>
    ... spotify component here
  </Box> </Flex> 
```

Enter fullscreen mode Exit fullscreen mode

## 整合快递

现在进入更具挑战性的部分。安装 Express ( `npm install express`)后，我在主项目目录中创建了一个 server.js 文件，如下图所示

```
const express = require("express");
const bodyParser = require("body-parser");
const path = require("path");

const app = express();

app.use(express.static(path.join(__dirname, "build")));

app.get("/", function(req, res) {
  res.sendFile(path.join(__dirname, "build", "index.html"));
});

app.listen(process.env.PORT || 8080); 
```

Enter fullscreen mode Exit fullscreen mode

设置好这个文件后，我必须修改 package.json 文件
中的一些脚本

```
"scripts":  {  "dev":  "react-scripts start",  "start":  "node server.js",  "build":  "react-scripts build",  "test":  "react-scripts test --env=jsdom",  "eject":  "react-scripts eject"  } 
```

Enter fullscreen mode Exit fullscreen mode

这些新脚本意味着当开发人员运行 npm start 命令而不是运行标准的 react-scripts start 命令时，它将运行`node server.js`。上面脚本的主要内容在 app.get("/")部分，这实际上意味着当 dev 在运行`npm run start` Express 后转到端口 8080 时，会将个人定向到主 react 应用程序的 index.html 文件。

此时需要注意的重要一点是，为了创建有效的 index.html，您需要在`npm run start`之前运行`npm run build`。我添加了一个名为“dev”的额外脚本，它直接在端口 3000 上运行应用程序，而不是每次进行本地更改时都必须等待运行新的构建并启动它。这是一个很好的工作，可以在不关心任何其他服务器问题的情况下开发应用程序特性。

我对 package.json 所做的另一个必要更改是包含了`"proxy": "http://localhost:8080"`，它将 Express 服务器代理到端口 8080，因此当服务在生产中运行时，当应用程序在 URL online 上公开时，任何其他端点设置(即 app . get(" any-other-endpoint-here ")都将是可访问的。

## 前端到后端的通信

我在 server.js 文件中添加了一个额外的端点:

```
app.get("/cat", function(req, res) {
  axios
    .get("http://thecatapi.com/aimg/get")
    .then(data => res.send(data.request.res.responseUrl))
    .catch(err => console.log(err));
}); 
```

Enter fullscreen mode Exit fullscreen mode

我设置了一个 Cat 组件来查询这个端点，并将解析后的 responseUrl 用作 img 标记的 src(通过组件状态设置)。你还会在下面看到，在初始页面加载时，我把查询猫的 url 状态作为一个服务端点——只是增加了一些变化:

```
import React, { Component } from "react";
import styled from "styled-components";
import axios from "axios";

const CatPic = styled.img`
  margin: auto;
  background-color: white;
  max-width: 60vw;
  max-height: 60vh;
  margin: 2em;
  border-radius: 10px;
  &:hover {
    cursor: pointer;
  }
  border: 0.2em solid lightgrey;
`;

class Cat extends Component {
  constructor() {
    super();
    this.state = {
      url: "https://cataas.com/cat" //default image - calling another cat api!
    };
    this.getCat = this.getCat.bind(this);
  }

  getCat() {
    axios
      .get("/cat")
      .then(data => this.setState({ url: data.data }))
      .catch(data => console.log(data));
  }

  render() {
    return (
      <div>
        <CatPic src={this.state.url} onClick={this.getCat} />
        <div />
      </div>
    );
  }
}

export default Cat; 
```

Enter fullscreen mode Exit fullscreen mode

## 英雄集成

所以到目前为止，当我在做这个项目的时候，我把所有的改变都提交给了我的本地 GitHub 库，还没有选择一个平台来服务这个应用程序。我想尝试几个服务，包括 Heroku、Firebase 和 AWS 静态托管。为了找到一个简单又便宜的托管解决方案，我最终尝试了 Heroku。

我设置了我的 Heroku 项目并对其进行了配置，这样当我对项目的主分支进行新的提交时，它将自动部署新的构建并公开暴露服务。理想情况下，将来我会添加一些 [CircleCI](https://circleci.com/) 测试和受保护的主分支设置，以防止直接将更改推送到主分支。为了这个副业的目的，目前的设置是好的，但任何推出的错误将几乎立即在网站上直播。在 [Heroku](https://devcenter.heroku.com/articles/github-integration) 上可以找到一些设置这种配置的说明，如果你对这个过程有任何问题，欢迎在下面评论。

## 最终结果

该应用程序目前在[这里](https://cat-ui.herokuapp.com/)上公开。如果你有兴趣看一下或者用它作为你自己的 create-react-app / Express 混合应用程序的样板，代码库可以在这里[获得](https://github.com/daniel40392/cat-ui)！

## 进一步改进

当前的实现有很多可以改进的地方，包括:

*   在本地开发服务时，使用节点开发环境来自动切换用于 express 通信的 URL
*   在后端调用的 API 之间切换
*   API 返回数据的处理->通常响应是一个图像字节流或一些类似的结构，可以处理成直接显示在主页上的图像
*   安全证书错误-似乎在一些 Mac 设备上 Chrome 有一些相关的问题，所以页面上的 SSL 证书，我怀疑这可以通过一些额外的 Heroku 配置来修复

## 你的想法&问题？

请在下面自由发表您的想法或问题。我很有兴趣看看上面的内容是否对那些刚开始使用 react 并希望开始使用像 express with create-react-app 这样的服务的人有用。

有各种各样的样板可供使用，比这个设置更好，但是这个项目的目的是展示如何使用 Express 后端从基本的 create-react-app 到更复杂的东西。这是一个在后端调用 API 的非常简单的例子，但是您可以想象如何将它带到下一个层次，通过 Express 服务器进行更复杂的计算或与私有服务的通信。

下次见！
# 做一只刺猬-第一部分

> 原文：<https://dev.to/booyaa/web-app-development-core-skill-hedgehog---part-one-2144>

*照片由[柳德米拉·德尼修](https://unsplash.com/photos/ULXu46fVCHY)在 Unsplash* 上拍摄

*这是我的“写作业”博客系列的一部分，介绍性的文章可以在[这里](https://dev.to/booyaa/blogging-my-homework-3ohf)找到。*

购买者自负:围绕概念的任何错误或误解几乎肯定是我自己的，而不是我的员工(制造技术)。这毕竟是我自己的学习，在学习过程中确实会出现错误。

# Web App 开发核心技能一级又名刺猬

顾名思义，这项核心技能是基于 Web 应用程序开发的。它围绕着 React 框架，因为这是我们在 [Made Tech](https://www.madetech.com/) 为前端工作选择的框架。

拥有 React 核心技能的 [Web 应用程序开发的第](https://learn.madetech.com/core-skills/web-application-development-with-react/)[级](https://learn.madetech.com/core-skills/web-application-development-with-react/#hedgehog)主要处理建立一个准备投入生产使用的新项目。

核心技能评估分为两部分:实践演示(应用)和问答形式的理论(理解)。我也相应地拆分了博客帖子，所以第一部分将是实际演示。

## 假设

您已经安装了[节点](https://nodejs.org/en/)和[停靠站](https://www.docker.com/)。

## 我们的应用

作为核心技能的一部分，你需要开发一个应用程序。我的选择是创建一个指导呼吸的应用程序。为什么选择这个应用程序？嗯，有时候，你需要喘口气，当一切变得有点多。

将会有更多的细节，但是现在，应该从以下几个方面来考虑:

*   用户界面
    *   有一个设置面板，允许您:
    *   定义吸气和呼气以及屏住呼吸之间的时间间隔。
    *   设定声音的音量
    *   有一个计时器来定义多久，你希望有你的呼吸指导会议。
*   行为
    *   有一个倒计时器
    *   当引导呼吸时，有视觉和听觉提示

## 设置

*   使用`npx create-react-app breathe`创建我们的应用程序
*   进入我们的应用程序`cd breathe`
*   启动我们的应用程序`npm start`

`npx`是安装和运行`create-react-app`的简便方法。

## 申请

评估的实践部分包括六个方面，您将在这六个方面接受评估。我为每个区域创建了一个标题。

### 创建了一个独立于候选人机器上的节点安装的应用程序

在这里，我们被评估是否能够提供一个可重复的构建。Docker 是我们的首选方式，也有其他选择。

```
FROM node:10
WORKDIR /app
COPY package.json ./
RUN npm install
CMD ["npm", "start"] 
```

Enter fullscreen mode Exit fullscreen mode

这个`Dockerfile`背后的一般思想是使用 Node 定义一个映像，复制`package.json`，安装它的依赖项并启动应用程序。

提示:在原型开发的早期阶段，我喜欢将`—verbose`添加到我的`npm install`中

Info:我在下面的参考资料部分提供了更多关于`Dockerfile`语法的信息。

提示:如果您的应用程序有许多`Dockerfile`或相关的脚本，您可能会发现将应用程序和 Docker 工件分开是明智的。

最后，我们构建并测试我们的 dockerized 应用程序。

```
docker build -t . <DOCKER_ID/MY_APP>
docker run -it --rm -p 3000:3000 -v ${PWD}/public:/app/public -v ${PWD}/src:/app/src <DOCKER_ID/MY_APP> 
```

Enter fullscreen mode Exit fullscreen mode

应用程序运行完毕后，您可以使用`http://localhost:3000`
[![Starting up React](../Images/9e67e78d03ce508c2d3caeaadb7352e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B5RwCe5---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://booyaa.wtf/img/hedgehog-001-start.png) 打开网站

提示:如果你做过任何类型的 web 开发，你会注意到 port `3000`很流行。确保刷新与此地址相关的 cookies，以避免意外行为。

你可能想知道为什么我们使用卷挂载(`-v`)这是为了支持热重新加载(这是一个要求)。

`docker run`感觉有点笨拙，所以让我们使用一个`docker-compose.yml`文件来简化 docker 调用。

```
version: '2'
services:
  web:
    build: .
    volumes:
      - ./public:/app/public
      - ./src:/app/src
    ports:
      - "3000:3000" 
```

Enter fullscreen mode Exit fullscreen mode

我们将再次测试。

```
docker-compose build web
docker-compose run --rm --service-ports web 
```

Enter fullscreen mode Exit fullscreen mode

参考

*   [将 Node.js web 应用程序| Node.js](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)
*   [Docker 文件参考| Docker 文档](https://docs.docker.com/engine/reference/builder/)
*   [撰写文件版本 2 参考| Docker 文档](https://docs.docker.com/compose/compose-file/compose-file-v2/#service-configuration-reference)

### 有一个 make recipe for serve，它在开发模式下运行应用程序

虽然命令历史很棒，但你希望构建和启动你的应用程序的过程是零努力。为了做到这一点，我们利用了`Makefile` s.

```
PHONY:docker-build
docker-build:
    docker-compose build web

PHONY:serve
serve:    
    docker-compose run --rm --service-ports web 
```

Enter fullscreen mode Exit fullscreen mode

如果您想知道我们为什么选择这些目标名称，(`docker_build`和`serve`)，这是因为我们决定为我们的`Makefile`定义一个标准。这是为了确保任何工程师都应该能够在没有任何文档的情况下知道如何构建、服务和测试应用程序。你可以在我们的请求评论报告中看到我们的`Makefile`标准: [Makefile 标准](https://github.com/madetech/rfcs/blob/master/rfc-012-makefile-standards.md)。

如果你想知道为什么我们不把我们的目标放在`package.json`中，那是因为不是所有的项目都是基于节点的。而 Makefile 可以一直存在于任何解决方案中。

同样，我们可以通过以下调用来测试这两个目标:

```
make docker_build
make serve 
```

Enter fullscreen mode Exit fullscreen mode

### 有一个 make recipe for test，它为应用程序运行测试

将`test`加到`Makefile`的时间。我们的`Makefile`现在长这样:

```
PHONY:docker-build
docker-build:
    docker-compose build web

PHONY:serve
serve:    
    docker-compose run --rm --service-ports web

PHONY: test
test:
    docker-compose run --rm web npm test 
```

Enter fullscreen mode Exit fullscreen mode

提示:如果您想要单次测试运行，也就是说，您不想使用监视进程，那么添加一个名为`CI`的环境变量，其值为`true`到`docker-compose.yml`的`web`部分

为了测试我们的目标测试，我们键入`make test`

### 有一个沙盒环境，允许他们快速构建组件原型

针对这一需求，我们安装了 Storybook。

```
npm i -g @storybook/cli
storybook init # assumes we're still in our app's folder 
```

Enter fullscreen mode Exit fullscreen mode

我们还将以下脚本添加到`package.json`

```
 "storybook":  "start-storybook -p 9009 -s public",  "build-storybook":  "build-storybook -s public" 
```

Enter fullscreen mode Exit fullscreen mode

参考

*   【React 故事书入门

### 能够根据变化进行热重装

您可以使用 React 获得开箱即用，这是通过 react-scripts 完成的。如果您已经通过`make serve`开始，您可以通过更改`src/App.js`中的文本看到这一点

参考

*   [在服务器启动问题# 873 Facebook/create-react-app GitHub 中禁用打开浏览器](https://github.com/facebook/create-react-app/issues/873)
*   [配置 create-react-app 而不弹出⏏–kitze–medium](https://medium.com/@kitze/configure-create-react-app-without-ejecting-d8450e96196a)
*   [主 Facebook/create-react-app GitHub 上的 create-react-app/readme . MD](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md)

### 运行 make test 时出现测试失败

我们可以通过抛出一个异常来编写一个失败的测试，这会导致测试立即失败！

[![React Test Failing](../Images/acd02aeca3459820b5127eba3a77d6a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uB6A9ks9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://booyaa.wtf/img/hedgehog-002-test-fail.png)

[![booyaa image](../Images/0856925e87024eeeb56184e23bf8ed45.png)](/booyaa) [## 在博客上写我的作业

### 标记 Sta Ana Sep 16 ' 181min read

#study #learning #fullstack](/booyaa/blogging-my-homework-3ohf)[![booyaa image](../Images/0856925e87024eeeb56184e23bf8ed45.png)](/booyaa) [## 做一只刺猬-第二部分

### 标记 Sta Ana Sep 21 ' 186min read

#learning #study #react #webappdev](/booyaa/web-app-development-core-skill-hedgehog---part-two-3p1i)[![booyaa image](../Images/0856925e87024eeeb56184e23bf8ed45.png)](/booyaa) [## 像狐狸一样狡猾

### 标记 Sta Ana Sep 28 ' 184min read

#study #learning #react #frontend](/booyaa/web-app-development-core-skill-fox-3ij5)
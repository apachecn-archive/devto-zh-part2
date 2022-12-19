# 优化 Docker 图像用于生产的速成课程

> 原文：<https://dev.to/adnanrahic/a-crash-course-on-optimizing-your-docker-images-for-production-2f9l>

[![This months sponsor is Zeet.](../Images/a4da9440f5519d3156594813d3a02017.png)T2】](https://bit.ly/adnan-zeet)

免责声明: [Zeet](https://bit.ly/adnan-zeet) 将在下个月赞助这篇博客。前几天我试过了。这就像无服务器，但运行整个后端。你可以自动托管和扩展应用。相当整洁。

* * *

你不讨厌部署你的应用需要很长时间吗？单个容器映像超过 1gb 并不被视为最佳实践。每次你部署一个新版本的时候都推几十亿字节对我来说听起来不太对。

### TL；速度三角形定位法(dead reckoning)

本文将向您展示如何优化 Docker 图像的几个简单步骤，使它们更小、更快、更适合生产。

目的是向您展示使用默认 Node.js 图像和它们的优化对应图像之间的大小和性能差异。这是议程。

*   为什么是 Node.js？
*   使用默认 Node.js 图像
*   使用 Node.js Alpine 图像
*   排除开发依赖项
*   使用基本的阿尔卑斯山图像
*   使用多阶段构件

让我们跳进来。

### 为什么是 Node.js？

Node.js 是目前最通用和初学者友好的后端入门环境，我把它作为我的主要语言编写，所以你必须忍受它。告我吧，对。😙

作为一种解释型语言，JavaScript 没有编译目标，比如 Go。您无法减少 Node.js 图像的大小。还是有？

我来证明这是错误的。为工作选择正确的基础映像，只为生产映像安装生产依赖项，当然，使用多阶段构建都是可以大幅减轻映像重量的方法。

在下面的例子中，我使用了我不久前编写的一个简单的 [Node.js API](https://github.com/adnanrahic/boilerplate-api) 。

### 使用默认的 Node.js 图像

当然，从一开始，我使用了默认的 Node.js 映像，它来自 Docker hub 的[。哦，我是多么的无知。](https://hub.docker.com/) 

```
FROM node
WORKDIR /usr/src/app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"] 
```

Enter fullscreen mode Exit fullscreen mode

想猜猜尺寸吗？我惊讶得目瞪口呆。 **727MB** 一个简单的 API！？

[![](../Images/db7670255b2371ae81efa88faff9411d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wx0QS_sJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3Gvi08t7bP-agD26yk2VLQ.png)

请不要这样做。你不需要这么做，真的，不要。

### 使用 Node.js 高山影像

最简单快捷的大幅缩减图像尺寸的方法是选择一个更小的基础图像。Alpine 是一个小型的 Linux 发行版，可以完成这项工作。只要选择 Alpine 版本的 Node.js 就会显示出巨大的提升。

```
FROM node:alpine # adding the alpine tag
WORKDIR /usr/src/app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"] 
```

Enter fullscreen mode Exit fullscreen mode

整整小了六倍！下降到 **123.1MB** 。这还差不多。

[![](../Images/010681cf746aed9df5b17014afe50e70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qiss6V5J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhLJYuKXsRHMdCtKUhk14nA.png)

### 排除开发依赖

嗯……但是我们肯定还能做些别的什么。我们正在安装所有依赖项，尽管我们只需要最终映像的生产依赖项。我们改变一下怎么样？

```
FROM node:alpine
WORKDIR /usr/src/app
COPY package.json package-lock.json ./
RUN npm install --production # Only install prod deps
COPY . .
EXPOSE 3000
CMD ["node", "app.js"] 
```

Enter fullscreen mode Exit fullscreen mode

我们走吧。我们又砍掉了 30MB！现在降到了 **91.6MB** 。我们有进展了。

[![](../Images/83c506707f7de14ee5a1a9ee9736954d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JvHMaJ4M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASktWUx5Y1y8O4PirwqSvFw.png)

这让我感到非常自豪，我准备就此收工。但是我突然想到。如果我从原始的阿尔卑斯山图片开始呢？如果我自己抓取基本 Alpine 映像并安装 Node.js，它可能会更小。我是对的！

### 使用基地高山影像

你可能会认为像这样的改变不会有什么影响，但它比之前的版本又减少了 20MB。

```
FROM alpine # base alpine
WORKDIR /usr/src/app
RUN apk add --no-cache --update nodejs nodejs-npm # install Node.js and npm
COPY package.json package-lock.json ./
RUN npm install --production
COPY . .
EXPOSE 3000
CMD ["node", "app.js"] 
```

Enter fullscreen mode Exit fullscreen mode

降到现在的 **70.4MB** 。这比我们开始的地方小了 10 倍！

[![](../Images/7b41c3daebbac209c4367b2eaffebb4b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7zrgdqWn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A31zcUa1Qi34tnV-_3OMTVQ.png)

我们现在能做的不多了，对吧？对不对…？

### 使用多级构建

嗯，实际上，有。让我们谈一谈层次。

每个 Docker 图像都是由层构建而成的。每一层都是 docker 文件中的一个命令。这是上面的文件:

```
FROM alpine # base alpine
WORKDIR /usr/src/app
RUN apk add --no-cache --update nodejs nodejs-npm # install Node.js and npm
COPY package.json package-lock.json ./
RUN npm install --production
COPY . .
EXPOSE 3000
CMD ["node", "app.js"] 
```

Enter fullscreen mode Exit fullscreen mode

FROM 指令创建一个层，WORKDIR 也是，RUN 也是，等等。所有层都是只读的，除了最后一层 CMD，它是一个可写层。只读图层可以在容器之间共享，这意味着一个图像可以在容器之间共享。

这里发生的事情是 Docker 使用存储驱动程序来管理只读层和可写容器层。一旦容器被删除，这是被删除的短暂层。很酷的东西。但是为什么这很重要呢？

通过减少层数，我们可以得到更小的图像。这就是使用多阶段构建的切入点。

```
FROM  alpine  AS  multistage
WORKDIR /usr/src/app
RUN apk add --no-cache --update nodejs nodejs-npm
COPY package.json package-lock.json ./
RUN npm install --production
​
#
​
FROM alpine
WORKDIR /usr/src/app
RUN apk add --no-cache --update nodejs
COPY --from=multistage /usr/src/app/node\_modules ./node\_modules
COPY . .
EXPOSE 3000
CMD ["node", "app.js"] 
```

Enter fullscreen mode Exit fullscreen mode

我们只使用第一个映像来安装依赖项，然后在我们的最终映像中，我们复制所有 node_modules，而不构建或安装任何东西。我们甚至可以跳过在最终映像中安装 **npm** ！

想猜猜最终尺寸吗？去吧！

[![](../Images/a604c51d2b45d68e4c31d787bead194d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lEm2-Ppg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AW-3Dyv-HSeBvZVoXfoySFw.png)

我要说我们做得很好，将它降到了 48.6 MB(T1)，这是 T2 的 15 倍(T3)，是一件值得骄傲的事情。

### 判决结果

别天真了，绝对没有理由在生产中使用千兆字节大小的图像。一个很好的第一步是使用一个微小的基础图像。从小处着手，循序渐进。

通过选择优化的基本图像，你会走得很远。如果你真的需要部署速度的提升，并且被缓慢的 CI/CD 管道所困扰，看看[多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)。你以后不会想用其他方式。

***注意*** *:我确实遗漏了一个示例，其中包含了部署到生产之前运行测试的开发依赖项，因为它与生产中运行的最终规模缩减无关。当然，这是一个有效的用例！欢迎在下面的评论中加入你的想法。我很想听听你的想法！*

如果你想查看我以前关于 Docker 和 Kubernetes 的 DevOps 相关文章，请随意前往[我的个人资料](https://dev.to/adnanrahic)。

希望你们喜欢读这篇文章，就像我喜欢写这篇文章一样。你觉得这个教程会对某个人有帮助吗？不要犹豫分享。如果你喜欢它，粉碎下面的独角兽，这样其他人就会在 DEV.to. 上看到它

* * *
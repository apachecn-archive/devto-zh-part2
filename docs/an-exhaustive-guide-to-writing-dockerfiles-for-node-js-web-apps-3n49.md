# 为 Node.js Web 应用程序编写 docker 文件的详尽指南

> 原文：<https://dev.to/hasurahq/an-exhaustive-guide-to-writing-dockerfiles-for-node-js-web-apps-3n49>

[![](img/190ee01b0784fd3b3862579237c64e53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YsqTw6IE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/an-exhaustive-guide-to-writing-dockerfiles-for-node-js-web-apps-bbee6bd2f3c4/1-4KhmpXFJ_Etczs6awRnAbg.png)

## TL；速度三角形定位法(dead reckoning)

这篇文章充满了从简单的 docker 文件到 Node.js web 应用程序的多阶段生产构建的例子。以下是本指南内容的快速总结:

*   使用适当的基础映像(carbon 用于开发，alpine 用于生产)。
*   在开发期间使用`nodemon`进行热重装。
*   优化 Docker 缓存层——按照正确的顺序放置命令，以便仅在必要时执行`npm install`。
*   使用`serve`包提供静态文件(通过 React/Vue/Angular 生成的包)。
*   使用多阶段`alpine`构建来缩小生产的最终图像尺寸。
*   #ProTips — 1)使用 COPY over ADD 2)使用`init`标志处理 CTRL-C 内核信号。

如果你想直接跳到代码，请查看 [GitHub repo](https://github.com/praveenweb/node-docker) 。

## **内容**

1.  简单的 Dockerfile 文件和。dockerignore
2.  使用 nodemon 热重装
3.  优化
4.  提供静态文件
5.  单阶段生产制造
6.  多阶段生产制造

让我们假设一个简单的目录结构。该应用程序名为 node-app。顶层目录有一个`Dockerfile`和`package.json`你的节点 app 的源代码会在`src`文件夹里。为了简洁起见，我们假设 server.js 定义了一个运行在端口 8080 上的 node express 服务器。

```
node-app
├── Dockerfile
├── package.json
└── src
    └── server.js 
```

## **1。简单的 Dockerfile 文件示例**
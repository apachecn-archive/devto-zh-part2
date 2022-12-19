# 关于使用 JSON web 令牌保护无服务器 API 的速成班

> 原文：<https://dev.to/adnanrahic/a-crash-course-on-securing-serverless-apis-with-json-web-tokens-22fa>

多么拗口的标题。你同意吗？在本演练中，您将了解如何使用 JSON web 令牌保护您的无服务器端点。

这将包括一个带有几个端点的无服务器 REST API 的基本设置，当然还有一个**授权器**功能。这个**授权器**将作为授权访问您的资源的中间件。

在创建过程中，我们将使用[无服务器框架](https://serverless.com/)来模拟开发环境，就像您习惯的那样。在本指南的最后，我们还将设置一个名为 [Dashbird](https://dashbird.io/) 的监控工具。它将允许我们以一种自然且易于理解的方式模拟常规 Node.js 应用程序的调试功能和概述。

如果我上面提到的任何东西对你来说是新的，不要担心。下面我会一一解释。否则，您可以通过查看这些教程来更新您的知识:

*   [使用 JWT 保护 Node.js RESTful APIs 认证和授权说明。](https://dev.to/adnanrahic/securing-nodejs-restful-apis-with-json-web-tokens)
*   [node . js 无服务器速成班](https://dev.to/adnanrahic/a-crash-course-on-serverless-with-nodejs-5jp) —无服务器基础讲解。
*   [用 Node.js 和 MongoDB 构建无服务器 REST API](https://dev.to/adnanrahic/building-a-serverless-rest-api-with-nodejs-and-mongodb-43db)—无服务器 REST API 讲解。

### TL；速度三角形定位法(dead reckoning)

在 head first 跳入之前，你可以严重伤害我的感情，只看这个 TL；Or 博士，继续看整篇文章。❤

*   [**创建 API**T3】](https://medium.com/p/ff657ab2f5a5#2aa5)
    *   [添加数据库](https://medium.com/p/ff657ab2f5a5#8132)
    *   [添加功能](https://medium.com/p/ff657ab2f5a5#e344)
    *   [为用户添加业务逻辑](https://medium.com/p/ff657ab2f5a5#5845)
    *   [添加认证](https://medium.com/p/ff657ab2f5a5#5663)
    *   [添加授权](https://medium.com/p/ff657ab2f5a5#40d6)
*   [**部署**](https://medium.com/p/ff657ab2f5a5#52e1)
*   [**检测**](https://medium.com/p/ff657ab2f5a5#2e10)
*   [**监控**](https://medium.com/p/ff657ab2f5a5#6e91)

准备好了吗？让我们跳进来吧！

### 创建 API

首先，我们需要为本地开发环境建立无服务器框架。这个框架是所有与无服务器架构相关的事情的事实上的框架。跳转到[他们的网站](https://serverless.com/)，按照指示进行设置，或者参考我在上面链接的文章返回[。](https://dev.to/adnanrahic/a-crash-course-on-serverless-with-nodejs-5jp)

安装过程非常简单。您在 AWS 帐户中设置了一个 AWS 管理角色，并将其链接到无服务器框架的安装。实际的安装过程只是运行一个简单的命令。

打开终端窗口，运行下面的命令。

```
$ npm install -g serverless 
```

Enter fullscreen mode Exit fullscreen mode

继续，一旦安装了它，只需在终端中运行一个命令，就可以在本地开发机器上获得样板无服务器服务。

```
$ sls create -t aws-nodejs -p api-with-auth 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将生成您需要的样板代码。

转到新创建的名为 **api-with-auth** 的目录，用您选择的代码编辑器打开它。

```
$ cd api-with-auth 
```

Enter fullscreen mode Exit fullscreen mode

一旦打开，你会看到两个主要文件。一个`handler.js`和一个`serverless.yml`文件。`handler.js`包含我们的应用程序逻辑，而`serverless.yml`定义我们的资源。

现在是时候安装一些依赖项了，以便为数据库交互设置我们需要的认证/授权方法、密码加密和 ORM。

```
$ npm init -y 
$ npm install --save bcryptjs bcryptjs-then jsonwebtoken mongoose 
```

Enter fullscreen mode Exit fullscreen mode

这是我们生产所需要的，但对于开发，我们将抓住无服务器离线插件。

```
$ npm install --save-dev serverless-offline 
```

Enter fullscreen mode Exit fullscreen mode

可爱！

#### 添加数据库

对于持久性数据存储，我们将在 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) 上获取一个托管的 MongoDB 实例。[这里有一篇我详细解释过的文章的参考。](https://dev.to/adnanrahic/building-a-serverless-rest-api-with-nodejs-and-mongodb-43db)

在服务文件夹的根目录下，让我们创建一个`db.js`文件来保存数据库连接的逻辑。继续粘贴这段代码。
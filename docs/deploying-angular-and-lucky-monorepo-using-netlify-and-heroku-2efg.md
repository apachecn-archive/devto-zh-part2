# 使用 Netlify 和 Heroku 部署 Angular 和 Lucky Monorepo

> 原文：<https://dev.to/mikeeus/deploying-angular-and-lucky-monorepo-using-netlify-and-heroku-2efg>

我喜欢将我的客户端和服务器放在一起，因为它允许我在一个 VS 代码工作区中打开两个应用程序，这让我更有效率。当单独部署应用程序时，困难就来了，但幸运的是，我们可以使用 Netlify 和 Heroku 这样的服务来简化我们的工作！

作为参考，[我的博客](https://mikias.net/blog)是用 Lucky 搭建的 JSON Api 和 Angular 搭建的前端。你可以看看 github 上的[源代码，了解如何一起部署这些应用程序，使用 nrwl 和 ngrx 与 Angular 或构建和测试我的幸运 API。](https://github.com/mikeeus/portfolio)

文件夹结构是这样的:

```
/client         # Angular Front End
/server         # Lucky Api Back End
/bin
  /deploy_server 
```

Enter fullscreen mode Exit fullscreen mode

## 运气好上 Heroku

Heroku 不允许使用非根目录发布你的应用程序。我通过使用从 coderwall 上的[这篇文章中学到的`git subtree`方法解决了这个问题，它允许我们部署 git 库的子目录。](https://coderwall.com/p/ssxp5q/heroku-deployment-without-the-app-being-at-the-repo-root-in-a-subfolder)

### 准备部署

首先创建 heroku 应用程序，添加 buildpack 并设置 Lucky 应用程序在 Heroku 上运行所需的环境变量。你可以在幸运指南中找到[的完整说明，唯一的区别是**对于幸运 Api，你只需要 crysal buildpack。**](https://luckyframework.org/guides/deploying-heroku/)

### 部署！

现在关键的区别在于我们如何部署到 heroku。我们将使用`git subtree`而不是`git push heroku master`来仅推送我们回购的子目录。

```
git subtree push --prefix server heroku master 
```

Enter fullscreen mode Exit fullscreen mode

将`--prefix`参数更改为子目录的名称。

为了方便起见，我将该命令放在一个文件中的`bin/deploy_server`处，并用`chmod u+x`使其可执行。

## 棱角分明

Netlify 无疑是我最喜欢的部署静态应用程序的服务。我把它用于我所有的 Angular 应用程序，即使在免费层，它也有很棒的功能。

### 勾搭上饭桶

要进行部署，我们只需登录 netlify，转到位于`app.netlify.com/account/sites`的站点页面，然后选择“从 Git 新建站点”按钮。

这将提示您使用 Github 或 Bitbucket 登录，然后选择要链接的存储库。一旦完成，它将监视推送到回购的事件，并自动部署您的应用程序，当然是使用魔法。

开玩笑的。没有魔法这种东西。我们实际上需要告诉 Netlify 如何构建我们的应用程序，以及在哪里可以找到需要服务的`index.html`。

### 配置基础目录

由于我们的应用程序位于子目录中，我们可以在根目录中创建一个`package.json`，并使用一个`"postinstall"`脚本来触发 npm 安装和构建(例如:`"postinstall": "cd client && npm install"`)，然后使用类似`"build": "cd client && ng build..."`的部署命令。

这在第一次部署时会很好，但是由于 netlify 缓存像`node_modules`这样的目录的方式，它不会缓存子目录中从`package.json`安装的包，并且后续的部署会失败。为了解决这个问题，我们需要创建一个`netlify.toml`文件来设置我们的应用程序的基本目录，指定我们的构建命令和发布目录。这将使 netlify 正确缓存我们的`node_modules`文件夹，并允许我们的构建脚本运行。

该文件将如下所示:

```
# netlify.toml

# Global settings applied to the whole site.  
# 
# “publish” is the directory to publish (relative to root of your repo),
# “command” is your build command,
# “base” is directory to change to before starting build. if you set base:
#    that is where we will look for package.json/.nvmrc/etc not repo root!

[build]
  base    = "client"
  publish = "client/dist/apps/portfolio"
  command = "npm run build-netlify" 
```

Enter fullscreen mode Exit fullscreen mode

**注意**发布目录路径需要从根目录设置，而构建命令将从基目录运行(在本例中为`client/`)

您可以在[网络文档](https://www.netlify.com/docs/continuous-deployment/#deploy-contexts)中找到更多配置选项。

### 构建 App

`npm run build-netlify`实际上扩展为:`ng build --prod --build-optimizer && cp _redirects dist/apps/portfolio`。当运行单页面应用程序时，你需要将每个非根路径重定向回你的`index.html`，否则 netlify 将显示一个我们不想要的 404 页面。

我们使用一个简单配置的`_redirects`文件来处理这个问题:

```
# client/_redirects
/* /index.html 200 
```

Enter fullscreen mode Exit fullscreen mode

### 部署！

现在，要部署我们的 Angular 应用程序，我们只需要使用:`git push origin master`推送到我们的 git 存储库。

## 目录结构

我们最终的目录结构如下所示:

```
/client         # Angular Front End
  /_redirects
  /package.json
  /...
/server         # Lucky Api Back End
  /...
/bin
  /deploy_server
netlify.toml 
```

Enter fullscreen mode Exit fullscreen mode

## TL；速度三角形定位法(dead reckoning)

**heroku 构建命令:**

*   使用幸运向导的[指令设置 heroku，但是记住**不要添加 nodejs 构建包**](https://luckyframework.org/guides/deploying-heroku/)
*   使用`git subtree push --prefix server heroku master`部署

**netlify:**

*   通过连接到您的应用程序 git repo 来创建 Netlify 应用程序
*   使用以下内容创建`netlify.toml`配置:

```
[build]
  base    = "client"
  publish = "client/dist/apps/portfolio"
  command = "npm run build-netlify" 
```

Enter fullscreen mode Exit fullscreen mode

*   在你的角根中创建`_redirects`文件，如下:`/* /index.html 200`
*   像这样在你的 Angular 应用中创建一个构建脚本:`"build-netlify": "ng build --prod --build-optimizer && cp _redirects dist/apps/portfolio"`
*   部署方式:`git push origin master`

## 加入我们

我希望你喜欢这个教程，并发现它很有用。加入我们的 [Lucky gitter 频道](https://gitter.im/luckyframework/Lobby)了解框架的最新进展，或者[查看文档](https://luckyframework.org/guides)了解如何通过 Lucky 将您的应用理念付诸实践的更多信息。
# 将我的第一个 React web 应用程序部署到 Firebase🔥

> 原文：<https://dev.to/sathish/deploying-my-first-react-web-app-to-firebase---36bd>

那是在一个周六的下午，当时我正为无法将我的项目部署到服务器上而头疼。我对 React 和前端世界还很陌生，在过去的一个月里，我一直在 Udemy 学习 React。

学完一半课程，我决定用学到的知识做一个简单的 app。我把它命名为 [**谁在乎呢？**](https://who-cares04.firebaseapp.com/) 这你可以在 [**GitHub**](https://github.com/TheWebDevel/whocares) 上找到。我希望把这个项目发布到网上，这样我就可以把它展示给我的朋友。这就是一切开始的地方。

## 祸不单行

这是我第一次使用 [**Webpack**](https://webpack.js.org/) 作为捆绑器。我通常在数字海洋上部署我的 Laravel 项目&,我也在一些节点项目上使用 Heroku。所以，我想到在任何一个上面部署这个应用。我在网上冲浪。结果是，我找不到一个不使用 node backend 来部署 React 应用程序的资源，或者这些步骤对我来说太难了，我无法理解并与我的应用程序相关联。在花费了 *4 个多小时*后，我最终决定放弃部署的想法。这时我从朋友那里得到一个建议，把它部署到 Firebase 上。

## 部署

在这一部分，我想写一下我花了大约 *20 多分钟*来托管我的应用程序的步骤。在此之前，我将分享我的 webpack 配置文件。

[![Imgur](../Images/f2861e5170df280604ba738c283c2082.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lC1HRJL9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/LcAYRcm.png)

# 1。安装ˌ使成形

要开始使用 Firebase 托管，请登录 Firebase 控制台，导航到项目仪表板的托管面板，然后单击 get started。

接下来，我们将安装 Firebase CLI(命令行工具)并初始化您的站点。

## 安装 Firebase CLI

安装 Node.js 和 npm 后，可以通过 npm 安装 Firebase CLI:

`npm install -g firebase-tools`

这将安装全局可用的 firebase 命令。要更新到最新版本，只需重新运行相同的命令。

## 初始化您的站点

如果您有一个想要部署的现有 Firebase 项目，请 cd 到项目的根目录并运行:

`$ firebase init`

firebase init 命令在我们项目的根目录下创建一个`firebase.json`配置文件。我们的`firebase.json`主机配置将如下所示:

```
{  "hosting":  {  "public":  "public",  "ignore":  [  "firebase.json",  "**/.*",  "**/node_modules/**"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

# 2。建设

使用 webpack，您可以通过在终端上使用以下命令将所有项目组件捆绑到一个文件中，

`$ webpack`

这将创建一个`bundle.js`文件，该文件可以导入到`index.html`文件中，这两个文件都位于公共文件夹中。

### 项目结构

```
 Root

    Public

        -bundle.js

        -index.html

    Src

        React Components 
```

Enter fullscreen mode Exit fullscreen mode

# 
  

1.  deploy

要部署您的站点，只需从项目目录中运行以下命令:

`$ firebase deploy`

这将把您的项目部署到`<YOUR-FIREBASE-APP>.firebaseapp.com`。
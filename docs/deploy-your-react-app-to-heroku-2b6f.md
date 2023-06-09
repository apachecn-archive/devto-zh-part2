# 将 React 应用程序部署到 Heroku

> 原文：<https://dev.to/smithmanny/deploy-your-react-app-to-heroku-2b6f>

在开始学习本教程之前，你需要先安装一些东西。

🚨如果你的目标是将 React UI + API (Node、Ruby、Python……)组合成一个 app，那么本教程不是答案。

### 要求:

1.  [节点/ NPM](https://nodejs.org/en/) —点击链接并下载安装程序
2.  [Git](https://git-scm.com/downloads) —点击链接并下载安装程序
3.  [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) —点击链接并下载安装程序

### 步骤:

1.  报名参加 [Heroku](https://www.heroku.com/)
2.  为部署设置我们的 React 应用程序
3.  创建 Heroku git 存储库

# 第一步——报名 Heroku

### 访问 [Heroku](https://www.heroku.com/) 免费招待

这一步说明了一切，我们需要先注册 Heroku，然后才能做任何部署。所以去 Heroku 报名吧。一旦你注册了，一定要去你的邮箱确认你的账户。

# 步骤 2 —设置 React 应用程序

[![package.json • Before](img/4892b4c4ce75e87378fe739ba2f022ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NUnAoUCz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2At-t56mOcUMwZm2Py8XDwgw.png)

打开你的 React 应用(我用的是 create-react-app)，打开你的 package.json 文件。如果您使用 create-react-app，我们将添加一个名为 engines 的新对象。在引擎对象内部，我们需要指定 npm 和节点的版本。为此，请打开您的终端并键入:

*   npm -v

按回车键

*   节点 v

按回车键
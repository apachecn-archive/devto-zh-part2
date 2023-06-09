# 以正确的方式在 heroku 上部署 React 应用程序

> 原文：<https://dev.to/webdevraj/deploy-a-react-app-on-heroku-the-right-way-5efo>

当我们谈到部署时，在 heroku 上部署 react 应用程序是最简单的任务。但是，如果您错过了一些重要的步骤，您可能会破坏 react-router 的功能或者将开发构建部署到生产环境中。

因此，让我们看看在 heroku 上以正确的方式部署 react 应用程序的过程，以便我们获得生产优化版本和完整的 react 路由器功能。

我使用 create-react-app 为 react 应用程序生成样板代码，并假设您使用的是相同的代码。

1.  第一步是使用 create-react-app 创建一个项目，并根据需要更新代码。 `$ create-react-app MyAwesomeApp
    $ cd MyAwesomeApp`
2.  接下来，我们必须在项目文件夹中初始化一个 git 存储库。 `$ git init`
3.  如果你还没有在 heroku 上注册。

4.  安装 heroku CLI

    [https://devcenter . heroku . com/articles/heroku-CLI](https://devcenter.heroku.com/articles/heroku-cli)

5.  使用您的电子邮件和密码登录 heroku CLI。
     `$ heroku login` 

6.  使用 create-react-app buildpack 创建一个新的 heroku 应用程序。这个构建包将 React UI 部署为静态网站。它还使用 React 应用程序的生产版本进行部署。
     `$ heroku create MY-AWESOME-APP --buildpack [https://github.com/mars/create-react-app-buildpack.git](https://github.com/mars/create-react-app-buildpack.git)` 

7.  在项目目录的根目录下添加一个新文件，并将其命名为 static.json。
     `{
    "root": "build/",
    "clean_urls": false,
    "routes": {
    "/**": "index.html"
    }
    }` 

8.  现在提交所有更改并将代码推送到 heroku master。
     `$ git add .

    $ git commit -m "initial commit"

    $ git push heroku master` 

9.  您可以使用`$ heroku open`来检查部署

查看[create-react-app-build pack](https://github.com/mars/create-react-app-buildpack)和 [Heroku](https://devcenter.heroku.com/articles/git) 了解更多细节。

原帖:[https://medium . com/@ WebDevRaj/deploy-a-react-app-on-heroku-the-right-way-e 17333d 29169](https://medium.com/@WebDevRaj/deploy-a-react-app-on-heroku-the-right-way-e17333d29169)
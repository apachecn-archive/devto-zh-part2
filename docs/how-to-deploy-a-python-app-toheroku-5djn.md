# 如何将 Python 应用程序部署到 Heroku

> 原文：<https://dev.to/davedodea/how-to-deploy-a-python-app-toheroku-5djn>

##### 本帖意在帮助那些学习 Python 的人。它将帮助您将新应用程序部署到网络上，以便您可以测试并与其他人共享。Heroku 自己的说明为初学者省略了几个步骤，因此本文为您提供了入门和运行的一切。

* * *

### 先决条件:

*   Git，如果你需要这方面的帮助，请查看我的另一篇文章[这里](https://medium.com/@davedodea/git-commands-you-need-to-git-going-b98adc42182e)。
*   注册一个 [Heroku 账号](https://signup.heroku.com/signup/dc)。
*   您的计算机上安装的 Python 版本> 3.6。
*   已安装的管道数:`pip install pipenv`。

### 英雄库 CLI:

安装 Heroku CLI(命令行界面)，以便您可以从命令行部署/管理您的应用程序/项目。
`' $ ' preceeds commands to be entered in the terminal/shell`

*   苹果电脑:`$ brew install heroku/brew/heroku`
*   Windows: [下载安装程序](https://cli-assets.heroku.com/heroku-x64.exe)。
*   Ubuntu: `$ sudo snap install heroku --classic`

* * *

#### 第一步:

如果你有一个现成的 Python 应用程序，那再好不过了！如果没有，让我们使用 Heroku 提供的一个用于测试目的，这样我们就可以了解过程:

*   `$ git clone https://github.com/heroku/python-getting-started.git`

移至您的应用程序目录，或者:

*   `$ cd python-getting-started`

在你的应用中安装一个名为 [Gunicorn](http://gunicorn.org/) 的网络服务器:

*   `$ pip install gunicorn`

添加您的应用要求:

*   `$ pip freeze > requirements.txt`

创建一个新文件，文件名为:`Procfile`没有文件扩展名。阅读更多关于 [Procfile](https://devcenter.heroku.com/articles/procfile) 的信息。
为这个特定的应用程序添加到 Procfile:

*   `$ web: gunicorn gettingstarted.wsgi`

您的应用程序将有不同的设置，即:

*   `$ web: gunicorn app:app`

上面的命令告诉 Heroku 启动 web 服务器以及模块和应用程序名称。

安装应用程序的要求:

*   `$ pip install -r requirements.txt`

现在我们可以部署我们的应用了:

*   `$ heroku create`

把我们的代码推给 Heroku:

*   `$ git push heroku master`

您可能需要确保您的应用程序至少有一个实例处于运行状态:

*   `$ heroku ps:scale web=1`

将显示您的应用所在的 URL，或者您可以在浏览器中打开它:

*   `$ heroku open`

如果您想调试某些问题，可以在 CLI 中查看应用程序的日志:

*   `$ heroku logs --tail`

#### 您的应用程序现已在 Heroku 上线，供您测试并与他人分享！

如果你有任何问题或补充，请在下面的评论中告诉我，或者在 [Twitter](https://twitter.com/DaveDODea) 上联系我。

~戴夫。
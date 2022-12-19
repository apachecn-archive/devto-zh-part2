# 将 Masonite 4 应用程序部署到 Heroku

> 原文：<https://dev.to/masonite/deploying-a-masonite-application-to-heroku-45jp>

# 简介

好了，你有了一个用 Masonite 构建的非常棒的应用程序。也许是 SaaS 或者下一个 Instagram。在你开支票之前，你需要把它部署到某个地方。

Heroku 是 Masonite 的优秀部署系统，因为 Heroku 的 CLI 工具使与 Heroku 应用程序的通信变得简单，而且将 WSGI 应用程序部署到 Heroku 非常简单。

Masonite 只是一个 WSGI 应用程序。这意味着 Heroku 的大部分关于 WSGI 部署的教程都可以密切关注，并且只需稍加调整就可以很好地工作。

# 几点注意事项

Heroku 是一个有趣的部署平台。Heroku 是所谓的“短暂”系统。这意味着随机地，每隔几个小时或几天，整个应用程序都会被清除，并放在 Heroku 内部基础设施的其他地方。

这意味着:

*   我们现在受限于我们可以使用的驱动程序。我们不能使用像`memory`驱动程序或缓存东西到磁盘，因为整个磁盘将被清除并在互联网上的其他地方重新创建。

# 入门

考虑到这一点，让我们解释一下 Masonite 具体需要做些什么。

# 司机

确保所有的驱动程序都可以在 Heroku 短暂的系统上运行。Masonite 在设计时就考虑到了短暂的系统，所以 Masonite 的所有特性都至少有一个驱动程序可以在短暂的系统上工作。例如，我们将不能使用`memory`会话驱动程序，而是需要使用`cookie`会话驱动程序(它将在客户端的浏览器上加密和设置会话数据)。

所有的驱动程序都是为一个短暂的系统正确设置的，所以你不需要担心这个问题，除非你在开发过程中改变了它。

# Procfile

Heroku 有一个 Procfile 的概念，它只是一个解释 Heroku 应该如何部署我们的应用程序的文件。

我们将只在目录结构的根目录下创建一个简单的 Procfile，这个目录结构就是`wsgi.py`所在的位置:

[![](../Images/d509002db759f520e423a061bbc9a748.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7Ets94_e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v4h6bu23xtfl37n2ph0r.png)

我们将使用 Gunicorn 来部署我们的 WSGI 应用程序，因此在这个 Procfile 中应该有一行简单的文本:

```
web: gunicorn wsgi:application 
```

Enter fullscreen mode Exit fullscreen mode

不过，指定工作人员的数量可能是有用的:

```
web: gunicorn -w 2 wsgi:application 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，我们就可以将它提交给源代码控制。

# 需求. txt

我们现在需要在我们的`requirements.txt`文件中指定 Gunicorn。

我们还应该将这些版本绑定到一个特定的版本，以防止 Heroku 升级我们的依赖项:

```
masonite>=4,<5
masonite-orm>=2.0,<3.0
gunicorn==19.9.0 
```

Enter fullscreen mode Exit fullscreen mode

完美。如果您连接到数据库，那么您还需要指定一个数据库驱动程序。你可能在开发的时候安装了一个，但是我们现在需要在这里指定一个，因为 Heroku 的短命系统。您可能会使用 Postgres，但也可以随意安装您需要的任何数据库驱动程序:

```
masonite>=4,<5
masonite-orm>=2.0,<3.0
gunicorn==19.9.0
psycopg2==2.7.5 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们可以这样做:

```
$ heroku run python craft migrate 
```

Enter fullscreen mode Exit fullscreen mode

# 数据库(可选)

如果您正在部署一个没有数据库的应用程序，那么上面的一切对您来说应该没问题。您现在可以部署您的应用程序了(通过下一节的部署)。

如果您正在部署一个确实需要数据库的应用程序，那么我建议您使用 Heroku 的 Postgres 数据库。它有一个非常慷慨的免费计划，只是让你的应用程序启动并运行，你也可以在以后升级到一个更大的数据库。

这是 Heroku 特有的，所以你可以在他们的网站上找到关于如何部署 Postgres 数据库的文档: [Heroku Postgres | Heroku 开发中心](https://devcenter.heroku.com/articles/heroku-postgresql)

# 部署

现在您的应用程序已经为 Heroku 设置好了，我们现在可以部署您的应用程序了！这也是 Heroku 特有的，所以请阅读这里的文档:[使用 Git | Heroku 开发中心部署](https://devcenter.heroku.com/articles/git)

在这里复制和粘贴指令是多余的，所以一定要仔细阅读。

# 环境变量

好的，这一部分很重要！由于我们不能将环境变量添加到系统本身(同样是因为 Heroku ),所以我们需要将它们全部添加到 Heroku 内部的仪表板中。

我们可以在 Heroku dashboard 中的 Dashboard > Your-App-Name > Settings > Config Vars 下添加所有环境变量。Heroku 称它们为配置变量，但它们实际上只是环境变量。

同样，如果您没有部署数据库，那么您只需要一个`KEY`环境变量。

您应该只为生产生成一个新的密钥，但是如果您希望它们是相同的:
，那么您可以随意使用您的`.env`文件中的密钥

```
$ python craft key 
```

Enter fullscreen mode Exit fullscreen mode

这将生成一个新的密钥:

```
KEY CQxnLONbVOO67gy2MWULKJPWXzZiEgyA1x36qnu3iYs= 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将它作为一个键添加到配置变量中，如下所示:

[![](../Images/9ade90501dd65028e286dd3567aec19f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--baDybxRV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/78q8yv53why6z3ans1wl.png)

## 数据库配置

如果您正在运行一个数据库，那么您需要将 DATABASE_URL 配置变量分解成 Masonite 使用的单独的环境变量。

Heroku 生成一个名为 DATABASE_URL 的大字符串，如下所示:

```
postgres://user3123:passkja83kd8@ec2-117-21-174-214.compute-1.amazonaws.com:6212/db982398 
```

Enter fullscreen mode Exit fullscreen mode

如果我们把它分解，看起来更像这样:

```
postgres://DB_USERNAME:DB_PASSWORD@DB_HOST:DB_PORT/DB_DATABASE 
```

Enter fullscreen mode Exit fullscreen mode

再次分解，我们需要添加这些配置变量:

```
DB_USERNAME = user3123
DB_PASSWORD = passkja83kd8
DB_HOST = ec2-117-21-174-214.compute-1.amazonaws.com
DB_PORT = 6212
DB_DATABASE = db982398 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要添加我们正在使用的驱动程序:

```
DB_DRIVER = postgres 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，如果没有找到环境变量，`APP_DEBUG`环境变量将在内部设置为 False。有一段时间，你可能想把它设置为 true，这样你就可以在测试时看到任何错误，以确保一切正常。我们可以将其设置为真:

```
APP_DEBUG=True 
```

Enter fullscreen mode Exit fullscreen mode

您的最终配置变量将如下所示:

[![](../Images/af29e3acdb34ba870715e417e11def05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LeYD03mx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/21aa1hfrxnttyamcdxtm.png)

* * *

恭喜你！现在您已经有了一个很棒的 Masonite 应用程序可以部署了！
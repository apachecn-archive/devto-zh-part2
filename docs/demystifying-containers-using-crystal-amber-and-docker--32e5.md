# 使用 Crystal、Amber 和 Docker 揭开容器的神秘面纱

> 原文：<https://dev.to/jvarness/demystifying-containers-using-crystal-amber-and-docker--32e5>

如果你和我一样，你听说过很多关于 Docker 和容器化的东西，但是还没有完全挖掘它的潜力，或者你不确定为什么或者如何它能适合你的用例。

就像所有的新技术一样，有一个学习曲线，Docker 也有一个曲线，但谢天谢地有大量的[文档](https://docs.docker.com/)来帮助减少这个曲线。

我会试着解释一些事情，比如我不是一个超级骗子 noob，但这篇文章会假设你对 Amber 和 Crystal 足够了解，可以对 web 应用程序做一些小的修改。如果你需要了解更多关于 Amber 和 Crystal 的知识，我强烈建议你看看 Elias Perez 的帖子，因为它们非常全面，可以帮助你很好地理解 Amber 应用程序是如何创建的。

# 生成琥珀色 App

首先，我建议使用`amber new`命令:
生成你自己的 amber 应用程序

```
amber new <name_of_app> 
```

Enter fullscreen mode Exit fullscreen mode

我目前正在用琥珀而不是 Angular 来实现英雄之旅的教程应用程序，但是它和我在[这篇文章](https://dev.to/jvarness/angular-apps-in-typescript-or-dart-9hi)中所写的相差甚远。

在没有向`amber new`命令提供任何选项的情况下，生成了一个新项目，它使用 PostgreSQL 和 Granite 作为它的 DB 和 ORM。这通常没那么糟糕，但是当涉及到配置数据库时，我通常会很恼火。在我的职业生涯中，我记得在尝试配置 Oracle 数据库时遇到了很多问题，就在几周前，我尝试在 Linux Mint VM 中安装 PostgreSQL，然后放弃并安装了 Xubuntu VM。

当你想做的只是*代码*时，这真的很烦人，但是你必须建立一个基础设施来运行你的应用程序！这很累人，不会让你更接近编写和运行应用程序的最终目标，而且即使你让你的环境正常工作，也不能保证它会像你的客户所拥有的一样*。*

 *要是我们能做一件更简单的事就好了...

# 不要下载数据库，下载 Docker！

说真的，如果你已经到了这一步，那就省掉下载 PostgreSQL 并试图让它运行的麻烦吧。下载 [Docker](https://docs.docker.com/install/) ，如果你相信的话，将会让你现在的事情变得更容易，从长远来看也是如此。

昨晚我把 Docker 下载到了我的 Mac 电脑上，在下载后的半小时内，我就可以把我的 Amber 应用程序部署到我的机器上的一个容器中。如果没有它，我可能会花大量的时间下载 PostgreSQL 并尝试建立一个 DB 实例，而这些时间我真的可以花在编码上。

如果您已经创建了一个简单的应用程序，并且已经为您的平台下载了 Docker，那么在您的终端上，使用下面的命令来运行您的应用程序:

```
docker-compose up -d 
```

Enter fullscreen mode Exit fullscreen mode

通过运行`docker-compose up`，您要求 docker 使用在生成应用程序时创建的`Dockerfile`和`docker-compose.yml`文件中定义的配置来编写应用程序。`-d`选项只是告诉 Docker，一旦构建了映像并且它们正在运行，就在后台运行它们，这样正在运行的 Docker 进程就不会附加到您的终端会话上。

要查看运行的新图像，运行命令`docker container ls -a`，您将看到 Docker 为您创建并运行的所有容器:

```
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS                    NAMES
459baf330231        tour-of-heroes      "amber watch"            4 minutes ago       Up 4 minutes               0.0.0.0:3000->3000/tcp   tourofheroes_app_1
cd860dfd3fc3        tour-of-heroes      "bash -c 'while ! nc…"   4 minutes ago       Exited (0) 4 minutes ago                            tourofheroes_migrate_1
b0a5fd0d4ce5        postgres            "docker-entrypoint.s…"   4 minutes ago       Up 4 minutes               5432/tcp                 tourofheroes_db_1 
```

Enter fullscreen mode Exit fullscreen mode

# 我刚才做了什么？

当然，您使用`docker-compose`在本地部署了您的第一个 Docker 映像！但是 Docker 做了什么？

看一看`docker-compose.yml`文件，你大概就能拼凑出发生了什么。在`docker-compose.yml`文件中定义了三个服务:`app`、`migrate`和`db`。`app`和`migrate`服务是属于您的 Docker 映像的服务，而`db`服务使用`postgres`映像，这样*您就不必自己管理 db*。没错:不需要配置默认管理员用户，不需要下载数据库。

由于`db`服务建立了数据库，您需要将您的 Granite 模型迁移到数据库中...你不需要这么做，因为 Docker 已经为你创建了一个容器！`migrate`服务将运行所有适当的`amber migrate`命令，将模型迁移到数据库中，并运行为您的项目编写的种子程序。

最后，还有一个`app`服务，它只是按照您的`Dockerfile`中的定义运行您的应用程序。一旦这个容器开始运行，就可以通过访问`http://localhost:3000`来访问应用程序了。

# 更改应用程序

随着 Docker 映像的运行，您实际上可以对您的应用程序进行更改，这些更改将会反映在您的映像中！

我要求 Amber 为我生成一个`Hero`模型的脚手架，但是它不够聪明，不知道“hero”的复数是“heroes”而不是“heros”。我走了一圈，修复了所有“heros”所在的地方，它立即反映在应用程序中！

Amber 也足够酷，默认情况下可以通过 CDN 将 [Bootstrap 3.3.7](https://getbootstrap.com/docs/3.3/) 拉入应用程序，所以我更新了`index.slang`文件，当英雄是“最喜欢的”英雄而不是“真”或“假”时，显示一个星形符号。

现在，如果您需要关闭应用程序，您可以使用`docker-compose down`关闭应用程序，但是*要注意*，因为调用`down`将关闭`app`、`migrate`和`db`容器，并且您之前保存在那里的任何信息都将丢失。

您可以安全地为您的应用程序重新构建 Docker 映像，并通过调用:
来启动它

```
docker-compose up -d --no-deps --build app 
```

Enter fullscreen mode Exit fullscreen mode

这将只重建应用程序，而不包括任何依赖项，如数据库。如果需要执行另一个`migrate`命令，可以在命令的末尾加上`migrate`。

# 包装完毕

如果你读了这篇文章，我希望你会觉得有帮助或有用！希望在阅读完这篇文章并跟随它之后，你会对容器有更多的理解。我知道经历这些让我的信心增强了一点！*
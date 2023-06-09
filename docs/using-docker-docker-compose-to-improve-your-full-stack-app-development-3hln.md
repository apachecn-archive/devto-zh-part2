# 使用 Docker & Docker Compose 改进您的全栈应用程序开发

> 原文：<https://dev.to/paigen11/using-docker-docker-compose-to-improve-your-full-stack-app-development-3hln>

[![Group of developers sitting around laptops](img/1241b7095630a823f738000032f3c6a1.png "Group of developers sitting around laptops")T2】](///static/53d2f41ecc3bb8e30d89e6f20c233861/d165a/developers.jpg)

## 简介

我不知道你怎么想，但对我来说，通常一个新项目最困难的部分是让所有必要的部分启动并运行。数据库、后端服务器，当然还有前端用户界面——要跟踪、管理依赖性、监控健康状况、防止端口冲突，并确保每个组件都可以连接到其他组件，以使应用程序从头到尾正常工作，这些都非常重要。

如果你和一个团队一起开发，并且你们都连接到同一个数据库，就不要让我开始了。这么说吧，这可能是很多令人头疼的事情的原因，也是我们经常听到的一句话“有人又把数据库炸飞了吗？我用来测试的不见了！”这并不好看，而且当数据失去同步或被直接删除时，肯定也不好玩。

**今天，我将向大家展示用 Docker 进行容器化开发是如何让大家的本地开发体验变得更好的。**

* * *

## 解？集装箱化的开发环境

[![Docker mascot, Moby Dock](img/f785ad6e159ece41db91640eb11008d3.png "Docker mascot, Moby Dock")T2】](///static/bee073438638a0ff42013ff748a9a39d/e6e7b/moby-dock.jpg)

我是码头吉祥物❤️，莫比码头。他太可爱了。

我将从根本上改进你的开发方式——至少在本地，也可能在你较低的生命周期和生产环境中。怎么会？在我们可靠的文件的帮助下。如果你不熟悉 Docker，我建议你看看我之前的两篇博文，内容涉及 Docker 的 **[基础知识和 Docker 最有用的功能之一](https://www.paigeniedringhaus.com/blog/docker-101-fundamentals-the-dockerfile)**[Docker 撰写](https://www.paigeniedringhaus.com/blog/docker-102-docker-compose)** 。**

如果你已经熟悉 Docker，请继续阅读。你知道 Docker，你知道它的容器化本质，你甚至可能使用过容器的力量或整个容器化的生态系统。

但是您有没有想过它会如何使您的本地化应用程序开发变得更容易呢？想想看:使用一个`docker-compose.yml`来控制你的开发环境解决了我上面提到的问题。

*   `docker-compose.yml`中列出的所有服务都可以通过一个命令一起启动，
*   即使应用程序在同一个端口上启动，也不会发生端口冲突(至少在内部 Docker 环境中是这样),
*   每个服务都知道并能够连接到其他服务，而不会出现问题，
*   出现“它在我的机器上工作”综合症的可能性更小，因为每个容器都使用完全相同的映像和完全相同的依赖项，
*   最棒的是，每个 Docker 环境都可以拥有自己的数据库，其他人无法访问这些数据库(从而破坏其中的数据)。

另外，做起来超级简单。感兴趣了吗？

## 如何对申请进行归档

我将展示用全栈 JavaScript 应用程序“对接”应用程序的简易性。这是一个 MERN 应用程序，只是用 MySQL 数据库替换了 MongoDB。

如果你想看我的完整应用程序，可以在 Github 的 **[找到代码。](https://github.com/paigen11/mySqlRegistration)**

这里是这个特定应用程序的文件结构的高级视图。因为我用一个相对较小的用户注册应用程序进行演示，所以我将服务器和客户端放在同一个 repo 中，但是将它们分解成多个独立的项目并在`docker-compose.yml`的帮助下将它们结合在一起是非常容易的。

> **补充说明:**如果这是两个(或更多)独立的服务，我需要在两个回购中保留一个`docker-compose.yml`，这样每个回购都可以为本地开发服务。
> 
> 每次调用`docker-compose up`时，正在开发的服务将构建一个新的映像，并且它可以访问存储在 Docker Hub(或您选择存储映像的任何地方)中的其他服务的 Docker 映像，该映像带有我在`docker-compose.yml`中想要的版本标签。
> 
> 对于生产，还会有第三个`docker-compose-prod.yml`只使用经过测试和批准的图像。但是这超出了本文的范围。

回到项目文件结构。

### App 文件结构

```
root/
├── api/ 
├── client/ 
├── docker/ 
├── docker-compose.yml 
├── Dockerfile 
```

Enter fullscreen mode Exit fullscreen mode

显然，这些目录中的每一个都包含了大量的文件，但是为了简单起见，我只展示了主要的文件结构。

尽管`client/`和`api/`文件夹都包含在同一个 repo 中，但我构建它们时考虑了微服务和模块化。如果一个部分成为瓶颈，需要第二个实例，或者应用程序变得太大，需要拆分，不需要太多的重构就可以做到。为了实现这种模块化，我的 API 和客户端应用程序都有自己的`package.json`文件，其中包含每个应用程序运行所需的依赖关系。

好的一面是，由于目前这是一个应用程序，两个应用程序都是 JavaScript，我可以有一个`Dockerfile`为两个应用程序工作。

### docker file

下面是`Dockerfile`的样子:

```
// download a base version of node from Docker Hub
FROM node:9

// create the working directory for the application called /app that will be the root
WORKDIR /app

// npm install the dependencies and run the start script from each package.json
CMD ls -ltr && npm install && npm start 
```

Enter fullscreen mode Exit fullscreen mode

这就是这个`Dockerfile`的全部内容:就这三个命令。`docker-compose.yml`有一点多，但当它被分解时，仍然很容易理解。

### 码头工-堆肥. yml

```
version: '3.1'

services:
  client:
    build: .
    volumes:
      - "./client:/app"
    ports:
      - "3031:3000"
    depends_on:
      - api

  api:
    build: .
    volumes:
      - "./api:/app"
    ports:
      - "3003:3000"
    depends_on:
      - db

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: users
      MYSQL_USER: test
      MYSQL_PASSWORD: test1234
    ports:
      - "3307:3306"
    volumes:
      - ./docker/data/db:/var/lib/mysql 
```

Enter fullscreen mode Exit fullscreen mode

> 我想指出的是，Docker Hub 上有一个较新版本的 MySQL，但我无法让它与 Docker Compose 一起正常工作，所以我会推荐使用 mysql:5.7 版本，直到另行通知。

在上面的代码片段中，您可以看到我的两个服务:`api`和`client`，以及数据库(这是我迄今为止第一次提到它，也是我最喜欢 Docker Compose 的一点)。

为了解释发生了什么，客户端和 API 服务都使用了相同的`Dockerfile`，它位于项目的根(为它们都赢得了`build: .`构建路径)。同样，每个文件夹都安装在我们在 docker 文件中指定的工作目录中，其中包含卷:`./<client or api directory>:/app`。我为每个服务公开了一个端口，以使各个服务的调试更容易，但是如果只通过 UI 向应用程序公开一个端口也很好。最后，`depends_on`让应用程序的每个部分等待，直到所有部分都启动。

`client`依赖于`api`的启动，`api`依赖于`database`的启动，一旦所有的凭证都被提供给数据库，并且从 [Docker Hub](https://hub.docker.com/_/mysql/) 中提取图像，就可以启动了。我在数据库上打开了一个端口，这样我就可以将 Sequel Pro 连接到我的数据库，并在用户对象被创建和更新时看到它们。同样，这使得我开发应用程序时的调试更加容易。

数据库中关于`volumes`的最后一行，是一个值得注意的特殊行。这就是数据在应用程序中的持久化方式。它只存在于运行 Docker 生态系统的本地机器上，但这通常是开发所需要的。这样，如果你使用 [Flyway](https://flywaydb.org/) 或 [Liquibase](https://www.liquibase.org/) 或另一个 SQL runner 来创建表格并向其中加载数据，然后更改数据，以测试应用程序的功能，可以保存更改，以便当你重新启动应用程序时，数据是你离开时的样子。真的很牛逼。

好了，我们已经介绍了`Dockerfile`,解释了`docker-compose.yml`,并且注意到了从 Docker Hub 中提取的数据库映像。我们就要出发了。

* * *

## 一行启动 App

现在是启动这个应用程序的时候了。如果您是第一次在本地开发这个应用程序，请在命令行中键入 docker-compose build。这将为客户机和 API 应用程序构建两个映像 MySQL 数据库作为映像直接来自 Docker Hub，因此不需要在本地构建该映像。这是你将在终端看到的。

[![Terminal output in the console from Docker Compose](img/77b32b59e28e7f120578dc0cb799b770.png "Terminal output in the console from Docker Compose")T2】](///static/5f0a0e8053bf48b99ce36f892c651b07/30c92/docker-compose-output.png)

*您可以看到 db 被跳过，API 和客户端都是使用项目根目录下的 Dockerfile 文件构建的。*

一旦图像构建完成，输入`docker-compose up`。您应该会在所有服务启动的终端中看到一条消息，然后在每个服务启动并连接时会记录大量代码。你应该可以走了。就是这样。开始运行。你完了。开始开发。

[![Terminal output in the console for Docker Compose up](img/f859e64eba5375949d6a4c80364ad370.png "Terminal output in the console for Docker Compose up")T2】](///static/bf3f6977060516d6fc7d83fb63a0e808/cd78c/docker-compose-up.png)

*这是你写完`docker-compose up`后马上就会看到的。一旦所有的服务注册完成，他们将尝试启动和连接，你应该可以开始开发了。*

每当你想停止你的应用程序时，你只需在终端中输入`docker-compose down`，服务就会优雅地关闭。数据将被保存在本地，所以当您键入 docker-compose up 来启动应用程序备份时，您的数据将仍然在那里。

[![Terminal output in the console for Docker Compose down](img/2013310d471abf347e833f33911ac46c.png "Terminal output in the console for Docker Compose down")T2】](///static/01929aeed3c9a1d7586094238fd96e06/59822/docker-compose-down.png)

*使用`docker-compose down`停止服务时看到的内容。*

* * *

## 结论

Docker 和 Docker Compose 可以让 web 开发变得更加容易。您可以创建功能齐全的、独立的开发环境，这些环境拥有自己的数据库和数据，而您只需付出很少的努力，就可以加快开发速度，减少或避免团队在配置和构建项目时通常会出现的问题。如果你还没有考虑过“整理”你的开发过程，我强烈建议你研究一下。

过几周再来看看——我会写更多关于 JavaScript、React、IoT 或其他与 web 开发相关的东西。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

感谢您的阅读，我希望这证明是有帮助的，并使您自己的本地开发对您的开发团队中的每个人来说更容易。

* * *

## 进一步引用&资源

*   [MERN 申请的 Github 回购](https://github.com/paigen11/mySqlRegistration)
*   [码头工人](https://www.docker.com/what-docker)
*   [坞站复合文档](https://docs.docker.com/compose/overview/)
*   【mysql library 坞站枢纽
# 有了 Docker Compose，您的开发工作流程变得更好了

> 原文：<https://dev.to/youknowfordevs/your-development-workflow-just-got-better-with-docker-compose-4mcl>

在之前的帖子中，我们看到了如何使用 Docker 来设置我们的基本节点开发环境。我们的下一步是减少这些笨拙的`docker run`命令的大小。这不仅仅是因为它们的笨拙，还因为如果我们只是从命令行输入它们，那么我们就没有一个简单的方法来分享我们正在做的事情——不仅仅是与他人，而是与我们自己，明天，当我们不可避免地忘记我们今天在做什么的时候！

因此，在我们忘记我们在上一篇文章中运行的命令之前，让我们将它锁定在一个可以重复使用的文件中。

但你会问，在什么文件里？

# 复合坞站

我们将要用来捕获这类命令的工具是 Docker Compose。这个应用在你安装 Docker 的时候就已经为你安装好了(假设你采纳了我们之前帖子的建议[拥抱 Docker](///2018/07/01/dont-install-node-until-youve-read-this.html#docker-first) )。Docker Compose 是一个*令人难以置信的*方便实用的工具，因为它允许我们使用 YAML 文件来创建 Docker 命令的定义，而不是必须使用命令行选项。这意味着我们可以轻松地共享和版本化我们的命令。

YAML 文件还可以用来管理我们希望同时启动的一组容器——也许我们的微服务需要 MySQL 数据库或 RabbitMQ 队列——如果这还不够，相同的文件格式还可以用来描述 Docker swarm 堆栈——一组将一起运行的服务——当部署我们的应用程序时。

正如在上一篇文章中，我们建议应用程序不应该再安装在本地，而应该在 Docker 容器中运行，现在我们想同样强烈地指出，在创建应用程序的过程中，无论是林挺、测试、打包还是部署，都不应该在没有被捕获到 Docker Compose 文件的情况下执行。

但是在我们变得太兴奋之前，让我们回到我们在早期的帖子中运行的命令(它启动了一个开发容器，我们在其中运行 Node ),让我们将其转换为使用 Docker Compose。

## 一个 Docker 编写的配置文件

回想一下，我们运行的命令是:

```
docker run -it --rm -v ${PWD}:/usr/src/app -p 127.0.0.1:3000:3000 \
  node:10.5.0-alpine /bin/sh 
```

要将它转换成 Docker Compose 文件，请启动您最喜欢的编辑器，创建一个名为`docker-compose.yml`的文件，在其中放置以下内容:

```
version: "3.2"

services:
  dev:
    image: node:10.5.0-alpine
    ports:
    - "127.0.0.1:3000:3000"
    volumes:
    - .:/usr/src/app
    command: ["/bin/sh"] 
```

您可能会发现原始命令行的哪些部分映射到这个合成文件中的哪些条目，所以我们将只标记一些可能不会立即显而易见的内容。

首先，条目`dev`只是我们的*服务*的名称。它可以是我们喜欢的任何东西，并且在一个文件中可以有多个这样的条目。我们一会儿会看到它是如何被用来指示我们想要发射的东西的。

(服务是 Docker Compose 用来描述正在运行的容器的术语。它不像我们使用`docker run`命令那样使用术语*容器*的原因是服务有额外的特性，比如能够包含一个容器的多个实例。)

接下来，您可能注意到端口映射现在用引号括起来了；在命令行中我们有`-p 127.0.0.1:3000:3000`，而在合成文件中我们有`"127.0.0.1:3000:3000"`。由于 YAML 文件的处理方式，这是一种最佳做法。如果一个低于 60 的端口被映射，并且没有指定 IP 地址(例如，`40:40`，那么解析器将不会把它视为`40`后跟`40`，而是一个基数为 60 的数字。你*可以*只要记住当使用低于 60 的端口时你需要引号，但是你将看到的大多数 Docker Compose 文件都会在*任何*端口号周围加上引号，这样更容易记住。

最后，您还会发现我们的`docker run`命令的`${PWD}`部分现在已经被替换为`.`，即当前目录。Docker Compose 在映射卷时不需要环境变量，这使事情变得简单了一些。YAML 文件中的路径总是相对于文件本身(并且支持相对路径)。

## 启动我们的开发容器

现在我们已经设置好了配置，只需运行带有服务名称的 Docker Compose 命令。运行以下命令，您应该已经再次启动了开发环境:

```
docker-compose run --rm --service-ports dev 
```

好的……所以它仍然不是块中最短的命令——我们将在未来的帖子中看到我们如何进一步降低它。但是这比我们以前使用的长命令要容易记住得多。更重要的是，无论您对配置文件做什么修改,*总是相同的*;我们想要添加到我们的`docker run`中的任何额外选项都将放入我们的 Docker Compose 文件中，清楚地记录下来并在源代码控制之下。

为了结束这一部分，我们将快速解释需要传递给`docker-compose run`的参数。第一个是`--rm`,它与我们在`docker run`中使用的选项完全相同——当命令完成运行时，我们的容器将被删除。

第二个是`--service-ports`，它指示 Docker Compose 使我们在 Compose 文件中定义的任何端口映射可用。添加这个参数有点烦人，你会发现许多讨论都认为这种行为应该是默认的。但逻辑是公平的；如果我们要启动许多连接的服务，比如 web 服务器和 MySQL 数据库，我们不需要将每个端口都映射到我们的主机上。在 web 服务器和 MySQL 服务器的例子中，不需要在我们的笔记本电脑上暴露 MySQL 的端口`3306`,因为它只在 web 服务器连接到后端时需要。Docker Compose 将创建一个网络，web 服务器和 MySQL 可以用它来相互通信。

所以我们有了它；运行该命令，我们将得到一个 shell 提示符，然后我们可以使用`docker run` :
以与前一篇文章完全相同的方式启动我们的 web 服务器

```
cd /usr/src/app
node app.js 
```

## 工作目录

我们刚才说过，使用 Docker Compose 的一个优点是，我们可以添加额外的选项，而无需改变运行命令的方式。一个例子是让 Docker 为我们切换到工作目录，也就是说，去掉我们的序列中的`cd /usr/src/app`步骤。

为此，我们只需将`working_dir`选项添加到 YAML 文件:

```
version: "3.2"

services:
  dev:
    image: node:10.5.0-alpine
    working_dir: /usr/src/app
    ports:
    - "3000:3000"
    volumes:
    - .:/usr/src/app
    command: ["/bin/sh"] 
```

再次强调，我们仍然以与之前完全相同的方式启动我们的开发环境——唯一的变化是对配置文件:

```
docker-compose run --rm --service-ports dev 
```

这一次我们的命令行提示将让我们进入正确的目录，我们可以直接启动服务器:

```
node app.js 
```

## 改变发射命令

但是我们可以更进一步。我们很少需要“在”容器内做事情，因为我们将使用在我们的笔记本电脑上运行的我们最喜欢的编辑器(记住[我们已经将我们的项目目录映射到容器中，这样我们的笔记本电脑和容器都可以访问我们的文件](///2018/07/01/dont-install-node-until-youve-read-this.html#a-development-environment))。因此，我们可能会发现自己更经常地不调用我们的容器，然后运行服务器。因此，我们可以将容器中运行的命令从启动 Bash shell 改为启动服务器:

```
version: "3.2"

services:
  dev:
    image: node:10.5.0-alpine
    working_dir: /usr/src/app
    ports:
    - "3000:3000"
    volumes:
    - .:/usr/src/app
    command: ["/bin/sh", "-c", "node  app.js"] 
```

### 干净利落的退场

您可能发现我们添加的命令并不是我们所期望的:

```
 command: ["node", "app.js"] 
```

但是:

```
 command: ["/bin/sh", "-c", "node  app.js"] 
```

其背景是，如果我们使用命令的第一个版本，简单地用`app.js`作为参数运行`node`，那么当我们试图用`[CTRL]+C`退出服务器时，什么都不会发生，我们将不得不寻找其他方法来杀死服务器。这是因为当 Node 在容器中作为主要的顶级应用程序运行时(您经常会看到被描述为*作为 PID 1* 运行)，Node 应用程序没有正确处理`SIGTERM`信号(T4)。

然而，Bash shell *确实正确地处理了整个`SIGTERM`舞蹈，并且当它接收到`[CTRL]+C`时会干净地关闭我们的服务器。所以我们需要做的就是在 shell 中运行我们的服务器。*

如果你需要(或想要)更详细地了解这一点，那么[在网上搜索类似“PID 1 docker node”](https://www.google.co.uk/search?q=pid+1+docker+node)的东西，你会找到许多文章。如果你只是想切入正题，那么请阅读[Docker](https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md)中使用 Node 的最佳实践指南中的[处理内核信号](https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md#handling-kernel-signals)一节。

## 多个服务

当然，如果我们认为我们可能需要这两个命令中的*两个*——一个在容器中启动一个 Bash shell，准备四处播放，另一个启动服务器——那么我们可以在 Docker Compose 文件中添加第二个条目:
,而不是覆盖第一个条目

```
version: "3.2"

services:
  shell:
    image: node:10.5.0-alpine
    working_dir: /usr/src/app
    ports:
    - "3000:3000"
    volumes:
    - .:/usr/src/app
    command: ["/bin/sh"]

  serve:
    image: node:10.5.0-alpine
    working_dir: /usr/src/app
    ports:
    - "3000:3000"
    volumes:
    - .:/usr/src/app
    command: ["/bin/sh", "-c", "node  app.js"] 
```

我们已经将 shell 版本的名称从`dev`更改为`shell`以表明它的用途，这意味着我们现在可以用
启动服务器

```
docker-compose run --rm --service-ports serve 
```

### 不重复自己

最后一个技巧涉及到重用文件中常用设置的方法。如您所见，我们两个服务之间的唯一区别在于`command`值。理想情况下，我们希望将所有其他值放入某个公共集合中，并在两个服务之间共享它们。

在 Docker Compose 文件格式的[3.4 版本中，这可以通过使用 YAML 锚:
来实现](https://docs.docker.com/compose/compose-file/)

```
version: "3.4"
x-default-service-settings:
  &default-service-settings
    image: node:10.5.0-alpine
    working_dir: /usr/src/app
    ports:
    - "3000:3000"
    volumes:
    - .:/usr/src/app

services:
  shell:
    << : *default-service-settings
    command: ["/bin/sh"]

  serve:
    << : *default-service-settings
    command: ["/bin/sh", "-c", "node  app.js"] 
```

所以首先注意到文档顶部的`version`值已经被更新。然后，我们想要创建用于共享的任何块都带有前缀`x-`,这就是我们如何告诉 Docker Compose 不要将这个块作为某个配置来处理。

在自定义块中，我们设置了一个锚点(`&default-service-settings`部分),并给它取任何我们想要的名字。最后，我们可以通过使用`<<`语法引用锚来引用这个块。

# 下一步

我们已经将我们的[原始`docker run`命令](///2018/07/01/dont-install-node-until-youve-read-this.html)转换成使用 Docker Compose，使得复杂的配置更容易管理。我们还添加了一些额外的命令来帮助我们的开发过程。我们现在也有了一种方法来将命令集合置于源代码控制之下。我们现在可以利用这种方法来:

*   添加更多的目录映射，这样安装了`npm install`的模块会留在我们的容器中*；*
*   为包含摩卡或 TAP 等跑步者的测试容器添加条目；
*   添加有助于构建过程的命令条目，例如使用 Webpack 或 Parcel。
*   启动本地 Nginx 服务器，它将镜像我们的实时部署。

我们将在以后的文章中深入研究这些技术。

祝你的项目好运！
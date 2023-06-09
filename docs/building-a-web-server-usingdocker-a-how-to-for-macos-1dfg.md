# 使用 Docker 构建 Web 服务器:MacOS 操作指南

> 原文：<https://dev.to/_robynedgar/building-a-web-server-usingdocker-a-how-to-for-macos-1dfg>

*供稿[迪伦阿斯普登](https://www.linkedin.com/in/dhaspden/)*
[![alt text](img/c27e3bcab11c2bead9d8e65a562b234d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iJ5ThOta--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ASpug1WjvET_0D9iyLQ2uew.png)

### 等等，Docker 是什么？

对于那些不熟悉的人来说， [Docker](//www.docker.com) 是一种在容器中创建、部署和运行一个或一组应用程序的软件技术。你问容器是什么？容器是包含运行应用程序所需的一切的包。这包括操作系统和该操作系统上运行应用程序所需的任何程序。

[![alt text](img/46c25729151598108e28ad623b2dddcb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J3t-cWbB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AiTtfM-VA1zDIDONFqHniKg.png)

在我们的例子中，我们的大多数容器都安装了 Linux (Ubuntu、Alpine 等)。)并安装了 Node.js。通过将内容保存到一个叫做图像的东西中，容器可以被重复使用无数次。

Docker 对于将扩展服务部署到生产环境中非常有用。您可以启动新的实例，只需下载 Docker 映像并运行 entry 命令即可获得正在运行的应用程序。它对于开发新的应用程序也很有用。Docker 不仅可以用来部署应用程序，还可以在容器内部进行本地开发。这意味着当您部署到生产环境时，您知道会发生什么。当你在 MacOS 上开发时，你可能会遇到程序在 Debian、Ubuntu 等上运行时运行方式不同的情况。Docker 让你在那些潜在的问题出现之前就能预见到它们。

Docker 对于新员工了解项目的诀窍也很有用。他们所要做的就是安装 Docker，他们可以立即进入一个项目，因为所有的依赖项都在容器内部运行。

### Docker 是唯一的解决方案吗？

不，还有其他工具——流浪者就是其中之一。然而，社区对 Docker 的采用和支持远远超过了其他解决方案，使得 Docker 使用起来简单了许多。使用 Docker，在本地开发并部署到生产环境是轻而易举的事情。Azure、Google Cloud Platform 和 Amazon Web Services 等大型主机完全支持 Docker 环境，并且可以扩展。这将大量开发运维工作转移到了云上，让您可以专注于重要的部分——构建您的应用程序。

# 好吧！让我们在 Docker 中构建一个 web 服务器。

### 首先，让我们准备好你需要的一切。

好的，开始之前，你需要一些资源。如果你还没有自制软件，现在是时候了。打开你的终端，运行下面的命令:

`$ /usr/bin/ruby -e “$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)”`

按照提示操作，安装 XCode 命令行工具可能需要几分钟时间。一旦完成，您需要更新家酿库并安装 Docker For Mac。您还需要 Node.js，您现在也可以安装它。

`$ brew update
$ brew cask install docker
$ brew install node`

第一个命令更新家酿库。第二个命令将在您的机器上安装 Docker For Mac。最后一个命令将 Node 安装到您的机器上。一旦安装了 Docker For Mac，您可能需要通过在应用程序文件夹中查找并打开程序来运行它。按照屏幕上的步骤设置 Docker。

### 接下来—让我们设置节点。

现在，您已经拥有了使用 Docker 创建节点应用程序所需的所有工具。让我们创建一个新的节点项目。

您将不会使用任何库或框架来构建节点应用程序，但是如果您使用了，过程将是相同的。导航到要在其中创建项目的文件夹，并运行以下命令。

`$ npm init --yes
$ touch index.js`

第一个命令使用默认值创建一个新的节点项目。第二个命令创建您的 JavaScript 文件，该文件将用作应用程序入口点。

接下来，您将创建一个基本的 JavaScript 文件，它在端口 1337 上启动一个 HTTP 服务器，并用“Hello，world！”响应所有请求在您喜欢的编辑器中打开 index.js 文件，并添加以下代码。

`const http = require(‘http’);
const server = http.createServer((request, response) => {
response.end(‘Hello, world!’);
});
server.listen(1337);`

你的节点应用完成了！现在我们所要做的就是创建一个 Dockerfile 来定义运行我们的应用程序的映像。

### 让我们创建 Dockerfile 文件。

您需要一个运行操作系统并安装了节点的容器。许多流行的软件产品都有相关的 Docker 图像来使它更容易。Node 也不例外。如果你真的很好奇，你可以在这里找到更多的资源:[https://hub.docker.com/_/node/](https://hub.docker.com/_/node/)。

你可以使用 LTS 版本的 Node，其代号为“Carbon”要开始创建 Docker 映像，您需要在项目根目录下创建一个 Docker 文件。使用下面的触摸命令来实现这一点。

`$ touch Dockerfile`

现在打开 docker 文件并添加以下内容。

`FROM node:carbon
WORKDIR /src
COPY . /src/
CMD [“node, “index.js”]
EXPOSE 1337`

第一行表示该图像继承自节点创建者提供的图像。这意味着默认情况下，您会安装带有节点 LTS 的 Linux。接下来，工作目录将是/src 文件夹。这意味着在容器中运行的所有命令都在该目录中运行。现在，您可以将项目中的所有文件复制到/src 文件夹中。这意味着您的 index.js 被复制到容器中。第四行定义了用于启动应用程序的命令。您将希望使用 Node 运行应用程序。在未来的努力中，您可能希望考虑一个在失败时自动重启应用程序的进程管理器。pm2 对此有好处。最后一个命令说，这个容器让您的应用程序端口 1337 默认打开，这样您就可以向服务器发出请求。

剩下的工作就是构建并运行您的新应用程序。Docker For Mac 安装 Docker 命令行工具，您可以使用它来构建您的映像。在您的终端中运行以下命令。

`$ docker build -t webapp .
$ docker run -p 1337:1337 webapp`

第一个命令构建映像，第二个命令运行映像。告诉第二个命令，所有对本地机器上端口 1337 的请求都将被转发到容器中的端口 1337(即您的应用程序！).

剩下的就是在你的浏览器中打开一个新标签，并导航到 [http://localhost:1337](http://localhost:1337) 。您应该会在 Docker 容器中看到来自服务器的友好问候。

恭喜您，您已经将一个节点应用程序部署到 Docker 容器中，并将其保存到一个可以部署到任何地方的映像中！理论上，您现在可以将该映像加载到像 Amazon Web Services 这样的服务上，并创建一个集群来自动扩展该应用程序。

### *附加资源:*

1.  [在 AWS 上部署 Docker 容器](https://aws.amazon.com/getting-started/tutorials/deploy-docker-containers/)
2.  [在谷歌云平台上部署 Docker 容器](https://cloud.google.com/kubernetes-engine/docs/tutorials/hello-app)
3.  在 Azure 上部署 Docker 容器

喜欢这个教程？关注 Hifyre 的 DevTips 频道，了解两周一次的 T2 技巧。

哦，我们有没有提到我们在招人？[联系我们](https://hifyre.bamboohr.com/jobs/)。
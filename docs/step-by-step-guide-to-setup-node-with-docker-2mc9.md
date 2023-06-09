# 使用 Docker 设置节点的分步指南

> 原文：<https://dev.to/itmayziii/step-by-step-guide-to-setup-node-with-docker-2mc9>

# 了解如何在 Docker 容器中设置节点 JS

## 本文的目标

*   有一个工作节点应用程序
*   通过确保进程不会因出错而退出，使节点应用程序具有弹性
*   通过在代码更改时自动重启服务器，使 Node 应用程序易于使用
*   利用 Docker:
    *   快速建立与生产环境相同的开发环境。
    *   能够轻松地在本地和服务器上切换节点版本
    *   Docker 的所有其他[福利](https://dzone.com/articles/top-10-benefits-of-using-docker)

### 先决条件

*   Docker 已经安装
*   至少具备节点和 NPM 的入门级知识

**如果你是那种只想看到最终结果的人，那么 github 回购可能更适合你**

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ itmayziii ](https://github.com/itmayziii) / [开发到节点对接设置](https://github.com/itmayziii/dev-to-node-docker-setup)

<article class="markdown-body entry-content container-lg" itemprop="text">

文章[https://dev.to/itmayziii/node-docker-setup-3n C5-temp-slug-2056 229](https://dev.to/itmayziii/node--docker-setup--3nc5-temp-slug-2056229)

</article>

[View on GitHub](https://github.com/itmayziii/dev-to-node-docker-setup)

## 1。准备一个简单的节点应用程序

我们将使用 Express，因为它很容易设置，并且该框架很受欢迎。

在一个干净的目录中，让我们从初始化 NPM 开始，继续运行该命令并按照提示进行操作(您在提示中输入的内容对于本指南并不重要)

```
npm init 
```

安装快捷方式

```
npm install --save-prod express 
```

安装 basic express 服务器。下面的文件只是说启动一个监听端口 3000 并以 *Hello World 响应的节点进程！*到/路线。

**src/index . js**T2】

```
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(port, () => {console.log(`Example app listening on port ${port}!`)) 
```

## 2。设置 Docker 来运行我们的节点应用程序

我们将使用 docker-compose.yml 文件来启动和停止 docker 容器，而不是键入长的 Docker 命令。您可以将该文件视为多个 Docker 容器的配置文件。

**码头-化合物. yml**

```
version: "3"
services:
  app:
    container_name: app # How the container will appear when listing containers from the CLI
    image: node:10 # The <container-name>:<tag-version> of the container, in this case the tag version aligns with the version of node
    user: node # The user to run as in the container
    working_dir: "/app" # Where to container will assume it should run commands and where you will start out if you go inside the container
    networks:
    - app # Networking can get complex, but for all intents and purposes just know that containers on the same network can speak to each other
    ports:
    - "3000:3000" # <host-port>:<container-port> to listen to, so anything running on port 3000 of the container will map to port 3000 on our localhost
    volumes:
    - ./:/app # <host-directory>:<container-directory> this says map the current directory from your system to the /app directory in the docker container
    command: "node  src/index.js" # The command docker will execute when starting the container, this command is not allowed to exit, if it does your container will stop

networks:
  app: 
```

现在我们已经配置好了，让我们用这个命令启动 docker 容器。这只是意味着启动我们的配置文件中定义的容器，并在后台运行它们(-d)

```
docker-compose up -d 
```

现在，您应该能够在浏览器中转到 [localhost:3000](http://localhost:3000) ，并看到`Hello World!`
[![hello world example shown in the browser](img/b1352cdb0f4d6ec144156105e582d10d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RayTi9So--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v7ktp1pbu6aqe81ldfxh.png) 
您还应该能够通过运行
来验证您的容器正在运行

```
docker ps 
```

它应该输出正在运行的 docker 容器的列表，类似于
[![list of running containers from the docker ps command](img/b317106cb3ae5b6f5160902a62fd8c4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--32XmPCXl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4n8otynzuw9cj3y17nw3.png)

### 管理该容器的有用 docker 命令

列出所有正在运行的容器

```
docker ps 
```

列出所有容器，不管它们是否正在运行

```
docker ps -a 
```

从同一目录中的 docker-compose.yml 文件启动容器

```
docker-compose up -d 
```

停止同一目录下 docker-compose.yml 文件中的容器

```
docker-compose stop 
```

从同一个目录中的 docker-compose.yml 文件重启容器

```
docker-compose restart 
```

查看 docker 容器中的日志文件

```
docker-compose logs -f 
```

## 3。让我们的应用具有弹性

如果您以前使用过 Node，那么您可能知道如果您的应用程序中出现错误，比如一个未捕获的异常，那么它将关闭该节点进程。这对我们来说确实是个坏消息，因为我们的代码中肯定会有 bug，并且不能保证我们的代码 100%没有错误。这个问题的解决方案通常是另一个进程，它监视我们的节点应用程序，如果它退出，就重新启动它。有这么多的解决方案，像 linux 的 supervisord、NPM 包 forever 和 PM2 等等...我们只需要为本指南选择一个。

我将把重点放在 [PM2](https://pm2.io/doc/en/runtime/overview/) 上，因为我对它最熟悉，而且除了进程管理之外，它还有一些其他功能，比如文件监控，这在我们的下一节中会派上用场。

安装 PM2

```
npm install --save-prod pm2 
```

可以通过命令行使用 PM2，但我们将设置一个简单的配置文件，就像我们对 docker-compose.yml 文件所做的那样，以防止我们重复键入长命令

**ecosystem.config.js**

```
const path = require('path')

module.exports = {
  apps: [{
    name: 'app',
    script: 'src/index.js', // Your entry point
    instances: 1,
    autorestart: true, // THIS is the important part, this will tell PM2 to restart your app if it falls over
    max_memory_restart: '1G'
  }]
} 
```

现在我们应该更改 docker-compose.yml 文件，使用 PM2 来启动我们的应用程序，而不是直接从 index.js 启动它

**docker-compose.yml** (只改变了*命令*选项)

```
version: "3"
services:
  app:
    container_name: app # How the container will appear when listing containers from the CLI
    image: node:10 # The <container-name>:<tag-version> of the container, in this case the tag version aligns with the version of node
    user: node # The user to run as in the container
    working_dir: "/app" # Where to container will assume it should run commands and where you will start out if you go inside the container
    networks:
    - app # Networking can get complex, but for all intents and purposes just know that containers on the same network can speak to each other
    ports:
    - "3000:3000" # <host-port>:<container-port> to listen to, so anything running on port 3000 of the container will map to port 3000 on our localhost
    volumes:
    - ./:/app # <host-directory>:<container-directory> this says map the current directory from your system to the /app directory in the docker container
    command: "npx  pm2  start  ecosystem.config.js  --no-daemon" # The command docker will execute when starting the container, this command is not allowed to exit, if it does your container will stop

networks:
  app: 
```

应该注意，更改 docker-compose.yml 文件不会影响已经运行的容器。为了使您的更改生效，您应该重启您的容器

```
docker-compose restart 
```

太好了，我们现在应该回到一个在[locah host:3000](http://localhost:3000)工作的应用程序，但现在我们的应用程序不会在我们有错误时倒下。

## 4。使我们的应用程序易于开发

您可能已经注意到，一旦一个节点进程启动，那么在您重新启动该节点进程之前，更改代码实际上不会做任何事情，对于我们来说，这将涉及到在每次进行更改时重新启动我们的 Docker 容器。听起来很糟糕。如果我们能在修改代码时让节点进程自动重启，那就太好了。在过去，我做过一些事情，比如引入一个文件监视实用程序，并使用该文件监视实用程序在文件更改时重启 Docker，或者我会使用 Nodemon，但在使用 Docker 时会有一些警告。最近，当一个文件发生变化时，我一直在使用 PM2 来重启我的节点进程，因为我们已经从上一步中获得了它，所以我们不需要安装另一个依赖项。

**ecosystem.config.js** (只增加了观察选项)**

```
const path = require('path')

module.exports = {
    apps: [{
        name: 'app',
        script: 'src/index.js',
        instances: 1,
        autorestart: true,
        watch: process.env.NODE_ENV !== 'production' ? path.resolve(__dirname, 'src') : false,
        max_memory_restart: '1G'
    }]
} 
```

如果我们没有将 NODE_ENV 环境变量设置为 *production* ，上面的配置文件现在将监视 *src* 目录。除了 *Hello World 之外，您还可以通过更改 index.js 文件在浏览器中打印其他内容来测试一下。*。同样，在此之前，你需要重启你的 Docker 容器，因为你改变了 PM2 运行容器的方式

```
docker-compose restart 
```

应该注意的是，重新启动节点进程可能需要一秒钟才能完成，如果您想查看它何时完成，可以查看 Docker 日志，以了解 PM2 何时完成了重新启动节点进程。

```
docker-compose logs -f 
```

当您的进程重新启动
[![Example of PM2 restarting node process](img/02836ffc13da2e1ef7c5a801a8c434d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m_vzgvk---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qciqk86nxtrkaq2mwxr3.png) 时，您会看到类似这样的内容

## 包装完毕

*   我们的目标之一是能够轻松地更改节点版本，您可以通过更改 *docker-compose.yml* 文件中的 image 选项来做到这一点。

*   本地安装依赖项是使用本地 NPM 和节点版本完成的，如果本地版本与 Dockers 版本不同，这有时会导致冲突。使用相同的 Docker 容器来安装您的依赖项更安全。您可以使用该命令，该命令将使用该容器来安装依赖项，然后删除它

```
docker run --rm -i -v <absolute-path-to-your-project-locally>:/app -w /app node:10 npm install 
```

*   如上所述，拥有一个不同于 Docker 正在运行的节点的本地版本可能会有问题。为了保持一致性，最好在容器内部运行命令。你可以进入一个集装箱

```
docker exec -it app bash 
```

上面的命令会将您放入容器中，这样您就可以继续从那里运行您的命令，即 *npm 运行开始*或 *npm 运行测试*

如果你不想进入容器内部，你可以像这样运行命令

```
docker exec -t app bash -c "npm run start" 
```
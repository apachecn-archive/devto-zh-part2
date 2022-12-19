# 用 mySQL 做一个 NodeJS API

> 原文：<https://dev.to/whoisryosuke/make-a-nodejs-api-with-mysql-4741>

今天我们将使用 NodeJS、Express 和 MySQL 制作一个 API 来提供 JSON 数据。我们将要构建的示例 API 将使用开源的 Kushy 数据集来提供大麻品种数据。如果需要示例数据，可以从 Github 下载，并将 SQL 文件导入到 DB 中。

我将一步一步地向您介绍安装依赖项的过程、我们应用程序的结构，以及如何从头开始构建它。

*本指南假设你对 mySQL 有基本的了解(最好是一个数据库——在你的本地服务器上， [LAMP](https://www.apachefriends.org/index.html) ，远程 URL，任何地方)，并且你至少已经在你的计算机上安装了【Node。和以前用过的 NPM。*

## 安装依赖项

为了创建 API，我们需要使用一些不同的库来实现它。这就是事情变得固执己见的地方。

我们将使用 **[Express](http://expressjs.com)** 作为我们的主要框架。如果你对此不感兴趣，你可能想试试 [Koa 或者 hapi](http://nodeframework.com/index.html#mvc)T6(还有另一个教程，因为我们要跳上快车)。

### 生产

`npm install express express-rate-limit cors helmet mysql --save`

我们将安装以下软件包:

*   **express** - MVC 用于创建节点站点
*   **快速-速率限制** -允许 API 的速率限制
*   cors 将允许你远程服务 API
*   **[头盔](https://github.com/helmetjs/helmet)** -用 HTTP 头保护你的 Express 应用
*   **[mysql](https://github.com/mysqljs)**——通过 Node 与 MySQL 连接并交互。

### 发展

`npm install --save-dev nodemon`

[Nodemon](https://github.com/remy/nodemon) 用于热重装开发中的服务器。每当我们更改代码并保存时，如果 nodemon 正在运行，它将使用新代码重启节点服务器。

## App 结构

我们的应用程序的结构是 MVC，或模型视图控制器。**模型**是到 MySQL 数据库的连接。**视图**是我们连接到并显示 JSON 数据的路径(比如 yoursite.com/api/users/)。**控制器**是从**模型**中获取数据并将其提供给**视图**的功能。

## 开发服务器

我们的“开发服务器”是 Node。那不是很方便吗？

让我们设置您的 package.json。在 scripts 部分，我们将设置启动脚本来运行我们的 server.js 文件，并将端口设置为 4200 ( `PORT=4200 node server.js`)。我们还将设置 **dev** 脚本来运行 nodemon，这将允许开发中的热重载。它应该是这样的:

现在，您可以在终端/命令行中使用`npm start`来运行服务器。这将启动节点服务器并运行 Express。或者可以通过运行`npm run dev`使用 Nodemon 为开发启用热重装。

## 你好世界

让我们将服务器设置为工作状态。我们将在您的项目根目录下创建一个名为 server.js 的文件，包括 Express 和它的路由器，并打印出一个“Hello World”样式的声明到网站。你可以在快速网站上找到本教程的版本。我的版本被修改为使用**路线**，而不是直接打印输出:

## 型号

现在，让我们连接到我们的 MySQL 数据库，并开始将信息提取到 Express 中。在 models 文件夹中创建名为`dbconnection.js`的文件:

我们定义想要查询的变量，并使用`query`函数执行 SQL 语句。这将返回一个回调函数，其中一个对象包含结果，另一个包含任何错误报告。

这个 MySQL 包遵循你在 PHP 中从 [PDO 那里看到的同样的原则，你使用准备好的 SQL 语句。那就是当你从来没有直接插入你的变量到一个 SQL 语句中时，你使用占位符像`??`和`?`来表示你想要转义的变量像``table_name` `or` 'value'。你可以在这里找到更多的查询示例。](http://php.net/manual/en/ref.pdo-mysql.php)

现在我们有了获取数据的方法，让我们设置请求数据的路径。

## 路线

当你访问站点的某些部分时，路由告诉服务器显示什么。例如，如果你想在 http://yoursite.com/about/的建立一个“关于”页面，你必须为`/about/`设置一条路线。这个路由会告诉服务器，当有人请求`/about/`时，我们会给他们一个响应(比如 HTML)。

我们将创建一个路由文件，当用户访问站点时，它会向用户显示欢迎文本(就像 Hello World 示例一样)。我们将创建另一个访问应变数据的路径。

我们开始吧！

### 路线

在 routes 文件夹中创建一个名为`index.js`的新文件，并添加以下代码:

我们创建一个接受 Express `app`变量的函数。在函数内部，我们导入控制器，它将实际显示数据。然后我们使用`app`来分配路线。

对于站点索引，我们使用`app.use()`，它输出 HTML。对于菌株，我们使用`app.route`向任何 GET POST 请求发送数据。

现在，当我们访问 http://yoursite.com:4200/strains/的[时，服务器被编程为使用来自`strain`控制器的`getAllItems()`。如果我们现在访问那里，我们将得到一个错误。所以让我们给它点东西展示一下。](http://yoursite.com:4200/strains/)

## 控制器+获取数据

我们需要一个控制器，从模型(MySQL DB)中提取数据，将其转换为 JSON，并将其返回给路由。您可以只创建一个查询数据库的函数。但是我们将创建一个`Class`，这样我们就可以有多种功能(用于将来不同的路线)。键入或复制粘贴以下内容:

要点分解了每一步。我们基本上做一个 SQL 查询，并打印结果或错误。我们使用`res.json()`来发送 JSON 来表示。

## 发布/更新/删除？

我们已经处理了 POST 请求的 GET 部分，但是发送数据或删除呢？使用快速框架很简单:

我们为`get`、`put`和`delete`路线分配不同的功能。在 put 和 delete 函数中，我们使用`req.params.rowId`来拉起`:rowID`，并执行 SQL 语句来删除它们。理想情况下，以某种方式验证请求(密码、OAuth、*或*)。

## 生产

您可以在生产中运行这个服务器，**但是您会注意到一个主要的缺陷**:一旦它崩溃，它就关闭了，并且 API 将无法工作，直到您手动重启服务器命令行样式。我们通过使用一个**进程管理器**来解决这个问题。

我们可以使用服务器来部署我们的应用程序，就像 Heroku T1 一样(这是一个基于云的主机，内置了流程管理器)，或者我们在生产服务器上安装自己的流程管理器。*我们下次再谈赫罗库。*

我用 [PM2](http://pm2.keymetrics.io/) ，但是还有[其他选项](https://expressjs.com/en/advanced/pm.html)。我会在这里报道 PM2。在 PM2 网站上有[快速入门指南，你会在那里找到更多的细节和有用的命令。](http://pm2.keymetrics.io/docs/usage/quick-start/)

*这需要 SSH 访问您的生产服务器。如果你不知道那是什么，我会联系你的主人，看看它是否可用。*

### 上传您的项目

将节点项目上传到您的服务器。您可以复制 node_modules 文件夹，但是建议您在项目文件夹中运行`npm install`。本地 node_modules 文件夹将包含生产服务器可能不需要的开发依赖项。

### 安装 PM2

你必须全局安装 PM2，因为我们将在整个服务器上使用。

`npm install pm2 -g`

### 快速启动

我们可以使用项目文件夹中的以下命令快速启动我们的应用程序:

`pm2 start app.js`

但是让我们创建一个配置文件来将变量传递给应用程序，比如`PORT=4200`，这样我们的应用程序就知道它应该在那个端口上运行。它基本上类似于前面的`package.json`脚本，但是 PM2 使用了不同的结构。

### 创建你的生态系统文件

在您的项目根目录中创建一个名为`ecosystem.json`的文件(其中`server.js`位于此处):

### 运行服务器！

你可以走了！在您的项目文件夹中运行此脚本:

`pm2 start ecosystem.json --env production`

### 检查服务器状态！

我们怎么知道它在运行？它坠毁了吗？我的控制台日志在哪里？！-别担心！PM2 将其数据存储在日志中，我们可以使用以下 shell 脚本来访问这些日志:

*   `pm2 show kushy-api` -显示服务器信息和统计数据。
*   `pm2 logs kushy-api --lines 50` -显示服务器日志的最后 50 行

将 kushy-api 更改为您在生态系统文件中指定的应用程序名称。

## 结论

你可以用它制作任何类型的 API。尽管我心里有足够的空间放 PHP，**一旦你理解了 Node 和 Express，制作一个 API** 就变得异常容易。这感觉比在 PHP 中使用像 [Slim](https://www.slimframework.com/) 这样的库更自然。

如果您有任何问题，请随时[通过 Twitter](http://twitter.com/stayregular420/) 联系我们。

希望有所帮助，奥斯卡

* * *

**继续阅读**

*   [快递](http://expressjs.com)
*   [如何访问 RowDataPacket mysql-node.js](https://stackoverflow.com/questions/38133084/how-to-access-rowdatapacket-mysql-node-js)
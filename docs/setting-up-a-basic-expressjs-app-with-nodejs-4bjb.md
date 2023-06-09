# 使用 NodeJS 设置基本的 ExpressJS 应用程序

> 原文：<https://dev.to/chancesm/setting-up-a-basic-expressjs-app-with-nodejs-4bjb>

## 简介

如果您想开始使用 NodeJS web apps，这里是一个很好的起点。我们将建立一个基本的应用程序，在页面上显示报价。本教程包括以下步骤:

*   设置 NodeJS 项目
*   使用 express 创建 web 服务器
*   使用 EJS 模板引擎库
*   使用 Express 中间件将外部数据发送到我们的模板

注意:这是从 WORDPRESS 博客上复制的，因此一些样式和文本没有被正确的转换

* * *

## 设置

对于本教程，我将假设您已经在开发环境中安装了 NodeJS 和 NPM。如果您没有安装这两个软件，我建议您根据您的操作系统选择以下链接:

*   Windows 操作系统
    *   [http://lmgtfy.com/?q=Install+NodeJS+and+NPM+on+Windows](http://lmgtfy.com/?q=Install+NodeJS+and+NPM+on+Windows)
*   Linux (Ubuntu)和 Mac OS X
    *   [http://node source . com/blog/installing-node-js-tutorial-using-nvm-on-MAC-OS-x-and-Ubuntu/](http://nodesource.com/blog/installing-node-js-tutorial-using-nvm-on-mac-os-x-and-ubuntu/)

对于本教程，我将使用节点版本 8.9.4 和 NPM 版本 5.6.0。

如果安装了节点和 NPM，在终端中运行以下命令将检查版本:

*   节点版本:
    *   `node -v`
*   NPM 版本:
    *   `npm -v`

最后，您应该准备好一个好的代码编辑器。我用的是 Visual Studio 代码。它是一个很棒的编辑器，有内置的文件管理器和集成的终端。你可以从这个网站下载。

既然已经设置好了，是时候开始编码了！

* * *

## 视频(给所有非读者)

[https://www.youtube.com/embed/kIAxZq1rew0](https://www.youtube.com/embed/kIAxZq1rew0)

* * *

## 走查

### 创建节点工程

首先，创建一个您想要工作的目录。我的是~/myapp。使用您的终端，进入这个目录并运行下面的命令:`npm init -y`。

这应该会在您的目录中创建一个 package.json 文件。创建一个与 package.json 文件中的“main”键匹配的文件。我的文件是 index.js。现在我们已经准备好了项目，我们可以继续了。

### 安装依赖项

对于应用程序，我们将使用 Express 作为我们的 web 服务器，因此我们需要使用 npm 安装 Express 模块。从您的项目目录打开一个终端并运行命令`npm i -s express ejs`。您现在应该有一个 node_modules 目录。Express 和 EJS 模板引擎安装在该目录中。

### 创建您的快递应用程序

您需要从 node_modules 文件夹中提取 express，并在 index.js 文件中使用它。为此，将以下几行添加到 index.js 的顶部:

`var express = require('express')
var app = express()` 

这将创建一个使用 express 的应用程序，我们将在接下来的步骤中为其添加功能。

为了运行 express 应用程序，您需要在 index.js 文件的底部添加以下行:

`app.listen(3000)`

这将在" [http://localhost:3000"](http://localhost:3000%E2%80%9D) 运行您的服务器。将这一行保存在 index.js 文件的底部。每当我们向页面添加更多代码时，我们会将其添加到这行代码的上方。

### 服务静态文件

我们需要一个文件夹，我们可以为我们的静态文件(css，图像，javascript 等)服务。)从。

在您的目录中创建一个名为“public”的文件夹。这将保存我们的静态文件。

将下面一行添加到 index.js 文件中:

`app.use(express.static('public'))`

当一个静态资源被请求时，你的应用程序将会在公共文件夹中查找。注意，应用程序在请求中不考虑公共文件夹的名称。例如，如果页面查找“js/test.js”，您的应用程序将在“public/js/test/js”中查找。

### 添加路线

我们的第一个路由将处理对基本路由“/”的简单 GET 请求。这意味着任何专门发送到" [http://localhost:3000"](http://localhost:3000%E2%80%9D) 的 GET 请求都将由这个路由处理。这是我们最终拥有主页逻辑的地方。

首先，将下面几行添加到 index.js 文件中:

`app.get('/', function (req, res) {
res.send('HELLO WORLD')
})`

您可以通过在您的目录中打开一个终端并运行`node index.js`来测试这是否可行。完成后，你可以在浏览器中导航到“ [http://localhost:3000 ”,你应该会看到你的内容。](http://localhost:3000%E2%80%99in)

### 用 EJS 创建视图模板

这需要几个步骤。首先，在应用程序目录中创建一个“视图”文件夹。接下来，在“views”目录中创建一个名为“index.ejs”的文件。将以下内容放入该文件:

` <！DOCTYPE html>
T3】

Quotes .quote{ border: 2px solid black; margin: 5px; }

# 报价:

~

接下来，您必须告诉应用程序在请求'/'路径时呈现该文件。

为此，您首先需要在您的“index.js”文件中添加以下两行，在您声明“app”之前:

`app.set('views', 'views');
app.set('view engine', 'ejs');`

最后，你需要告诉应用程序渲染文件，把你的路线中的`res.send('HELLO WORLD')`替换成下面的一行:

`res.render('index')`

如果您运行您的服务器，它应该向您显示您的模板文件和报价示例。

我们准备好了最后一步。

### 向视图发送数据

为了完成这一步，我们需要做一些设置。首先，我们需要创建一个名为“控制器”的文件夹，其中包含我们所有的数据处理文件和函数。Net，我们将在控制器文件夹中创建一个名为“quotes.js”的文件。在该文件中，我们将添加一些虚拟数据:

`var data = [
{
author: "Chance",
quote: "Cliche web tutorials are lame."
},
{
author: "Yoda",
quote: "Do, or do not. There is no 'try'"
},
{
author: "Ben Franklin",
quote: "A penny saved is a penny earned."
},
{
author: "Dr. Seuss",
quote: "Don't cry because it's over, smile becuse it happened."
}
]`

接下来，我们将导出一个我们的 app 可以用来获取这些数据的函数:

`exports.getData = function() {
return {
quotes: data
}
}`

在' index.js '中，我们需要请求控制器。为此，在您的路线前添加线路`var Quotes = require('./controllers/quotes');`。

现在我们有了数据，我们需要将它发送给我们的视图。将我们路线中的`res.render('index')`替换为:

`res.render('index', Quotes.getData())`

视图现在有数据了。用下面的代码替换示例 div 将允许视图遍历数据并将其打印到页面:

`<% for(quotes of quotes){ %>

~

* * *

## 结论

我们做到了！希望这篇教程能够帮助你掌握一些 Express、EJS 和 NodeJS 的基础知识。

谢谢你花时间通读我的第一篇教程！
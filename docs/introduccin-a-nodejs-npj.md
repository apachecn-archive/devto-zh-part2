# Node.js 简介

> 原文：<https://dev.to/juanmirod/introduccin-a-nodejs-npj>

(这篇帖子最初刊登在我的博客上，请浏览更多关于开发、javascript 和技术的内容:[【出于对代码的热爱】](http://juanmirod.github.io/)

[Node.js](https://nodejs.org/) 是使用 Google V8 引擎的 JavaScript 运行环境。JavaScript 和 Node.js 的故事很有趣，因为几乎一切似乎都是偶然发生的。JavaScript 以[10 天内由 bredan Reich](http://juanmirod.github.io/public/javascript10days.pdf)设计而闻名，并以其[前后不一致](https://www.destroyallsoftware.com/talks/wat)而闻名，但仍成为网络的事实语言。浏览器的技术强制加上极低的学习曲线，使得 JavaScript 成为近来增长最快的语言。jQuery、Mootools 和主干、Lodash、Angular、React 等工具-我...。原则上没有结构的动态语言的架构不断演变。JS 目前是最广泛使用和增长最快的语言之一。因此，JavaScript 开发人员不可避免地希望在浏览器之外使用该语言。《阿特伍德法》规定:

> 任何可以用 JavaScript 编写的应用程序，最终都会用 JavaScript 编写

Node.js 之前曾多次尝试在浏览器之外为 JavaScript 建立运行时环境。程序员希望将 JavaScript 从浏览器中删除，他们希望能够编写应用程序、修改文件和访问硬件。关于 Node.js 和 npm 开头的一个非常有趣的故事是 Issac Z 自己讲述的。schleter(NPM 的创造者)[在他的博客](http://blog.izs.me/post/157295170418/my-first-npm-publish)

**TLDR:** Node.JS 和 npm 成为服务器上的 JavaScript 运行环境。现在，在 web 开发的情况下，我们可以在服务器和客户端上使用相同的语言进行开发，但也有一些人使用 node para iot、para robots、for command line tools 等。

## 安装和复盖

要在 Windows 或 Mac 上安装 Node.js，只需转至主页面并下载安装程序即可。如果使用 Ubuntu，我们只需要几个命令:

```
 curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs 
```

Enter fullscreen mode Exit fullscreen mode

如果使用另一个 Linux 发行版，[请看这里](https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager)。

安装后，我们可以通过键入“
”来验证版本是否正确(当前版本 lt 为 6.x)

```
 node -v 
```

Enter fullscreen mode Exit fullscreen mode

为了运行节点解释器，REPL，我们只需键入命令`node`，终端将成为 JavaScript 控制台，我们可以在其中运行我们的代码。

read eval print loop)是一个控制台，它执行我们给它的 JavaScript 中的每个表达式，并立即返回表达式的结果。例如，如果我们写:

```
 > 2 + 2
4 
```

Enter fullscreen mode Exit fullscreen mode

`4`是`2 + 2`表示式的结果，另一个例子

```
 > console.log('Hola Mundo')
'Hola Mundo'
undefined 
```

Enter fullscreen mode Exit fullscreen mode

‘hello world’是产生‘t0’和‘t1’的输出，是函数返回的。我们还可以定义函数和变量`globales`我们可以用如下方式:

```
 > var factorial  = function(x) {
...   if ( x <= 1 ) return x
...   return x * factorial(x-1)
... } 
undefined
> factorial(4)
24 
```

Enter fullscreen mode Exit fullscreen mode

在 Node.js 的当前版本中，我们几乎支持 2015 年的所有规范，因此我们可以用另一种方式编写上述函数:

```
 > const factorial  = x => ( x <= 1 ) ? x : x * factorial(x-1) 
undefined
> factorial(4)
24 
```

Enter fullscreen mode Exit fullscreen mode

REPL 对于测试小功能和表达式非常有用，我越来越多地使用它，而且这个博客的例子通常是以易于在 REPL 上测试的方式编写的。对代码问题立即作出答复的好处是无价的，我们通常在证明之前不会意识到这一点。

## 模块和 npm

Node 不仅是 REPL，我们还可以运行文件。我们只需创建一个包含要运行的 javascript 代码的文件，并将其传递给命令`node`

```
 echo 'console.log("Hello Node")' > hello.js
node hello.js
// Hello Node 
```

Enter fullscreen mode Exit fullscreen mode

每个 JavaScript 文件都是 Node.js 的一个模块，如果要使用文件中定义的任何函数，必须首先导出该函数。例如，我们创建了文件`factorial.js`，内容如下:

```
 const factorial = x => ( x <= 1 ) ? x : x * factorial(x-1)

module.exports = factorial 
```

Enter fullscreen mode Exit fullscreen mode

如果运行该文件，我们将看到什么都没有发生。

```
 node factorial.js 
```

Enter fullscreen mode Exit fullscreen mode

我们的模块除了定义一个函数并将其导出外，没有其他功能，但从副本本身或另一个 Node.js 文件中，我们可以导入该函数并使用它:

```
 > const factorial = require('./factorial.js')
> factorial(5)
120 
```

Enter fullscreen mode Exit fullscreen mode

是吗？我们已经有了编写代码、将其封装在模块中并运行的机制。这是 Node 开发的基础，就像这样简单。

Node 带来了一系列的基本模块，我们可以作为“[标准库”](https://nodejs.org/dist/latest-v6.x/docs/api/)使用，但 Node.js 的优点之一是由于它的书架很小而保持了灵活性。

这也是 npm 的强项。 [npm](https://www.npmjs.com/) 是 Node.js 和 npm 社区中 Node.js 的集中模块存储库，其理念也是小模块做一件事，类似于 Unix 命令。这使得语言更容易组合、重新排列和修改，具有很大的潜力。npm 现在是所有语言中开源模块数量最多的存储库，其数量继续以高于所有其他语言的速度增长。

npm 与 Node.js 一起安装在我们的系统上，我们可以使用它在项目中全局或本地安装任何软件包。一个项目只是一个我们在其中运行的文件夹`npm init` :

```
 mkdir hello
cd hello
npm init 
```

Enter fullscreen mode Exit fullscreen mode

运行此命令时，程序将询问有关项目的一些问题，并创建一个具有最小设置的文件“`package.json`”。如果我们只想尝试安装一些软件包，我们可以运行“`npm init -y`”来创建此文件，npm 将使用最低默认设置和文件夹名称作为项目名称。

现在，我们可以通过运行`npm install`安装任何注册表包，例如，我们可以安装[【expressjs】](http://expressjs.com)，一系列库来创建 web 服务器:

```
 npm install --save express 
```

Enter fullscreen mode Exit fullscreen mode

修饰符“`--save`”指示 npm 我们希望将此依赖项保存到项目文件中。通过本地安装 express，我们可以创建包含以下内容的文件`index.js`:T2]

```
 const express = require('express')
const app = express()

app.get('/', function (req, res) {
  res.send('Hola desde Node!')
})

app.listen(3000, function () {
  console.log('Servidor creado y escuchando en el puerto 3000!')
}) 
```

Enter fullscreen mode Exit fullscreen mode

并在控制台上运行:

```
 node index.js

Servidor creado y escuchando en el puerto 3000! 
```

Enter fullscreen mode Exit fullscreen mode

如果打开浏览器并转到“localhost:3000”，您将看到“从节点打招呼！”消息

这些是 node . js . módulos、运行时环境、npm 中央存储库和 JavaScript 中的基本开发工具。使用您知道的内容，您可以稍微浏览 npm 注册或 express 文档，并开始开发您自己的 web 服务器:d
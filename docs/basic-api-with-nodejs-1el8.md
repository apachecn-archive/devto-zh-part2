# 带有 NodeJs 和 ES6 的简单 Restful Api。

> 原文：<https://dev.to/ogwurujohnson/basic-api-with-nodejs-1el8>

[![nodebanner](../Images/ef745a833233f026067a67b0a0f50879.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TcvC0-R3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vi4j2ga0ubftabyp1f9e.png) 
**注意点**:贯穿本文的所有 JavaScript 代码都将遵循 ECMA 脚本 2015 标准编写。如果你有 JavaScript 和 Es6 的先验知识就更好了，是的，我知道 NodeJs 很热门，你想马上开始，但是你知道吗，我们只需要一个先决条件选项卡；

**先决条件** :
1。JavaScript 知识
2。EcmaScript 2015 的知识，通常称为 ES6
3。对 NodeJs 的好奇以及它是如何工作的
4。享受用 JavaScript (LOL)
5 编写 hello world 程序的乐趣。安装 NodeJs
6。安装邮递员

好吧，先不说先决条件，我们开始挖吧。首先，我们希望明确区分什么是 NodeJs，什么不是。通读几个朋友之间的对话，看看你是否能猜出谁是对的:

马克:嘿，大卫，这是什么语言？

大卫:嘿，马克，这是诺杰斯。

**Mark** :是新的 JavaScript 框架吗？

大卫:哦，是的，这是一个新的 JavaScript 框架，或者我应该说，可能是一个库，虽然不确定，但它最初是由 Ryan Dahl 在 2009 年编写的，大约在第一个服务器端 JavaScript 环境 Netscape 的 LiveWire Pro Web 推出 13 年后。最初的版本只支持 Linux 和 Mac OS X。它的开发和维护由 Dahl 领导，后来由 Joyent 和开源社区赞助....啦啦啦

**史蒂夫** : *谷歌搜索*但是我刚刚从谷歌上看到 NodeJs 是一个运行时环境，它既不是框架也不是库，但是 NodeJs 是一个运行时环境是什么意思呢？

大卫:嗯，我不知道，你知道吗，我们打电话问问他。

`*Now that's my cue as Johnson to blow their mind.*`

NodeJs 既不是一个库也不是一个框架，而是一个运行时环境。对于那些想知道什么是运行时环境的人，我将使用一个来自我们不太远的亲戚 java 的例子。在用 Java 运行代码和编译之前，您需要一个 Java 运行时环境(JRE ),它通常打包在 Java 开发工具包(JDK)中，如果您的系统上没有安装这个工具，您可能无法编译和运行编写良好的 Java 代码。本质上,( JRE)使我们有可能在我们的系统上运行 Java 程序，这同样适用于 NodeJs，我们知道 javascript 只能在浏览器上运行，但是 NodeJs 使我们有可能在我们的 PC 上运行我们的 Javascript 程序，事实上帮助我们在浏览器之外运行 Javascript。为了理解我的意思，在你的系统上安装 NodeJs 之前，启动你的 visual studio 代码，写一个控制台语句并试着运行它，是的，你肯定会看到一个错误，现在安装 NodeJs 并重新加载你的 vscode，试着再次运行相同的程序，会发生什么。

现在相信我们已经确定了 NodeJs 实际上是什么，我们不会不提到 NodeJs 运行在 chromes *V8 引擎*上。 *V8* 是最初为谷歌 Chrome 打造的 JavaScript 执行引擎。2008 年，谷歌将其开源。用 C++编写， *V8* 将 JavaScript 源代码编译成本机代码，而不是实时解释。

好了，回到今天的主题，*创建一个节点 API* 。

**用 NodeJs 和 ES6 创建一个简单的 Restful API**

首先，我们需要在我们的系统上安装 Nodejs，如果您还没有这样做的话。

下载节点👉[此处](https://nodejs.org/en/download/)。
T3![nodejs download api](../Images/27abcc699a715bc674a6b7b47a05d005.png)T5】

安装好 fire up 代码编辑器后，让我们开始创建 api。

创建一个项目目录，我将其命名为 mine nodeApi

在项目目录中创建一个 index.js 文件

打开您的命令窗口并导航到您的项目目录，或者我应该说 CD 进入您的项目目录

[![cd into](../Images/1b32b30c0c0f94c95e9234ca35801c5a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gQipU_y---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5m5lnks6tn2kzr3cou8j.PNG)

在您的项目目录中，运行 npm init，您可以在所有提示中单击 enter，让 npm 使用默认设置设置您的项目，或者您可以编辑诸如作者和描述之类的内容，您可以在稍后创建 package.json 文件时编辑这一部分。

[![npm init](../Images/4be51392552b9587fbdc86f2eeacf9da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6zh6mHSX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7p3nlygy7j45ijywx2ci.PNG)

在上面的步骤之后，检查您的项目文件夹，一个新的文件 package.json 已经被添加，您可以在这个文件中进行上面讨论的那些更改。

[![added package.json](../Images/08771c1def287ff2f19cd09545fa4abc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n7HJ3Vjq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vh5yxh2ry9arktifvmfr.PNG)

现在让我们开始编写我们的 API。NodeJs 有一个名为 HTTP 的内置模块，它允许 NodeJs 通过超文本传输协议(HTTP)传输数据。要包含 HTTP 模块，请使用 require()方法:

在 index.js 文件中键入以下内容；

```
const http = require('http');
const port = 3000;
const hostname = '127.0.0.1';

const server = http.createServer((req,res)=>{
    res.statusCode = 200;
    res.setHeader('Content-Type','text/plain');
    res.end('Hello World\n');
});

server.listen(port,hostname,()=>{
    console.log(`Serving running at http://${hostname}:${port}/`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

*从上面的☝️中，我们将使用 Http 模块中的 createServer 方法来创建一个 http 服务器。当有人试图通过端口 3000 访问计算机时，将执行传递到 http.createServer()方法中的函数。该函数有两个参数`req`和`res`，分别代表`request`和`response`。保存代码，回到你的命令提示符，使用这个命令运行我们的项目*、`node index.js`。

[![first run](../Images/9743387204474e2f12ec711291b7b24b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YWDGnXhW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y7xz89g6c5ieojhojahj.PNG) 
现在我们的服务器正在监听端口 3000，把你提示上的那个 URL 复制到你的浏览器或者邮差那里，向它发送一个请求，你应该会看到下面的内容；

[![first request](../Images/6413662611bb7dbba066b988a70ba25a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gSENTxgl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4k14jw0ghasllkm3bcce.PNG)

在 node 上创建一个服务器并让它监听请求实际上很简单。现在让我们创建端点，应用程序的用户将使用这些端点与我们的系统进行通信。这也很容易，在你的代码编辑器中输入下面的代码；

```
const http = require('http');

const server = http.createServer((req,res) => {
    if(req.url === '/'){
       res.send('Welcome');
    }

    if(req.url === '/courses'){
       res.send('welcome to courses');
    }
});

server.listen(3000);
console.log('Server listening at port 3000...'); 
```

Enter fullscreen mode Exit fullscreen mode

在创建服务器时，我们在端点上监听请求，当发出请求时，我们检查`re.url`以确定系统应该给出什么响应。再次启动我们的服务器，这次发送请求到邮差`http://127.0.0.1:3000/`和`http://127.0.0.1:3000/courses`的端点，你应该看到我们发送的响应分别是*欢迎*和*欢迎参加课程*。

但是用这种方式创建端点会非常混乱，很难组织，而且在代码中丢失的可能性很高，这就是 expressJs 作为一个构建在 NodeJs 之上的框架发挥作用的地方，还有其他框架，如 sailsjs、Koa 等。

返回到您的命令提示符，使用这个命令
`npm install express --save`安装 express，然后输入

[![install express](../Images/13cf34031ef16abd593f56ce5f7a0672.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2tuHhGf3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gnp545ofmgkpwb1xdftb.PNG)

现在，要使用 express，我们必须导入它，然后使用它附带的一些方法来创建端点。

在编辑器中键入以下代码；

```
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Welcome');
});

app.get('/courses', (req, res) => {
  res.send('welcome to courses');
});

const hostname = '127.0.0.1';
const port = 3000;
app.listen(port, () => {
  console.log(`Serving running at http://${hostname}:${port}/`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这就是用 NodeJS 编写 API 端点有多容易。当你输入完代码后，像往常一样运行它，看看代码，试着理解它。有任何问题，请在评论区提出。

对于进一步的阅读，我推荐以下文章。 [NodeJs By w3 学校](https://www.w3schools.com/nodejs/default.asp)T3】2。[Moz 开发网](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs)的 NodeJs，我也参与了这个教程。
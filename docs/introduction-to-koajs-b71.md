# Koa.js 简介

> 原文：<https://dev.to/mubaris/introduction-to-koajs-b71>

[![Banner](img/d093cb6b70d527790c103316b7c8976d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hq_aPTqb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/gJXT4iu.png)

最初发布在我的[博客](https://mubaris.com/2018/09/29/koa-basics/)

[Koa.js](https://koajs.com/) 是由 [Express.js](https://expressjs.com/) 背后的团队开发的一个极小的 Node.js web 框架。Koa 使用异步函数，这比回调函数更有优势。默认情况下，Koa 没有附带任何中间件。这使得 Koa 非常简洁优雅。在这篇文章中，我们将开始使用 Koa.js 构建一个 API

Koa 要求**节点 v7.6.0** 或更高版本的 **ES2015** 和**异步**功能支持。

### 先决条件

*   Node.js 知识
*   ES6 语法熟悉度

## 我们在建造什么？

在 Koa 的帮助下，我们将构建一个简单的[情感分析](https://mubaris.com/2017/11/04/movie-sentiment-analysis/) API，它将文本作为输入，并将情感得分作为输出。我们将使用下面的 NPM 包来构建这个 API。

*   [Koa](https://www.npmjs.com/package/koa) - Web 框架
*   [Koa 路由器](https://www.npmjs.com/package/koa-router) -用于路由
*   [Koa 主体解析器](https://www.npmjs.com/package/koa-bodyparser) -解析请求主体
*   [感悟](https://www.npmjs.com/package/sentiment)——分析文本

让我们开始构建这个 API。

## 你好世界

我们从一个最简单的例子开始。首先，我们需要安装 Koa。创建一个新目录，我们将运行以下命令来安装 Koa。

```
yarn add koa 
```

Enter fullscreen mode Exit fullscreen mode

hello world 的例子很简单，

```
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
    ctx.body = 'Hello World';
});

app.listen(3000, () => {
    console.log('Server started on localhost:3000');
}); 
```

Enter fullscreen mode Exit fullscreen mode

第一行是导入 Koa。在下一行中，我们初始化 Koa 应用程序。

`app.use(function)`是中间件。每个发送到服务器的请求都会调用这个函数。我们将主体设置为“Hello World”。因此，在每条路线上，我们都会得到“Hello World”的响应。最后，我们正在监听端口号 3000。

## Koa 中间件

在 Koa 中创建一个定制的中间件非常容易。在上一节中，我们使用了`app.use(function)`，这个函数可以用来创建一个 Koa 中间件。Koa 中间件以类似堆栈的方式流动，允许您在下游执行操作，然后在上游过滤和操作响应。Koa 中间件是返回带有签名`(ctx, next)`的`MiddlewareFunction`的简单函数。中间件运行时，必须手动调用`next()`来运行“下游”中间件。

```
const Koa = require('koa');
const app = new Koa();

app.use(async (ctx, next) => { 
    console.log('1'); 
    await next(); 
    console.log('2');
});
app.use(async (ctx, next) => {
    console.log('3'); 
    await next(); 
    console.log('4');
});
app.use(async (ctx, next) => { 
    console.log('5');
    ctx.body = 'Hello World';
    console.log('6');
});

app.listen(3000, function(){ 
    console.log('Server started on localhost:3000');
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您在浏览器上点击`localhost:3000`,您将得到下面的控制台输出。这个过程是这样的，

*   浏览器向服务器发送请求
*   第一个中间件被调用，打印“1”
*   第一个中间件使用`next()`调用下一个中间件。第一个暂停，执行到下一个
*   第二个中间件被调用，输出“3”
*   `next()`，第二次停顿
*   第三个中间件被调用，输出“5”
*   第三个中间件将响应发送回浏览器“Hello World”
*   第三个继续，打印“6”，执行向上。
*   第二个中间件继续，打印“4”，执行向上。
*   第一个中间件继续，打印“2”。

```
Server started on localhost:3000
1
3
5
6
4
2 
```

Enter fullscreen mode Exit fullscreen mode

Koa 中间件可以用于日志记录、异常处理、身份验证等等。这里是来自 Koa Wiki 的中间件列表。

让我们继续构建情感分析 API。

## 输入情绪

我们将使用一个名为 [`sentiment`](https://www.npmjs.com/package/sentiment) 的 Node.js 库来计算情感得分。这个库执行基于模糊神经网络的情感分析。它附带了一个带有预定义分数的单词列表。对于每一个句子，它会找到情感中所有单词的平均情感分数。它给出的分数范围是-5 到 5，这里-5 是最消极的，5 是最积极的。我们先从安装`sentiment`开始。

```
yarn add sentiment 
```

Enter fullscreen mode Exit fullscreen mode

让我们看一个它如何工作的例子

```
const Sentiment = require('sentiment');
const sentiment = new Sentiment();
let result = sentiment.analyze('Cats are amazing.');
console.log(result);
/*
{ score: 4,
    comparative: 1.3333333333333333,
    tokens: [ 'cats', 'are', 'amazing' ],
    words: [ 'amazing' ],
    positive: [ 'amazing' ],
    negative: [] }
*/
result = sentiment.analyze('Cats are lazy');
console.log(result);
/*
{ score: -1,
    comparative: -0.3333333333333333,
    tokens: [ 'cats', 'are', 'lazy' ],
    words: [ 'lazy' ],
    positive: [],
    negative: [ 'lazy' ] }
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这里的`score`是所有词的情感得分之和，`comparative`是平均得分。我们对`comparative`的分数感兴趣。

让我们将情感分析与我们的 Koa 应用程序集成起来。

## Koa +人气

我们需要安装中间件`koa-router`来使用 Koa 中的路由，安装中间件`koa-bodyparser`来解析请求体。让我们安装这些与，

```
yarn add koa-router koa-bodyparser 
```

Enter fullscreen mode Exit fullscreen mode

现在我们正在构建最终的 API。我们将使用以下 API 配置。

*   在`/analyze`发布请求
*   格式为`{"text": "The text to be analyzed"}`的 JSON 请求体
*   格式为`{"text": "The text to be analyzed", "score": 0.3}`的 JSON 响应
*   情绪得分在-1 到 1 的范围内，而不是-5 到 5

```
const Koa = require('koa');
const Router = require('koa-router');
const Sentiment = require('sentiment');
const bodyParser = require('koa-bodyparser');

const app = new Koa();
const router = new Router();
const sentiment = new Sentiment();

// Analyze a text and return sentiment score in the range of -1 to 1
function analyze(text) {
    const result = sentiment.analyze(text);
    const comp = result.comparative;
    const out = comp / 5;
    return out;
}

// Use bodyparser middleware to parse JSON request
app.use(bodyParser());

// Define POST request route to analyze the text
router.post('/analyze', async (ctx, next) => {
    // Look for text property on request body
    const text = ctx.request.body.text;
    if (text) {
        // Analyze the given text
        const score = analyze(text);
        // Send response
        ctx.body = {
            text,
            score
        };
    } else {
        // Send error if there's not text property on the body
        ctx.status = 400;
        ctx.body = {
            "error": "Please provide a text to analyze"
        };
    }
});

// Use Koa Router middleware
app
    .use(router.routes())
    .use(router.allowedMethods());

// Finally, start the server
app.listen(3000, function(){
    console.log('Server started on localhost:3000');
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的情感分析 API。我们会一行一行的来。

*   首先，我们导入必要的库并初始化它们。
*   `analyze()`接受文本输入并返回-1 到 1 范围内的情感分数
*   `app.use(bodyParser())`，我们告诉 Koa 使用 bodyparser 中间件来解析 JSON 请求
*   我们定义`/analyze`路线来分析文本。此路由仅接受 POST 请求。
*   `/analyze` route 中的函数试图从请求中获取文本属性。如果可以的话，将带有分数和文本的回复发送给客户端。如果没有，我们发回一个错误。
*   我们告诉 Koa 使用路由器中间件
*   最后，用`listen`启动服务器

这就结束了我们使用 Koa.js 的情绪分析 API。在 Koa 教程的第 2 部分中，我们将介绍日志记录和向我们的情感分析 API 添加分析。
# 如何在 Node.js 中创建自己的 AWS Lambda 中间件框架？

> 原文：<https://dev.to/uday_rayala/how-to-create-your-own-aws-lambda-middleware-framework-in-nodejs-2fkb>

[![](img/4eb3595702e258cb4f17c5cc32ef57ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F0Hi_fhw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/415/1%2AO4r5gQQQ37s0Lc4pTYbE3g.png)T3】AWS Lambda 中间件框架

我们大约有 25 辆 lambdas，它们有一些共同的功能，比如:

*   与哨兵集成
*   删除不必要的事件
*   记录有关正在处理的事件的信息
*   数据狗跟踪等。

我们需要一种方法来定义这些功能，并在 lambdas 中重用它们。这篇文章是关于一个我们用 Node.js 编写的中间件框架，它只需要很少的代码，没有外部库。

### 中间件模式

我们可以将这些功能提取到一个函数中，并在 lambdas 中调用它，这将允许我们重用代码。但我们认为，如果我们能引入一些约定，将这些通用功能集成/维护到 lambdas 中会容易得多。

在使用了许多 web 框架之后，我们觉得类似的方法也适用于我们，这些框架的概念包括 java 中的过滤器、Express 中的中间件和 T2。

想法是实现一个 lambda 处理函数作为核心函数和一组中间件函数。

*   核心函数将做 lambda 应该做的事情，比如转换传入数据、将事件写入数据存储等。
*   每个中间件将处理一个功能，如集成哨兵或日志等。

中间件的调用顺序如下:

```
MiddleWare1 -> MiddleWare2 -> MiddleWareN-> CoreFunction 
```

中间件具有与 lambda 处理函数相同的签名，并带有一个额外的参数 *next* 中间件。在最后一个中间件的情况下， *next* 参数将是核心函数。如果核心函数返回某个值，中间件通常会返回相同的值。

并且每个中间件功能可以选择何时调用下一个中间件。这样，我们可以将中间件功能分为三个部分:

*   Before 部分—此代码在核心函数之前执行。我们可以做一些事情，比如过滤事件，给上下文添加更多的数据等等。
*   下一个中间件部分——这可能是对核心函数的实际调用。中间件可以选择不调用核心函数，而是完成 lambda 的执行。
*   After Section —此代码在核心函数之后执行。我们可以做错误处理、记录或返回不同的响应等事情。

### 承诺而不是回调

我们的大多数 lambda 函数代码创建或使用 promises，因为它们主要查询/写入外部系统，如 Dynamodb、Elasticsearch 等。我们认为如果我们的核心函数和中间件使用承诺而不是回调会更容易。

Node.js v6.10 中典型的 AWS lambda 定义如下所示:

```
exports._myHandler_ = function(event, context, callback){
   // Do something
   // callback(null, "some success message");
   // or
   // callback("some error type");  
} 
```

这就是我们想要的处理函数的样子:

```
const handlerFunction = (event, context) => {
  return new Promise()
} 
```

***注意:*** *Node.js v8.10 支持异步处理程序，这在我们编写这个中间件框架的时候是没有的。这个步骤在 Node.js v8.10 版中可能是多余的*

### 中间件编排器

因为我们有一个与 lambda handler 不同的签名，所以我们用中间件创建了一个函数。

它将核心函数和一组中间件作为输入，并返回一个与 lambda 处理程序具有相同签名的函数。

```
export._myHandler_ = withMiddlewares(
  handlerFunction, 
  [Middleware1(), Middleware2()]
) 
```

这是 withMiddlewares 的实现:
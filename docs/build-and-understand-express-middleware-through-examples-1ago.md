# 通过示例构建并理解 Express 中间件

> 原文：<https://dev.to/oktadev/build-and-understand-express-middleware-through-examples-1ago>

如果您在过去的七八年中做过任何重要的节点开发，您可能在某个时候使用过 [Express](https://expressjs.com) 来构建 web 服务器。虽然您可以在 Node 中创建一个服务器而不使用库，但是它并没有提供太多现成的东西，而且添加功能也很麻烦。Express 是一个极简的、“非个人化的”服务器库，并且已经成为在 Node 中构建 web 应用程序的事实上的标准。要理解 Express，你需要理解 Express 中间件。

## 什么是快递中间件？

中间件的字面意思是你放在软件的一层和另一层中间的任何东西。Express 中间件是在对 Express 服务器的请求的生命周期中执行的功能。每个中间件都可以访问它所连接的每个路由(或路径)的 HTTP `request`和`response`。事实上，Express 本身完全妥协于中间件功能。此外，中间件既可以终止 HTTP 请求，也可以使用`next`将请求传递给另一个中间件功能(稍后会有更多相关内容！)中间件的这种“链接”允许您划分代码并创建可重用的中间件。

在本文中，我将解释什么是中间件，为什么你会使用它，如何使用现有的 Express 中间件，以及如何为 Express 编写你自己的中间件。

## 要求编写快递中间件

创建、使用和测试 Express 中间件需要安装一些东西。首先，你需要节点和 NPM。为了确保您已经安装了它们，您可以运行:

```
npm -v && node -v 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到已经安装的节点和 NPM 版本。如果出现错误，需要[安装节点](https://nodejs.org/en/)。在撰写本文时，我使用的是这两个版本的最新版本，即 Node 10.9.0 和 NPM 6.4.1，但是所有示例都应该适用于 Node 版本 8+和 NPM 版本 5+。

我还将使用 Express 版本。这一点很重要，因为从 3.x 版本到 4.x 版本进行了重大更改。

安装 [Postman](https://www.getpostman.com/) 来使用除了`GET`之外的任何 HTTP 动词测试路由也会很有帮助。

## 快递中间件:基础知识

首先，您将使用最基本的 Express 内置中间件。这将让您有机会了解中间件是如何使用的，以及 Express 中间件是如何构建的。

创建一个新项目并`npm init`它…

```
npm init
npm install express --save 
```

Enter fullscreen mode Exit fullscreen mode

创建`server.js`并粘贴以下代码:

```
const express = require('express');
const app = express();

app.get('/', (req, res, next) => {
  res.send('Welcome Home');
});

app.listen(3000); 
```

Enter fullscreen mode Exit fullscreen mode

通过`node server.js`运行服务器，访问`http://localhost:3000`，你应该会在你的浏览器中看到“欢迎回家”。

`app.get()`函数实际上是内置的 Express 中间件。您会注意到传递给该方法的参数是`req`、`res`和`next`。它们是传入的请求、正在编写的响应和中间件完成后要调用的方法。在这种情况下，一旦响应被发送，函数就退出，所以不需要调用`next()`方法。您还可以通过调用`next()`方法来链接其他中间件。

让我们再看几个不同类型的中间件的例子。

## 快速请求日志中间件示例

在 Express 中，可以将中间件设置为“全局”中间件；这意味着它将被每个传入的请求调用。

将`server.js`的内容改为:

```
const express = require('express');
const app = express();

app.use((req, res, next) => {
  console.log(req);
  next();
});

app.get('/', (req, res, next) => {
  res.send('Welcome Home');
});

app.listen(3000); 
```

Enter fullscreen mode Exit fullscreen mode

这一次，当您转到`http://localhost:3000`时，您应该在浏览器窗口中看到相同的内容，但是回到控制台窗口，您将看到传入请求对象的输出。

中间件注销请求对象，然后调用`next()`。管道中的下一个中间件处理对根 URL 的 get 请求，并发回文本响应。使用`app.use()`意味着这是全局中间件，每次调用都会被调用。

## 限制快递请求内容类型示例

除了为所有调用运行中间件之外，您还可以指定只为特定调用运行中间件。

再次将`server.js`文件更改为:

```
const express = require('express');
const app = express();

const requireJsonContent = () => {
  return (req, res, next) => {
    if (req.headers['content-type'] !== 'application/json') {
        res.status(400).send('Server requires application/json')
    } else {
      next()
    }
  }
}

app.get('/', (req, res, next) => {
  res.send('Welcome Home');
});

app.post('/', requireJsonContent(), () => {
  res.send('You sent JSON');
})

app.listen(3000); 
```

Enter fullscreen mode Exit fullscreen mode

这一次，通过运行:
启动服务器

```
node server.js 
```

Enter fullscreen mode Exit fullscreen mode

为了测试这一点，打开 Postman 并为`http://localhost:3000`创建一个 post 请求。不要设置任何头并运行请求。您将返回“服务器需要应用程序/json”消息。

现在返回添加值为`application/json`的`Content-Type`头，并再次运行请求。您将从服务器获得“您发送了 JSON”消息。

这个`app.post()`方法调用添加了`requireJsonContent()`中间件功能，以确保传入的请求有效负载具有设置为`application/json`的`Content-Type`头值。如果它没有通过检查，就会发送一个错误响应。如果是的话，请求就会通过`next()`方法传递给链中的下一个中间件。

## 第三方快递中间件

到目前为止，您已经构建了几个定制的中间件，但是已经构建了许多包来做您通常想做的事情。事实上，您已经通过使用`app.get()`或`app.post()`中间件函数使用了简单的路由中间件库。有数以千计的中间件库用来做诸如解析传入数据、路由和授权之类的事情。

Okta 有一个用于 OIDC 安全的 Express 中间件，我将向您展示如何使用第三方中间件库。

### 为什么 Okta 适用于快递申请

在 Okta，我们的目标是让[身份管理](https://developer.okta.com/product/user-management/)比你习惯的更容易、更安全、更可扩展。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

## Okta 的 OIDC 快递中间件

要安装 Okta 的 OIDC 中间件 Express，运行:

```
npm install @okta/oidc-middleware --save 
```

Enter fullscreen mode Exit fullscreen mode

然后在`server.js`文件中，创建一个带有一些配置选项的中间件实例，以便 Okta 知道如何连接到 Okta 应用程序。

```
const oidc = new ExpressOIDC({
  issuer: 'https://{yourOktaDomain}/oauth2/default',
  client_id: '{yourClientId}',
  client_secret: '{yourClientSecret}',
  redirect_uri: 'http://localhost:3000/authorization-code/callback',
  scope: 'openid profile'
}); 
```

Enter fullscreen mode Exit fullscreen mode

您还需要告诉 Express 使用 OIDC 中间件路由器，而不是默认路由器。

```
app.use(oidc.router); 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以像使用任何其他中间件一样使用它:

```
app.get('/protected', oidc.ensureAuthenticated(), (req, res) => {
  res.send('Top Secret');
}); 
```

Enter fullscreen mode Exit fullscreen mode

`oidc.ensureAuthenticated()`函数是 Okta 库中的一个中间件。它运行一个函数来查看当前用户是否登录。如果是，它调用`next()`让`app.get()`函数继续处理请求。如果不是，它将发回一个`HTTP 401 (Unauthorized)`响应。

## 中间件顺序很重要

当 Express 收到一个请求时，每个与请求匹配的中间件都按照初始化的顺序运行，直到出现一个终止动作(比如发送一个响应)。

[![Middleware Flow](../Images/7a982a999431573d7b7e5b8a1168649a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yuzjeMN6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/express-middleware-examples/middleware-30b3b30ad54e21d8281719042860f3edd9fb1f40f93150233a08165d908f4631.png)

因此，如果发生错误，所有打算处理错误的中间件将被按顺序调用，直到其中一个调用终止事件，如`res.send()`或`res.end()`。

## 了解更多关于 Express 中间件的信息

关于设置 Okta OIDC 中间件的详细说明，您可以遵循 [ExpressJS 快速入门](https://developer.okta.com/quickstart/#/okta-sign-in-page/nodejs/express)。

在这个 [GitHub repo](https://github.com/senchalabs/connect#middleware) 中还有一个官方支持的 Express 中间件列表，您可以试用并深入了解更多信息

最后，如果您有兴趣了解更多关于如何使用 Okta 的知识，有一个 [Okta Node SDK](https://github.com/okta/okta-sdk-nodejs) 可以在您的应用程序中实现更多的用户管理功能。

一如既往，我很乐意在评论或 twitter @oktadev 上听到你的想法和问题！
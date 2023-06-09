# 通过 Micro，GraphQL 授权创建模拟 API 服务器

> 原文：<https://dev.to/sasurau4/create-mock-api-server-with-authorization-by-micro-graphql-51h8>

如今，现代 web 应用程序分为两部分。
一种是 web 浏览器、iOS 或 Android 应用等客户端。
再一个是服务器端。
它们通过 web API 连接。

如果我们定义像 GraphQL 或 Swagger 这样 API 模式，这种架构使我们能够并行开发前端和后端。

并行开发似乎比其他方法更好更快，但它会导致前端开发的问题。
没有后端实现的情况下如何开发前端？

一个答案是从模式模拟后端服务器。

因此，本文介绍了如何使用 json-web-token 授权来模拟 GraphQL 服务器。
这里的代码是

## 工具

*   [微型开发](https://github.com/zeit/micro-dev)
*   [阿波罗-服务器-微](https://github.com/apollographql/apollo-server/tree/master/packages/apollo-server-micro)
*   [node-jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)

## 我选择 micro 作为模拟服务器的原因

微型开发功能非常有用。热重载使我们可以更快地编写模拟服务器，标准输出中漂亮的日志对于调试非常方便，其他一些特性也很好。

当然，我们也可以使用与 express-server 的微开发相同的功能。要启用热重装，请安装 webpack 和一些相关的 npm 模块，修改 webpack 配置和其他一些内容。
启用漂亮日志，应用中间件，自定义摩根输出。
他们有点复杂。

所以，micro-dev 默认有这些特性。

## 菜谱

### 所有路线

```
module.exports = router(
  post('/auth/login', loginHandler),
  post('/graphql', graphqlHandler),
  get('/graphql', graphqlHandler)
); 
```

Enter fullscreen mode Exit fullscreen mode

只有两条路径，一条是登录路径，另一条是 graphql 路径。
微本身没有路由器，就用微路由器。
这个很简单！

### 登录处理程序

```
const loginHandler = async (req, res) => {
  const body = await json(req);
  const { email, password } = body;

  // check correct user's email and password set
  if (!isUser({ email, password })) {
    return send(res, 401, 'Incorrect email or password');
  }

  const access_token = createToken({ email, password });
  return send(res, 200, { access_token });
}; 
```

Enter fullscreen mode Exit fullscreen mode

LoginHandler 只需检查请求正文中的电子邮件和密码组合是否正确。
如果通过检查，用 access_token 发送 200。

关于 jwt 授权的更多细节，参见 [auth.js](https://github.com/sasurau4/mock-micro-graphql-auth/blob/master/auth.js)

### 图形 SQL 处理程序

```
const apolloHandler = apolloServer.createHandler();

const graphqlHandler = async (req, res) => {

  const { authorization } = req.headers;

  // check authorization format check
  if (authorization === undefined || authorization.split('  ')[0] !== 'Bearer') {
    return send(res, 401, { error: 'Error in authorization format' });
  }

  // check token
  const { error } = await verifyToken(authorization.split('  ')[1]).catch(e => ({
    data: null,
    error: e,
  }));

  if (error) {
    console.log(error);
    return send(res, 401, { error: 'Error access_token is revoked' });
  }

  return apolloHandler(req, res);
}; 
```

Enter fullscreen mode Exit fullscreen mode

GraphQL 处理程序简单地检查请求报头中的承载令牌是否有效。如果通过检查，则返回 apollo-server-micro 中定义的 apolloHandler。

本文推荐 GraphQL 资源和模式，参见 [apollo-server.js](https://github.com/sasurau4/mock-micro-graphql-auth/blob/master/apollo-server.js) 。

注意:
您希望在浏览器中看到 GraphQL playgruond 来尝试查询。
增加了一些检查和 apolloHandler 的提前返回。
在我的[回购](https://github.com/sasurau4/mock-micro-graphql-auth)中，检查用户代理是否为 Chrome。

## 结论

使用 API 模拟服务器的微开发服务器对开发人员的体验非常好。没有配置，好看的日志，热重装。

如果你对微感兴趣，请看[牛逼-微](https://github.com/amio/awesome-micro)！
感谢阅读。

## 引用

这篇文章受到下面文章的启发。谢谢！
[https://qiita.com/acro5piano/items/d421e2d41ee15e20e1de](https://qiita.com/acro5piano/items/d421e2d41ee15e20e1de)
[https://qiita.com/oz4you/items/3f3223048bd2109de47b](https://qiita.com/oz4you/items/3f3223048bd2109de47b)
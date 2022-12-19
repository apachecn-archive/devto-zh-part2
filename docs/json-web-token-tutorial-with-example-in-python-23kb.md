# 带有 Python 示例的 JSON Web Token 教程

> 原文：<https://dev.to/apcelent/json-web-token-tutorial-with-example-in-python-23kb>

网站认证的传统模式是使用基于 cookie 的认证。在典型的 REST 架构中，服务器不保存任何客户端状态。REST 的无状态方法使得会话 cookies 从安全角度来看是不合适的。在使用 cookies 来保护 REST 服务时，会话劫持和跨站点请求伪造是常见的安全问题。因此，需要认证和保护无状态 REST 服务。

在本文中，我们将学习如何使用 JSON Web 令牌来保护我们的 REST API。JSON Web 令牌是一种开放的行业标准 [RFC 7519](https://tools.ietf.org/html/rfc7519) 方法，用于在双方之间安全地表示声明。JWT 碰巧得到了 Firebase、谷歌、微软和 Zendesk 等公司的支持。

## 用比喻理解

我们都有一张借记卡。一旦插入自动取款机，我们就可以提取这笔钱。借记卡只能使用我的账户，一旦过期就不能使用。JSON Web 令牌与此类似，您将令牌插入一个身份验证系统，就可以访问属于您的受限数据。

## JWT 的工作

当使用 JWT 进行身份验证时，您通常会将令牌存储在浏览器的本地存储或会话存储中。要注销，您只需移除令牌。没什么别的可以作废了。使用这种方法进行身份验证的好处之一是，令牌不会持久存储在数据库中，因此在进行身份验证时，您不必查询会话存储。

让我们借助这个简单的插图来看看它

[![Alt text of image](img/5f1b1f696d93aa0bf5dd9d13ceab9169.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TkU95Ghs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/json-web-token-authentication-apcelent.png)

## JWT 的结构

JSON Web 令牌示例:

 `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ0b3B0YWwuY29tI iwiZXhwIjoxNDI2NDIwODAwLCJodHRwOi8vdG9wdGFsLmNvbS9qd3RfY2xhaW1zL2lzX2FkbWluI jp0cnVlLCJjb21wYW55IjoiVG9wdGFsIiwiYXdlc29tZSI6dHJ1ZX0.yRQYnWzskCZUxPwaQupWk iUzKELZ49eM7oWxAQK_ZXw` 

因为有 3 个部分由 a .分隔，所以每个部分都是以不同方式创建的。我们有三个部分:

*   页眉
*   有效载荷
*   签名

 `<base64-encoded header>.<base64-encoded payload>.<base64-encoded signature>` 

**表头**

JWT 头声明编码对象是一个 JSON Web 令牌(JWT ), JWT 是一个 JWS，使用 HMAC·SHA-256 算法进行分解。例如:

```
{
    “alg”: “HS256”,
    “typ”: “JWT”
} 
```

Enter fullscreen mode Exit fullscreen mode

“alg”是一个字符串，指定用于对令牌进行签名的算法。

“typ”是令牌的字符串，默认为“JWT”。指定这是 JWT 令牌。

**有效载荷(索赔)**

声明或有效负载可以定义为关于实体的声明，它包含安全信息以及关于令牌本身的附加元数据。

以下是索赔属性:

*   iss:令牌的颁发者

*   sub:令牌的主题

*   奥德:代币的受众

*   qsh:查询字符串散列

*   exp:Unix 时间中定义的令牌过期时间

*   nbf:“不早于”时间，用于标识在此之前不得接受 JWT 进行处理的时间

*   iat:颁发令牌的“颁发时间”，以 Unix 时间表示

*   jti: JWT ID 声明为 JWT 提供了一个唯一的标识符

**签名**

签名

遵循 JSON Web Signatre 规范来生成最终的签名令牌。JWT 报头、编码声明被组合，并应用诸如 HMAC·SHA-256 的加密算法。签名的秘密密钥由服务器持有，因此它将能够验证现有的令牌。

## JWT 热门图书馆

*   Java [atlassian-jwt](https://bitbucket.org/atlassian/atlassian-jwt/) 和 [jsontoken](https://code.google.com/archive/p/jsontoken/)

*   Python [请求](https://github.com/jpadilla/pyjwt)

*   Node.js [节点-jwt-简单](https://github.com/hokaccha/node-jwt-simple)

*   Ruby [ruby-jwt](https://github.com/jwt/ruby-jwt)

*   PHP [firebase php-jwt](https://github.com/firebase/php-jwt) 和[透明 jwt](https://github.com/luciferous/jwt)

*   。净 [jwt](https://github.com/jwt-dotnet/jwt)

*   哈斯克尔[哈斯克尔-jwt](http://hackage.haskell.org/package/jwt)

基于令牌的方法的优势

*   JWT 方法允许我们对任何服务器或域进行 AJAX 调用。因为 HTTP 报头用于传输用户信息。

*   他们不需要在服务器上有单独的会话存储。JWT 本身传达了全部信息。

*   服务器端简化为一个 API，静态资产(HTML、CSS、JS)可以通过 CDN 提供服务。

*   认证系统是移动就绪的，令牌可以在任何设备上生成。

*   由于我们已经消除了对 cookies 的需求，我们不再需要针对跨站点请求提供保护。

*   API 键提供了非此即彼的解决方案，而 JWT 提供了更细粒度的控制，可以出于任何调试目的对其进行检查。

*   API 密钥依赖于中央存储和服务。JWT 可以自行颁发，也可以由外部服务在允许的范围和到期时间内颁发。

## 在 Python 中创建 JWT

对有效载荷进行编码

```
>>> import jwt
>>> encoded = jwt.encode({'some': 'payload'}, 'secret', algorithm='HS256')
    'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzb21lIjoicGF5bG9hZCJ9.4twFt5NiznN84AWoo1d7KO1T_yoc0Z6XOpOVswacPZg' 
```

Enter fullscreen mode Exit fullscreen mode

在服务器上解码有效载荷

```
>>> jwt.decode(encoded, 'secret', algorithms=['HS256'])
{'some': 'payload'} 
```

Enter fullscreen mode Exit fullscreen mode

希望这篇文章有所帮助。欢迎在评论中发表你的想法。

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/json-web-token-tutorial-with-example-in-python.html)上。
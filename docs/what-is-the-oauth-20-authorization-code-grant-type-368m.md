# 什么是 OAuth 2.0 授权码授予类型？

> 原文：<https://dev.to/oktadev/what-is-the-oauth-20-authorization-code-grant-type-368m>

授权码授权类型可能是您会遇到的最常见的 OAuth 2.0 授权类型。用户授权应用程序后，web 应用程序和本机应用程序都会使用它来获取访问令牌。

本文是我们探索常用 OAuth 2.0 授权类型系列文章的第一部分。如果您想在我们开始之前回顾一下并了解更多关于 OAuth 2.0 的知识，请查看[OAuth 到底是什么？](https://dev.to/blog/2017/06/21/what-the-heck-is-oauth)，也在 Okta 开发者博客上。

## 什么是 OAuth 2.0 授权类型？

在 OAuth 2.0 中，术语“授权类型”指的是应用程序获取访问令牌的方式。OAuth 2.0 定义了几种授权类型，包括授权代码流。OAuth 2.0 扩展还可以定义新的授权类型。

每种授权类型都针对特定的使用情形进行了优化，无论是 web 应用、本地应用、无法启动 web 浏览器的设备，还是服务器到服务器的应用。

## 授权码流程

授权码授权类型由 web 和移动应用程序使用。它与大多数其他授权类型不同，首先需要应用程序启动浏览器来开始流程。概括地说，该流程具有以下步骤:

*   应用程序打开浏览器，将用户发送到 OAuth 服务器
*   用户会看到授权提示并批准应用程序的请求
*   用户通过查询字符串中的授权码被重定向回应用程序
*   应用程序用授权码交换访问令牌

### 获得用户的权限

OAuth 就是让用户能够授予对应用程序的有限访问权。应用程序首先需要决定它正在请求哪些权限，然后将用户发送到浏览器以获得他们的权限。为了开始授权流程，应用程序构建了一个如下所示的 URL，并打开一个浏览器访问该 URL。

```
https://authorization-server.com/auth
 ?response_type=code
 &client_id=29352915982374239857
 &redirect_uri=https%3A%2F%2Fexample-app.com%2Fcallback
 &scope=create+delete
 &state=xcoiv98y2kd22vusuye3kch 
```

Enter fullscreen mode Exit fullscreen mode

下面是对每个查询参数的解释:

*   `response_type=code` -这告诉授权服务器应用程序正在启动授权代码流。
*   `client_id` -应用程序的公共标识符，在开发者第一次注册应用程序时获得。
*   `redirect_uri` -告知授权服务器在批准请求后将用户发送回何处。
*   `scope` -一个或多个空格分隔的字符串，指示应用程序正在请求哪些权限。您使用的特定 OAuth API 将定义它支持的范围。
*   `state` -应用程序生成一个随机字符串，并将其包含在请求中。然后，它应该检查在用户授权应用程序后是否返回相同的值。这是用来防止 [CSRF 袭击](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_%28CSRF%29)的。

当用户访问这个 URL 时，授权服务器将提示他们是否愿意授权这个应用程序的请求。

[![OAuth Prompt](img/0d57eab9032a6f6aa1a3c72d66f99d3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1Rgoy89J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/oauth-authorization-code-grant-type/oauth-prompt-6489c6516fda5e0deea637d96f8398683fea6ade6054ee779e306e0b8ac5dcb8.png)

### 重定向回应用程序

如果用户批准了请求，授权服务器会将浏览器重定向回应用程序指定的`redirect_uri`，向查询字符串添加一个`code`和`state`。

例如，用户将被重定向回一个 URL，如

```
https://example-app.com/redirect
 ?code=g0ZGZmNjVmOWIjNTk2NTk4ZTYyZGI3
 &state=xcoiv98y2kd22vusuye3kch 
```

Enter fullscreen mode Exit fullscreen mode

`state`值将与应用程序最初在请求中设置的值相同。应用程序应该检查重定向中的状态是否与其最初设置的状态相匹配。这可以抵御 CSRF 和其他相关攻击。

`code`是授权服务器生成的授权码。这段代码相对较短，通常持续 1 到 10 分钟，这取决于 OAuth 服务。

### 用授权码换接入令牌

我们已经准备好收尾了。既然应用程序已经有了授权码，它就可以用它来获得一个访问令牌。

应用程序使用以下参数向服务的令牌端点发出 POST 请求:

*   `grant_type=authorization_code` -这告诉令牌端点应用程序正在使用授权码授权类型。
*   `code` -应用程序包括在重定向中给出的授权码。
*   `redirect_uri` -与请求代码时使用的重定向 URI 相同。有些 API 不需要这个参数，所以您需要仔细检查您正在访问的特定 API 的文档。
*   `client_id` -应用程序的客户端 ID。
*   `client_secret` -应用程序的客户端秘密。这确保了获取访问令牌的请求仅来自应用程序，而不是来自可能截获了授权代码的潜在攻击者。

令牌端点将验证请求中的所有参数，确保代码没有过期，并且客户端 ID 和密码匹配。如果一切正常，它将生成一个访问令牌并在响应中返回它！

```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
  "access_token":"MTQ0NjJkZmQ5OTM2NDE1ZTZjNGZmZjI3",
  "token_type":"bearer",
  "expires_in":3600,
  "refresh_token":"IwOGYzYTlmM2YxOTQ5MGE3YmNmMDFkNTVk",
  "scope":"create delete"
} 
```

Enter fullscreen mode Exit fullscreen mode

授权代码流完成！应用程序现在有了一个访问令牌，可以在发出 API 请求时使用。

## 何时使用授权码流程

授权代码流最好用在 web 和移动应用程序中。因为授权码授权具有用授权码交换访问令牌的额外步骤，所以它提供了隐式授权类型中不存在的附加安全层。

如果你正在使用移动应用程序中的授权代码流，或者任何其他类型的不能存储客户端秘密的应用程序，那么你也应该使用 [PKCE 扩展](https://www.oauth.com/oauth2-servers/pkce/)，它提供了对授权代码可能被拦截的其他攻击的保护。

代码交换步骤确保攻击者无法拦截访问令牌，因为访问令牌总是通过应用程序和 OAuth 服务器之间的安全反向通道发送的。

## 了解更多关于 OAuth 和 Okta

您可以在[OAuth.com](https://www.oauth.com/)上了解更多关于 OAuth 2.0 的信息，或者查阅这些资源来开始构建！

*   [开始使用 Spring Boot、OAuth 2.0 和 Okta](https://dev.to/blog/2017/03/21/spring-boot-oauth)
*   [ASP.NET 核心 2.0 中的令牌认证-完整指南](https://dev.to/blog/2018/03/23/token-authentication-aspnetcore-complete-guide)
*   [通过 Spring Boot 和 OAuth](https://dev.to/blog/2017/10/27/secure-spa-spring-boot-oauth) 确保您的 SPA 安全，或点击 [Okta 的 OIDC/OAuth 2.0 API](https://dev.to/docs/api/resources/oidc) 获取我们如何支持 OAuth 的具体信息。一如既往，在 Twitter [@oktadev](https://twitter.com/oktadev) 上关注我们，获取更多精彩内容。
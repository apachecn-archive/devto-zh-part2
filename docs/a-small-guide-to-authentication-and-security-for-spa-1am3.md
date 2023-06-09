# SPA 的身份验证和安全性小指南

> 原文：<https://dev.to/stereobooster/a-small-guide-to-authentication-and-security-for-spa-1am3>

这绝不是一个详尽的指南，只是为你入门。

**设置**:假设我们想要构建部署到`m.example.com`的新 SPA，我们还有一个部署到`www.example.com`的旧应用程序，例如 Ruby on Rails。新的应用程序将是一个静态网站，例如，我们将只有资产(JS，HTML，CSS，图像)部署在那里(它可能是一个后端和 SSR 的应用程序，但为了简单起见，让我们省略这一点)。此外，我们将使用`api.example.com`作为 SPA 应用程序的 API 端点。

## 共享会话

我们希望在新旧应用程序之间共享会话。要做到这一点，我们需要在根域使用 cookie——cookie 的 HTTP 头可以是这样的:

```
set-cookie: SID=...; Domain=.example.com 
```

请注意域开头的点。这样浏览器将发送 cookies 到我们所有的子域，如`m.example.com`、`www.example.com`、`api.example.com`。一旦用户在我们的服务中认证，他们将在任何地方都被认证。

## 安全饼干

所有这些考虑都是针对`api.example.com`和`www.example.com`的。

### `HttpOnly`

[`HttpOnly`指令](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Secure_and_HttpOnly_cookies)禁止 JavaScript 访问 cookies，以防止通过 XSS 劫持会话。

```
set-cookie: SID=...; HttpOnly 
```

### `Secure`

[`Secure`指令](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Secure_and_HttpOnly_cookies)指示浏览器仅通过 http 发送 cookies，以防止通过中间人攻击劫持会话。(如果攻击者能够伪造证书，攻击仍有可能)

```
set-cookie: SID=...;  Secure 
```

### `SameSite`

[号`SameSite`指令](https://www.netsparker.com/blog/web-security/same-site-cookie-attribute-prevent-cross-site-request-forgery/)阻止 CSRF 攻击。我选择使用这个指令的一个更宽松的版本(`Lax`)，它在大多数情况下应该足够了(阅读一下指令，看看你自己是否够用)。

```
set-cookie: SID=...; SameSite=Lax 
```

## 为资产担保

所有这些 HTTP 头都是针对`m.example.com`和`www.example.com`的。

### [`Strict-Transport-Security`T4】](https://helmetjs.github.io/docs/hsts/)

```
Strict-Transport-Security: max-age=86400 
```

### [`X-Content-Type-Options`T4】](https://helmetjs.github.io/docs/dont-sniff-mimetype/)

```
X-Content-Type-Options: nosniff 
```

### [`X-Frame-Options`T4】](https://helmetjs.github.io/docs/frameguard/)

```
X-Frame-Options: DENY 
```

### [`X-XSS-Protection`T4】](https://helmetjs.github.io/docs/xss-filter/)

```
X-XSS-Protection: 1; mode=block 
```

### [`Content-Security-Policy`T4】](https://helmetjs.github.io/docs/csp/)

本帖我不用`Content-Security-Policy`，但是强烈推荐你用。(也许我会写一个单独的帖子)

## API 的安全性

### CORS

使用 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 。指定允许哪些方法，以及缓存多长时间[预检请求](https://developer.mozilla.org/en-US/docs/Glossary/Preflight_request)

```
access-control-allow-methods: GET,HEAD,PUT,PATCH,POST,DELETE
access-control-max-age: 86400 
```

指定允许从哪个域访问 API

```
access-control-allow-origin: https://m.example.com 
```

指定`allow-credentials`否则，cookies 将不起作用。考虑到你不能在凭证指令中使用星号(`*`)。

```
access-control-allow-credentials: true 
```

### JSON API

对于所有请求，除了可能无需认证即可访问的端点，都需要`Content-Type`，这将触发 CORS 检查(通过飞行前请求):

```
Content-Type: application/json; charset=utf-8 
```

## JS 客户端

现在我们有了所有的基础，是时候从我们的前端实际调用 API 了。让我们为此使用 [`fetch` API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 。

### 匿名请求

对于允许匿名用户访问的端点，使用“普通”提取。不要用`Content-Type`，否则会变得更慢，对用户没有任何好处。

```
fetch(url) 
```

### 认证请求

对于其他请求，使用`credentials: "include"`来启用 cookies(这是最新 Fetch 规范中的默认选项，但并非所有浏览器都实现了它)。使用`headers: { "Content-Type": "application/json; charset=utf-8"}`来触发 CORS 检查，并实际通过后端的检查(我们在前面已经“实现”了)。

对于`GET`个请求:

```
fetch(url, {
  credentials: "include",
  headers: { "Content-Type": "application/json; charset=utf-8"}
}) 
```

对于`POST`个请求:

```
fetch(url, {
  credentials: "include",
  headers: { "Content-Type": "application/json; charset=utf-8"},
  method: "POST",
  body: JSON.stringify(params)
}) 
```

> 刘天舒在 Unsplash 上的照片
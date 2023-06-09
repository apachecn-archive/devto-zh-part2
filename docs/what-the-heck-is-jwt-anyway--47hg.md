# JWT 到底是什么？

> 原文：<https://dev.to/siwalikm/what-the-heck-is-jwt-anyway--47hg>

在本文中，我们将了解 JWT 令牌的基本原理，以及如何利用它来认证双方之间的通信，所有这些都使用普通的 NodeJS 和 javascript。

> JWT 是 JSON Web Token 的缩写，它是一种紧凑的 URL 安全方式，用于表示要在双方之间传输的声明。JWT 中的声明被编码为一个 JSON 对象，该对象使用 JSON Web Signature (JWS)进行数字签名。~ IETF

呃！什么？

简而言之，JWT 令牌是我们在发出网络请求以安全传递数据并确保数据未被篡改时在标头或 url 中传递的字符串。

例子:[www.example.com/private/?token=xxxxx.yyyyy.zzzzz](http://www.example.com/private/?token=xxxxx.yyyyy.zzzzz)

您可能想知道令牌格式是怎么回事！JWT 令牌由三部分组成，用点

`( . )`

隔开，它们是:

```
header.payload.signature 
```

Enter fullscreen mode Exit fullscreen mode

让我们来详细看看 JWT 辅币的不同部分。

**1。报头**
报头通常由两部分组成:令牌的类型，即 JWT，以及使用的哈希算法，如 HMAC SHA256 或 RSA。

```
{
   "alg": "HS256",
   "typ": "JWT"
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，这个 JSON 被 Base64Url 编码，形成 JWT 的第一部分。

```
'use strict';

var header = { "alg": "HS256", "typ": "JWT" };
var enc_header = Buffer.from(JSON.stringify(header)).toString('base64');
// ► "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9" 
```

Enter fullscreen mode Exit fullscreen mode

**2。有效载荷**

令牌的第二部分是有效负载，它包含了**声明**。声明是预定义的键及其值。有三种类型的债权:注册债权、公共债权和私人债权。

*   **注册声明:**这些是一组预定义的密钥，不是强制性的，而是推荐性的。其中有 **iss** (发行人) **exp** (到期时间)等。

*   **公共声明:**这些可以由使用 jwt 的人随意定义。但是为了避免冲突，应该在 IANA JSON Web 令牌注册中心中定义它们，或者将其定义为包含抗冲突名称空间的 URI。

*   **私有声明:**这些是为在同意使用它们的各方之间共享信息而创建的自定义键值对，它们既不是注册声明，也不是公共声明。

```
{
   "exp": "2019-02-14",
   "message": "roses are red"
} 
```

Enter fullscreen mode Exit fullscreen mode

然后对有效负载进行 Base64Url 编码，形成 JSON Web 令牌的第二部分。

```
'use strict';

var payload = { "exp": "2019-02-14", "message": "roses are red" };
var enc_payload = Buffer.from(JSON.stringify(payload)).toString('base64');
// ► eyJleHAiOiIyMDE5LTAyLTE0IiwibmFtZSI6IkpvaG4gRG9lIn0 
```

Enter fullscreen mode Exit fullscreen mode

**3。签名**

要创建签名部分，您必须获取编码的报头、编码的有效载荷、秘密、报头中指定的算法，并对其进行签名。NodeJS 自带非常强大的[加密库](https://nodejs.org/api/crypto.html)，这对我们来说太好了，我们将在我们的例子中使用它。

```
'use strict';
const crypto = require('crypto');
var jwt_secret = "secret";
// enc_header and enc_payload are computed earlier
var signature = crypto.createHmac('sha256', jwt_secret).update(enc_header +"."+ enc_payload).digest('base64');
// ► 6C46KAaZGp6RjbSqGllfdQF7g8vXCp02NTSrz-PzeoI 
```

Enter fullscreen mode Exit fullscreen mode

该签名用于验证 JWT 的发送者就是它所说的那个人，并确保消息在发送过程中没有被更改。

最终的 JWT 令牌看起来像这样

```
var token = `${enc_header}.${enc_payload}.${signature}`;
// ► eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOiIyMDE5LTAyLTE0IiwibWVzc2FnZSI6InJvc2VzIGFyZSByZWQifQ.0u-mkgLo5479CPjJJ4mXCwn2RW4dFT12fiYiopRWsZw 
```

Enter fullscreen mode Exit fullscreen mode

这里需要记住的重要一点是，JWT 令牌用于认证而不是加密，因此即使不知道密钥，有人也可以读取您的报头和有效载荷数据。

但是在收到令牌后，您可以用您的密钥再次签署报头和有效载荷，并将其与收到的签名进行比较，以检测令牌或消息的篡改。

一个很好的开始是去 jwt.io 上的这个[在线 JWT 调试器，并使用我们刚刚在上面生成的令牌。](https://jwt.io/#debugger)

嗨！我是 [@Siwalik](https://twitter.com/intent/follow?user_id=4708084272) ！如果你喜欢这篇文章，请在 twitter 上关注我，了解我最新的科技探险和附带项目！🙌
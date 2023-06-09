# JWT 的逻辑(JSON Web 令牌)

> 原文：<https://dev.to/_mertsimsek/logic-of-the-jwtjson-web-tokens-502d>

在这篇文章中，我们将学习什么是 JWT 和 JWT 的逻辑。IETF 被设计成一种标准的令牌格式。如验证、用户识别、数据完整性和信息安全等多个点相互关联。

JWT 由 3 个独立的以 Base64 格式编码的 JSON 片段组成。这些部分由一个点(.)象征和代表整个 JWT。如我所说，有 3 个字段。分别是**荷西报头、有效载荷和签名**。

### 穆标题

JWT 报头信息以 JSON 格式编写，并且可以找到标准字段。你可以在下面看到一个例子。

```
{
    "alg": "HS256",
    "typ": "JWT"
} 
```

Enter fullscreen mode Exit fullscreen mode

**Alg** 表示，“指定用于保护数据完整性的加密算法。”而 **typ** 的意思是，“定义了一个 JWT 对象”。

当 JOSE 头包含在令牌中时，它以 Base64 格式编码。比如上面 JSON 标题的 Base64 对应就是`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`。JWT 的 URL 友好性是由于它使用了 Base64 符号。

### 有效载荷

JWT 令牌在令牌生产者和消费者之间应该是唯一的。这种唯一性定义了有效载荷或其他声明信息。有效负载可以包括诸如用户 ID、超时和用户权限之类的字段。

```
{
  "id": "12345",
  "name": "Mert Simsek",
  "scopes": [
      "email",
      "page"
   ]
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，它需要是一个类似于 **id** 的字段来表示归属。这是有标准的，但不是强制性的。

### 签名

由三部分组成的 JWT 的最后一部分是 JWT 签名。签名部分保证了令牌制造商和消费者之间的数据完整性。创建签名时，使用 JOSE 标题中定义的算法。

只要，我们试着用 PHP 创建一个 JWT，它会如下。

```
$header = base64_encode("HEADER");

$payload = base64_encode("PAYLOAD");

$mixed = $header + "." + $payload;

$secretKey = "My_Secret_Key";

$signature = base64_encode(hash_hmac('sha256', $mixed, $secretKey);

$jwtToken = $mixed + "." + $signature; 
```

Enter fullscreen mode Exit fullscreen mode

作为上述操作的结果，我们的 JWT 令牌输出将是。您可以在[https://jwt.io/](https://jwt.io/)查看生成的 JWT 信息。

就这些了，在这篇文章中，就讲到这里。我们已经学习了 JWT 的逻辑学。
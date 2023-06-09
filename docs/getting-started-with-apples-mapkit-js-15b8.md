# 开始使用 Apple 的 MapKit JS

> 原文：<https://dev.to/rgo/getting-started-with-apples-mapkit-js-15b8>

> 最初发布于 [blog.rubeng.nl](https://blog.rubeng.nl/getting-started-with-apples-mapkit-js/)
> 
> 本文主要讲解如何轻松入门 MapKit JS。如果你已经设置好了，我建议你查看一下苹果公司关于 MapKitJS 的优秀文档。

自从我第一次在网上看到苹果地图，我就爱上了它。你可以在你自己的网站上运行它(这需要代理🧐).的苹果终端)

这就是为什么当苹果上周在测试版中宣布他们的官方 MapKit JS 库时，我相当高兴！

我想，新的 MapKit JS 库将使苹果地图添加到你的网站变得非常容易。在他们的产品演示和开发者网站上，苹果展示了下面的方法来给你的嵌入代码添加一个认证令牌(JWT 令牌)。

```
mapkit.init({
    authorizationCallback: function(done) {
        var xhr = new XMLHttpRequest();
        xhr.open("GET", "/services/jwt");
        xhr.addEventListener("load", function() {
            done(this.responseText);
        });
        xhr.send();
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

明确地说，您需要自己托管这个`/services/jwt/`端点。端点应该返回一个签名的 [JWT 令牌](https://jwt.io/)，然后可以用来验证苹果端点。

使用这种配置意味着在初始化 map 之前，每个 Map 实例化都需要调用一次您自己的服务器。除了额外的负载，您还需要创建一个(API)端点，配置 JWT 库，等等。特别是对于静态网站，这是一个相当大的问题。

### 有什么替代方案？

幸运的是，苹果可能意识到这并不是在所有情况下都行得通。因此，隐藏在文件中他们说:

> [..].它还可以在开发机器上运行，以生成一个可以直接签入源代码的长期令牌。[...】
> <small>[苹果开发者文档](https://developer.apple.com/documentation/mapkitjs/mapkit/2974045-init)</small>

这个 JWT 令牌可以被提交到源代码控制中。JWT 没有端点-需要签名，万岁！当然，这些令牌有一个截止日期，但是你可以自由设置这个截止日期为 10 年后。

这正是我们(至少是我)想要的。在代码中嵌入一个令牌，不需要额外的调用，没有额外的麻烦。

此外，将令牌提交到源代码中并不意味着您失去了所有控制权。使用 Apple Developer portal，您可以撤销用于生成 JWT 的私钥。

我们刚刚从上面繁琐的认证流程转到下面:

```
mapkit.init({
    authorizationCallback: function(done) {
        done('Insert JWT-token here');
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是，首先我们需要生成一个 JWT 令牌。

> 如果你不熟悉 JWT 的，你可能想读一下 Auth0 的这篇精彩文章。

### 生成 JWT

在你开始之前，我们需要收集一些要求。

*   你需要[注册一个地图 ID](https://help.apple.com/developer-account/#/dev4bb1cc12b) 。这是地图实例的唯一标识符。Apple 指定您将希望每个环境使用一个地图 ID(例如开发和生产)。
*   为了给 JWT 签名，你还需要[一个启用了 MapKit JS 服务的私钥](https://help.apple.com/developer-account/#/devcdfbb56a3)。

创建新的私钥后，你会得到一个 [PKCS #8](https://en.wikipedia.org/wiki/PKCS_8) 私钥。私钥不需要密码。

> 请务必将密钥保存在安全的地方，苹果只提供一次下载。🔑

现在我们有了所有的要求，我们需要根据苹果网站上的[规范生成一个 JWT 令牌。](https://developer.apple.com/documentation/mapkitjs/mapkit/2974045-init)

苹果使用使用[黄道曲线加密法](https://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography/)的`ES256`算法，所以你会希望将 JWT `algorithm`头设置为`ES256`。

您还需要使用参数`kid`将您的密钥 ID 添加到 JWT 报头中。您可以在开发人员门户中找到这个 ID，您也可以在那里创建密钥。

而且，正如 JWT 指出的，您需要在标题中添加 JWT 类型。

把所有这些放在一起，你的 JWT 头球现在看起来如下:

```
{
  "alg": "ES256",
  "typ": "JWT",
  "kid": "Your Key ID"
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来。JWT 有效载荷。这里没什么特别的。

Apple 规定,`iss` (issuer)参数应该设置为团队 ID，您已经用它创建了地图 ID &键。

苹果建议——但不要求——也使用`origin`参数。您可以指定允许使用 MapKit JS 代码的来源。这可以防止未经授权使用您的代币，这将计入您的每日限额。

此外，根据 JWT 的规范，您需要设置`exp`(到期)和`iat`(发布时间)参数。这些值是从[时间](https://en.wikipedia.org/wiki/Unix_time)开始以秒为单位指定的。

我建议你使用较长的过期时间，一旦令牌过期，你的地图将不再加载。您可以使用最适合您需求的值。

我们的有效载荷现在已经完成，看起来像这样:

```
{
  "iss": "Your Team ID",
  "iat": 1528473755323,
  "exp": 1528476433723,
  "origin": "Your origin header" // Recommended, but not required.
} 
```

Enter fullscreen mode Exit fullscreen mode

可选地，您可以提供一个`origin`参数来限制代码可以在哪些域上使用。这应该与您的浏览器传递的`Origin`头相匹配。

最后一步是签署 JWT 令牌的前两部分。因为我不是一个密码专家，所以我不会详细讨论这个问题。有[个库](https://jwt.io)可以为你签名。

签名由以下部分组成:

```
ECDSASHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  privateKey
) 
```

Enter fullscreen mode Exit fullscreen mode

当我们把我们的 JWT 放在一起时，我们会得到如下的东西

```
eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IllvdXIgS2V5IElEIn0.eyJpc3MiOiJZb3VyIFRlYW0gSUQiLCJpYXQiOjE1MTYyMzkwMjIsImV4cCI6MTUyODQ3NjQzMzcyMywiV2hlbiB5b3UiOiJyZWFkIHRoaXMsIiwieW91IHJlYWxseSB0aG9yb3VnaGx5IjoicmVhZCB5b3VyIGFydGljbGVzLiJ9.yGNubQUo6tzYsArulZgeNZxv7-6anvCC57tfTV0zqK2HN5HbmwLGesIA2nfXktD1UcN3mkGhTaUqxkEnIA5xuQ 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以使用此令牌来鉴定 Apple 端点。

### 更简单的方法

如果您只是在寻找一个嵌入了 API-token 的简单地图，那么这篇文章可能会让您有些震惊。别担心，有更简单的方法。

我创建了一个工具来为你生成嵌入代码(和 JWT 令牌)。您仍然需要上面(和工具上)列出的需求，但是剩下的就很简单了。

你只需输入你的详细资料和嵌入代码出来，很容易！此外，您的详细信息永远不会离开您的浏览器，因此安全性不会受到影响。🔐

请登录 [mapkitjs.rubeng.nl](https://mapkitjs.rubeng.nl) 查看

* * *

问题、赞美、评论？通过 [@rubengmrs](https://twitter.com/rubengmrs) 或者 [ruben@rubeng.nl](//mailto:ruben@rubeng.nl) 告诉我。

<small>Apple 是 Apple，Inc .的注册商标。本文、工具和参考资料不归 Apple Inc.</small>

<small>所有，也不隶属于 Apple Inc .</small>
【】此外，如果您还没有看过我的其他帖子，请查看 [blog.rubeng.nl](https://blog.rubeng.nl/getting-started-with-apples-mapkit-js/) 。
# OAuth 2.0 和 OpenID 连接流

> 原文：<https://dev.to/grahamcox82/oauth-20-and-openid-connect-flows-283j>

这个帖子一部分是为了我自己的利益，一部分是为了让人们告诉我，我完全误解了一些事情。所以，如果是错的，请告诉我！

OAuth 2.0 提供了两种标准流程:

*   授权码授予- `response_type=code`
*   OAuth 2.0 隐式授权- `response_type=token`

OpenID Connect 随后使用以下内容对其进行了补充:

*   OIDC 隐式授予- `response_type=id_token token`或`response_type=id_token`
*   混合流- `response_type=code id_token`、`response_type=code token`或`response_type=code id_token token`

实际上，这些都以完全相同的方式工作:

*   如果`response_type`包含`code`，则生成并返回一个授权码，该授权码可用于交换访问令牌
    *   在这种情况下，如果`scope`包含`openid`，那么在来自令牌端点的响应中包含一个 ID 令牌和一个访问令牌。
*   如果`response_type`包含`token`,那么生成并返回一个访问令牌——尽管可能是一个更严格限制的、更快过期的令牌。
    *   在这种情况下，在重定向时以片段形式返回参数。如果不存在，则将参数作为 Querystring 返回。
*   如果`response_type`包含`id_token`，则生成并返回一个 ID 令牌。

这三个规则可以混合和匹配，但是希望给出规范中包含的组合。

然而，有趣的是，如果你遵循这三条规则，你会得到更多的灵活性。例如，规范允许所有 7 种组合，但只能以特定的顺序排列——恰好是字母顺序。忽略这个事实会让你也接受:

*   代码令牌 id_token
*   令牌码
*   id_token 代码令牌
*   等等

我看不出这有什么好处，但是通过简化规则而获得的灵活性似乎有些用处。

此外，强制输入的规则显然是:

*   范围-总是必需的
*   response_type -总是必需的
*   client_id -总是必需的
*   redirect_uri -总是必需的
*   状态-始终推荐
*   nonce -如果 response_type 包含`id_token`但不包含`code`时需要。否则可选。
    *   我不明白这一点，但是 OpenID Connect 规范只在 OIDC 隐式流中明确地将`nonce`标记为 REQUIRED，而在其他流中没有。

我错过了什么吗？或者，这种更简单的解释真的给出了规范所需的一切吗？
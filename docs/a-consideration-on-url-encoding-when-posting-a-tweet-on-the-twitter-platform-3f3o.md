# 在 Twitter 平台上发布推文时对 URL 编码的考虑

> 原文：<https://dev.to/webdva/a-consideration-on-url-encoding-when-posting-a-tweet-on-the-twitter-platform-3f3o>

这篇文章向您展示了在使用 Twitter API 时如何对 tweets 进行 URL 编码。

当 Twitter API 用户代理或客户请求在 Twitter 平台上发布推文时，Twitter API 文档规定推文应根据需要进行“ [URL 编码](https://developer.twitter.com/en/docs/tweets/post-and-engage/api-reference/post-statuses-update)”

如果您发送一个 API 请求来发布一条推文，而没有考虑 Twitter 平台将对您提议的推文执行的 URL 编码，那么提议的推文可能会变长，从而超过 280 个字符的限制。这将导致 Twitter API 服务器发送一个 API 错误作为响应，即指定建议的 tweet 超过了 280 个字符的限制。为了解决这个问题，我们需要一个算法来解决最大化我们可以请求 Twitter 平台 API 服务器接受的字符数的问题，这也是本文讨论的目标。

我们考虑以下算法:

1.  将原始 tweet 消息字符串编码为 URI 编码的字符串。
2.  提取新的 URI 编码字符串的前 280 个字符。
3.  使用编码字符串的缩写形式，将其从编码的 URI 字符串解码为非编码的 URI 字符串。

为了对字符串进行编码，我们使用了`encodeURIComponent`内置的 JavaScript 函数。然后我们使用`substr` string 方法提取编码字符串的前 280 个字符。然后，使用内置的 JavaScript 函数`decodeURIComponent`对编码后的字符串进行解码。

算法的 JavaScript 实现:

```
const URIEncodedString = encodeURIComponent(original_tweet_message_string);
const shortenedURIEncodedString = URIEncodedString.substr(0, 280);
const decodedShortenedURIEncodedString = decodeURIComponent(shortenedURIEncodedString); 
```

Enter fullscreen mode Exit fullscreen mode

`original_tweet_message_string`变量是请求发布的 tweet 状态字符串。

请注意，URI 编码可能会因为任何原因而失败，这需要您以某种方式处理错误。这可以被认为是这个算法的缺点，或者至少是这个算法的 JavaScript 实现的缺点。

随着算法的执行，我们现在有了一个可以放心地发送到 Twitter API 服务器的字符串。服务器将对该字符串执行编码，由于我们执行了预处理，因此最终提议的 tweet 状态字符串不会超过 280 个字符的限制。
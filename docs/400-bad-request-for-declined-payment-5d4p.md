# 400 拒绝付款的错误请求

> 原文：<https://dev.to/catriname/400-bad-request-for-declined-payment-5d4p>

[TLDR:成功的 JSON post，但是如果信用卡被网关拒绝，他们返回 400 错误请求。这会引发中的异常。NET 的 HTTP 响应]

几周前，我编写了一个. NET post 到 PayTrace API，帮助我演示和测试使用客户端加密的信用卡支付。这个过程大致是这样的:

*   作为 PayTrace 上的商家创建模拟账户
*   下载 PEM 密钥
*   在提交包含信用卡信息的表单时，导入的 PayTraceJS 库会加密信用卡号和 csc 代码
*   使用演示帐户的用户名和密码提交令牌请求
*   使用令牌提交事务(包括加密信息以及其他必填字段),并等待响应

成功的 HTTP 响应返回状态代码 200。我通过 stream 读取它，使用 JSON 将它反序列化到我的 CardResponse 对象中(成功和失败的响应具有相同的设计)。在我开始测试被拒绝的卡之前，一切都很顺利。

当响应为 200 时，我最初的演示代码看起来是这样的:

```
var httpResponse  = (HttpWebResponse)req.GetResponse();

using (var streamReader = new StreamReader(httpResponse.GetResponseStream()))
{
  string result = streamReader.ReadToEnd();
  creditCardResponse = JsonConvert.DeserializeObject<CardResponse>(result);
} 
```

Enter fullscreen mode Exit fullscreen mode

**问题在于，当卡被拒绝时(由于资金不足、地址错误或任何原因)，HTTP 响应状态代码从 200 变为 400“错误请求”。这导致在上面示例的第一行抛出一个立即异常**。更糟糕的是，400 代码有点误导，因为我最初的想法是我的 JSON 请求格式错误。可能，我漏掉了一个必填字段？号码

这是由 PayTrace 设计的。

如果你[检查他们的文档](https://developers.paytrace.com/support/home#14000044372)和他们拒绝响应的样本，它清楚地说明:“下面的响应将返回一个 HTTP 状态 *400 错误请求。*

我最初通过调试模式对此进行了测试，一直到获得令牌(我显然碰到了 PayTrace 墙，并传递 auth 以获得一个错误的请求，而不是未授权的请求)。然后，我从调试会话中取出令牌，再运行几行代码，得到我的 HttpRequest 的 JSON。我收集了请求所需的所有信息，并将其扔给了邮递员。Postman 返回了一个 JSON 字符串，以及我需要的所有细节:成功(失败)、响应消息等。我缩小了问题的范围，直接联系到我如何做出回应。NET HttpResponse。

那现在怎么办？我的 HttpResponse 在 400 上立即失败，这意味着我无法挖掘和读取数据流，更不用说了解这张特定信用卡被拒绝的原因了。

我的第一次尝试是尝试简单的 try/catches，但是直到我[捕捉到那个特殊的 web 异常](https://msdn.microsoft.com/en-us/library/system.net.webexception.response(v=vs.110).aspx)时，我的尝试才刚刚好。我改编的代码:

```
try
{
  HttpWebResponse response = (HttpWebResponse)req.GetResponse();

  using (var streamReader = new StreamReader(response.GetResponseStream()))
  {
    string result = streamReader.ReadToEnd();
    creditCardResponse = JsonConvert.DeserializeObject<CardResponse>(result);
  }
}
catch (WebException ex)
{
  using (WebResponse resp = ex.Response)
  {
    using (Stream data = resp.GetResponseStream())
    {
      StreamReader sr = new StreamReader(data);
      string result = sr.ReadToEnd();
      creditCardResponse = JsonConvert.DeserializeObject<CardResponse>(result);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我将不得不为其他类型的回复(不是 200 或 400)修改这个，但这是我需要克服这个问题的推动力。我能够将失败的响应反序列化到我的 CardResponse 对象中，并报告失败的原因。
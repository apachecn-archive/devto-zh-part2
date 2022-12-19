# 为 Alexa - 4 构建-渐进响应

> 原文：<https://dev.to/anandmukundan/building-for-alexa---4---progressive-response-leg>

这篇文章着眼于一个你可以添加到技能中的特殊功能，叫做渐进反应。

这些允许你向用户展示技能正在进行中，可能需要一些时间。例如，如果您正在查找一个订单的交货状态，但是您需要与多个物流提供商核对以获得最终状态。整个操作可能需要 30 秒，然后你需要将这些信息打包成有用的语音响应。因此，当你在第三方检查进行到一半时，让用户知道你正在处理请求可能是有意义的，这可能会花费你更多的时间来获取信息。

[![See the source image](img/1ef169f17b6f8e847ce64fd7454a91f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9vxHK75q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.stack.imgur.com/D2alZ.gif)

想象一下这是 windows 应用程序中沙漏的声音，或者是 web 应用程序中的动画圆圈(就像你在这里看到的)。

发送渐进式对话框的方式是用 VoicePlayer 调用 API 端点/指令。说出命令。

目前，您还没有现成的方法通过 AlexaSkillKit.NET 库来实现这一点，但是由于这是一个普通的 REST 端点，您可以像调用其他 REST 服务一样直接调用它。

下面是我写的一个函数，你可以用来发送渐进式对话框，作为你的 Alexa 技能的一部分(这使用 RestSharp 库进行 API 调用)。apiToken、requestId 和 apiURL 是您从 Amazon 获得的请求的一部分。您需要使用收到的令牌，否则 REST 调用将失败。

```
 internal static void SendProgressiveResponse(string apiToken, string requestId, 
string apiBaseUrl, string speechText)
        {
            if (speechText.Length > 600)
            {
                throw new ArgumentException("Progressive Text cannot be more than 600 characters", "speechText");
            }
            var restClient = new RestClient(apiBaseUrl);
            RestRequest restRequest = new RestRequest();
            restRequest.Resource = "v1/directives";
            restRequest.Method = RestSharp.Method.POST;
            restRequest.AddHeader("Authorization", "Bearer " + apiToken);
            restRequest.AddHeader("Content-Type", "application/json");
            var body = new
            {
                header = new
                {
                    requestId = requestId
                },
                directive = new
                {
                    type = "VoicePlayer.Speak",
                    speech = speechText
                }
            };
            restRequest.AddJsonBody(body);
            var response = restClient.Execute(restRequest);
            if (response.StatusCode == System.Net.HttpStatusCode.NoContent)
            {
                Log.Debug("Progressive was successful");
            }
            else
            {
                Log.Debug($"Progressive failed withe code - {response.StatusCode}, {response.StatusDescription}.");
            }
        } 
```

你可以在这里获得更多关于渐进式回应[的信息..](https://developer.amazon.com/docs/custom-skills/send-the-user-a-progressive-response.html)
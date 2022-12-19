# 获取 Azure DataFactory 的 BigQuery refresh_token

> 原文：<https://dev.to/jpda/getting-your-bigquery-refreshtoken-for-azure-datafactory-2emn>

在 WTF 总部这里，我现在有几个数据科学家朋友问我如何连接起来，所以让我们开始吧。

如果你正在用 Azure Data Factory 移动 BigQuery 数据，[从这里开始](https://docs.microsoft.com/en-us/azure/data-factory/connector-google-bigquery)，在我们的官方文档中准备好连接到 BigQuery。当您达到获得刷新令牌的点时，请回来，我们将为您排序。

### 首先，让我们前往谷歌的开发门户

如果你还没有在谷歌门户中定义的现有应用程序，点击[这里](https://console.developers.google.com/projectcreate)并创建一个新项目。不管怎样，创建一个新的可能是个好主意，即使你已经有了一个，因为我们希望所有这些独立于我们正在做的其他工作。

[![Call it whatever you want.](../Images/2e13e41e0a598625ea2a1d95ad50c828.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VPePjX-Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__AfjOcuB4y8DAr3Zl3Ca6Vw.png) 随你怎么称呼它。

### 我们去拿些凭证吧

单击“Create”后，我们将回到控制面板。确保在标题中“Google APIs”旁边的下拉列表中选择了您的新项目。然后，我们将转到侧栏中的凭据。

[![a](../Images/1333fecfad45d99e57004e28989c0296.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TXpRNvsw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520__laBaGvNpsQaVg5A__%25207ZGJng.png)

首先，前往 OAuth 同意屏幕。在创建凭据之前，我们需要在这里填写一些详细信息。

[![a](../Images/145f02716dc93c2c64866eb9fd8bfaf9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---haIBYhI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__lhafqlaUU0368cMVygW6iA.png)

你真正需要的是一个名字。这将显示给用户，以便他们知道他们同意的应用程序的名称-在我们的情况下，因为很可能你实际上没有分发任何会使用它的东西，我会确保它是你和你的组织可以识别的东西。

接下来，我们需要为我们的应用程序生成一些凭证，以向 Google 的 API 进行身份验证。

我们想要创建一个 OAuth 客户端 ID。

[![a](../Images/0d2eca02e5b84ed3bae850f9b9cbf62a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZhGG8N4X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__rMOkqfCjlrIfHiVRKC727w.png)

选择网络应用程序，给它一个名字，然后在重定向 URI 下降。您可以在这里使用 localhost，但是使用您控制的域可能会更好。实际上不需要在该地址*存在任何东西*,但它确实提供了较小的风险，因为如果您的客户端 ID/秘密被泄露，人们可以请求将令牌发送回 localhost，这可能不是*您的* localhost。

[![a](../Images/61314aa6435103a217e3e28ec84e4dd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hM7NXHvN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520____%2520djscIdWFd2DFMJnomlxEQ.png)

一旦你点击创建，你会得到你的客户 ID 和密码。把这些东西藏在你能很快找到的地方，我们在接下来的步骤中会用到它们。

[![Yes, I’ve already revoked this app](../Images/976f3b7c4f639dbb18c98cf41f7bfa24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xpi52kbO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520__pzkmdSlCoVNSX____%2520CFzG8Ew.png) 是的，我已经撤销了这个应用

### 生成令牌

接下来我们进入有趣的部分。你可以使用像 Postman 这样的工具(因为我们将有一个 POST 请求)或者你可以用 curl 或者你喜欢的工具来踢它。如果你喜欢工匠，自由放养，手工制作的 OAuth，那你就来对地方了。

首先，我们需要获得用户同意才能访问特定的帐户(在本例中是您的帐户，或者是您的 BigQuery 数据所在的任何位置)。这是一次性的操作，一旦我们同意，我们就不需要再这样做了。

我们需要:

*   生成请求同意的 URL
*   得到那个网址
*   同意
*   获取从 GET 返回的`authorization_code`,并将其换成一个`access_token`,我们可以用它来实际访问 BigQuery API。
*   另外，我们需要得到一个`refresh_token`，数据工厂将使用它来请求后续的`access_token`

### 生成您的同意 URL

收集到目前为止我们创造的所有碎片。我们需要您的客户 ID 和密码，以及您放在 OAuth 同意详细信息屏幕中的有效重定向 URI。

这是我们的模板

`GET https://accounts.google.com/o/oauth2/v2/auth?client_id= **<CLIENT_ID>** &redirect_uri= **<URL-ENCODED REDIRECT URI>** &response_type= **code** &access_type= **offline** &prompt= **consent** &scope= **<URL-ENCODED SCOPE>**`

我们将对此进行一点解构:

*   `client_id` —您的客户 ID
*   `redirect_uri` —您之前输入的重定向 URI。对 URI 进行编码是个好主意。如果你在必应上搜索 URL 编码器，你会在顶部找到一个
*   `response_type` — `code`告诉 Google 我们希望在响应中返回一个授权码，我们可以用它来请求一个`access_token`
*   如果我们需要一个`refresh_token`,`offline`是我们需要请求的(你会想要这个)
*   `scope` —这是我们想要连接的目标服务(在我们的例子中，是 BigQuery)。你可以在这里查看所有可用的范围[。你很可能想要`https://www.googleapis.com/auth/bigquery`](https://developers.google.com/identity/protocols/googlescopes)

当我们把所有的东西都放进去了，我们应该会得到这样一个 URL:

`GET https://accounts.google.com/o/oauth2/v2/auth?client_id=693826606074-5j8ituji2g2ajt0d35ldj1kks7pdpeq0.apps.googleusercontent.com&redirect_uri=http%3A%2F%2Flocalhost&response_type=code&access_type=offline&prompt=consent&scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fbigquery`

会要求您登录，这是您应该做的。然后我们会得到这个:

[![a](../Images/fac8e31d07fe94d221a74c43553832f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H3QiJfRV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__kKcIt6zkAK9hGN0fKcgisA.png)

这是我们实际的同意屏幕——你会注意到我们被要求允许我们的应用程序(bigquery-azuredf)在 Google BigQuery 中查看和管理你的数据。听起来像是我们想要的。单击“允许”并留意地址栏。

[![a](../Images/a112a5923338ca2fdff7de1e70239f1b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hxGM8xGD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520__tKQ__%2520K35__SrKj4Fm5mygoxw.png)

因为我使用了`http://localhost`作为我的`redirect_uri`，但是在 localhost 上没有托管任何东西，所以我最终使用了 404，这很好。注意唯一的 querystring 参数，`code` —它包含我们的授权代码，我们将使用它来获取访问和刷新令牌。复制该值(在本例中，从 4/AAC 开始…)并暂时将其保存在安全的地方。

### 用您的授权码换一些代币

现在我们需要将代码*发送回*给 Google，以换取一个访问令牌。

我们将构建另一个 URL 并发送一些数据。

`POST https://www.googleapis.com/oauth2/v4/token?code= **<CODE RETURNED IN PREVIOUS REQUEST>** &client_id= **<YOUR CLIENT ID>** &client_secret= **<YOUR CLIENT SECRET>** &redirect_uri= **<URL-ENCODED REDIRECT URI>** &grant_type= **authorization_code**`

让我们也来解构一下这个:

*   code —我们在上次响应中获得的代码，您应该已经复制了它
*   客户端 ID —与之前相同
*   客户端密码—您之前创建凭据时生成的密码。我们在这里第一次使用它——我们需要向 Google 认证我们的*应用程序*,请求使用授权码，所以我们使用 ID 和 secret 来完成这项工作。
*   重定向 URI —和以前一样
*   授权类型—根据规范，将其保留在字符串`authorization_code`中

现在我们不能在浏览器中进行 GET，所以打开你选择的 HTTP POST 工具——Postman，curl，等等。

[![In Postman](../Images/8f5874cbb2dce884b3b91b5b8893912c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_JRuYIKg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520__s__%2520Y2wFNoSS%2520____OloKHVHZ__%2520g.png) 在邮递员

如果一切顺利，你应该会得到一个访问令牌和一个刷新令牌！如果进展不顺利，请查看错误消息。每个授权码只能使用一次，因此，如果您使用了一个授权码并出现错误，您需要返回浏览器并重新同意获取另一个授权码。

我还注意到授权码中有斜线和散列，所以在将它们添加到您的 URL 之前对它们进行 URL 编码可能是个好主意。

### 获取我们的刷新令牌

一旦获得成功的结果，您应该在响应中获得一个 JSON 对象:

*   这是您(或者，在我们的例子中，是数据工厂)实际发送给 API 进行请求的内容
*   `token_type` —表示令牌的用法—在本例中，它是一个`Bearer`令牌，当数据工厂调用服务时，它会在`Authorization`头中使用这个令牌。
*   `expires_in` —访问令牌的有效性，以秒为单位。您会注意到这只有 6 分钟，因此下一个字段很重要…
*   这是我们(数据工厂)在需要的时候用来请求后续访问令牌的。如果您正在编写自己的应用程序，您应该将 refresh_token 保存到某个安全的地方，并在不提示用户的情况下使用它来重新访问数据。

如果您的响应中没有刷新令牌，请确保您的初始请求包含`access_type=offline`！

### 全部搞定！

至此，我们都完成了！将您的`refresh_token`添加到数据工厂中的 BigQuery 连接器，并测试您的连接。

### 奖励:从你的刷新令牌中手动获得一个访问令牌

无论出于何种原因，如果您想从刷新令牌中手动获取访问令牌，您可以使用以下示例请求:

`GET https://www.googleapis.com/oauth2/v4/token? **refresh_token** =<REFRESH TOKEN HERE>& **client_id** =<YOUR CLIENT ID>& **client_secret** =<YOUR CLIENT SECRET>& **redirect_uri** =<URL-ENCODED REDIRECT URI>& **grant_type** =refresh_token`

以及它的解构:

*   `refresh_token` —自上次请求后，您在安全的地方保存了**的刷新令牌**
*   `client_id`、`client_secret`和`redirect_uri`——我们一直在用的同一个
*   `grant_type` —始终是字符串`refresh_token`(不是您实际的刷新令牌)
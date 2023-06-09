# Connect v2 的 Square API 版本控制

> 原文：<https://dev.to/squaredev/square-api-versioning-for-connect-v2-4l91>

我们为 Connect v2 开发人员带来了激动人心的更新！我们已经为 Square 的 API 发布了所有 Connect v2 端点的[版本。这将使我们能够以前所未有的速度提供更多的功能和特性。不过不用担心，我们的 API 将保持向后兼容，并允许您在最方便的时候升级。这意味着您可以安全地继续使用您已经喜欢的 Square APIs，但允许我们在不破坏您的代码的情况下为您带来新的或改进的功能。](https://developer.squareup.com/docs/changelog/overview)

[![](img/493053553d10b8cf249afff7ca5d91f2.png)T2】](//images.ctfassets.net/1wryd5vd9xez/26WYajTAeTr98rly97fON0/6cb93860b98b12021b930c007dd7dcf0/https___cdn-images-1.medium.com_max_3840_1_BkvbW9_QdvWTI6s-Elvx9Q.png)

### 这对我意味着什么？

如果您不想升级您的集成或者使用最新最好的，那么这意味着您可以坚持使用您当前使用的 API 版本。如果你*真的*想要升级，你可以通过设置你的`Square-Version`头到你想要的版本来轻松测试。

### 我怎么知道我在哪个版本？

你可以随时查看你的[开发者仪表盘](https://connect.squareup.com/apps)。您还可以检查调用端点的响应，并查看您的`Square-Version`头设置为什么。创建应用程序时，默认版本(当前为应用程序设置的版本)被设置为 API 的最新版本。这意味着，如果您在发出连接 v2 端点的请求时没有明确指定要使用的 API 版本，将使用您的默认版本。因此，如果 API 的最新版本是`2018-07–12`，并且你在`2018-07–15`上创建了你的应用，那么你将被设置为版本`2018-07–12`。

### 你为什么要这样做？

这使我们能够向开发人员提供更多的功能，同时防止升级 API 对已经实现 API 的开发人员产生负面影响。

### 我的 SDK 需要升级吗？

如果您要升级您的 API 版本，您可以在准备好的时候升级 SDK 版本。SDK 的每个版本都“绑定”到 API 的一个版本，因此您永远不需要担心 SDK 会访问“错误”的版本，即使您更改了默认版本。随着 API 新版本的发布，我们的 SDK 也有了新版本。SDK 版本将遵循格式`2.20180712.0`来对应 API 的版本`2018-07–12`。只需查看 SDK 版本，您就可以知道 SDK 使用的是什么版本的 API。

这里最大的好处是，即使你不记得进入你的仪表板并升级你的应用的默认 API 版本，SDK 也会处理将`Square-Version`头显式设置为与 SDK 匹配的 API 版本。不要再考虑使用哪个版本，让 SDK 为您完成这项工作！

### 这是什么样子？

```
curl https://connect.squareup.com/v2/locations/{{location_id}}/transactions \
   -X POST \
   -H "Content-Type: application/json"  \
   -H "Authorization: Bearer ACCESS_TOKEN" \
   -H "Square-Version: 2018-07-12" \
   -d '{
    "idempotency_key": "74ae1696-b1e3-4328-af6d-f1e04d947a13",
    "amount_money": {
      "amount": 200,
      "currency": "USD"
    },
    "card_nonce": "card_nonce_from_square_123",
   }' 
```

我们非常期待向开发人员提供新的和改进的功能。拥有 API 版本控制是将它交给开发人员的关键一步。我们现在可以更快地为开发人员提供他们想要的功能，同时也不会破坏现有的实现。如果有你真正想要的新功能或者你想要改进的东西，来加入我们的 [Slack 社区](https://squ.re/slack)或者在标签为`square-connect`的 [StackOverflow](https://stackoverflow.com/) 上发帖，这样我们就能收到你的来信。

*想要更多？[注册](https://www.workwithsquare.com/developer-newsletter.html?channel=Online%20Social&sqmethod=Blog)获得我们每月的开发者简讯，或者来 Square dev [Slack 频道](https://squ.re/slack)打招呼！*
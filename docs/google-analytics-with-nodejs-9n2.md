# 使用 NodeJS 的谷歌分析

> 原文：<https://dev.to/dvwbr/google-analytics-with-nodejs-9n2>

# 为什么要 Google Analytics？

作为我们正在进行的鞋带专业知识自动化的一部分，我们已经决定与谷歌分析整合，我最近的重点是看到这一成果。在我们的早期整合阶段，谷歌分析将在帮助我们更好地了解特定商店的目标受众规模方面发挥重要作用，并使我们能够为他们提供更好的目标广告活动。

# 谷歌分析和 NodeJS

Google 发布了 NodeJS 客户端库的 alpha 版本，可以在 Github 上找到[。使用 Google 支持的客户端库的主要优势之一是它们提供了自动令牌刷新，因此您不必花费太多精力来确保您收到的访问令牌仍然有效。](https://github.com/google/google-api-nodejs-client)

另一个非常有用的资源是谷歌分析的查询浏览器。我发现在开发过程中，能够依靠这种方法来验证我从 API 收到的数据非常有帮助。

从[Google API](https://www.npmjs.com/package/googleapis)版本 28 开始，支持原生异步/等待，使得对 GA API 的调用更容易读取和处理。

# 获得授权

我们使用 OAuth2 使用户能够授予我们对其 GA 帐户的权限。一旦获得授权，我们就能够存储他们的访问令牌和刷新令牌，并使用这些令牌来验证他们帐户中的数据请求。

# 认证和请求

当我们准备通过 GA API 发出请求时，我们使用 OAuth2 客户机来设置凭证——在本例中，它是一个简单的节点对象，包含两个键`access_token`和`refresh_token`，以及相应的数据。下面是一个使用 OAuth2 客户端获取准备发出请求的分析 API 对象的示例。

```
const oauth2Client = new OAuth2(CLIENT_ID, CLIENT_SECRET, REDIRECT_URL);
const credentials = { refresh_token: 'REFRESH_TOKEN', access_token: 'ACCESS_TOKEN' };
oauth2Client.setCredentials(credentials);
const analyticsAPI = googleApi.analytics({ version: 'v3', auth: oauth2Client }); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以使用`analyticsAPI`来发出请求:

`await analyticsAPI.management.profiles.list({ accountId: '~all', webPropertyId: '~all' });`

以上将获取特定用户可用的所有配置文件。

# 鞋带和嘎

现在在鞋带公司，我们最感兴趣的是了解目标受众的规模。因此，当用户将 GA 集成到他们的鞋带帐户中时，我们也会跟踪一个适当的视图进行查询(我们强烈建议用户选择默认的、未过滤的视图，以获得最佳结果)。这样，我们可以动态生成一个细分市场，以隔离针对他们的 Shopify 域的流量，从那里我们可以了解一系列时间跨度内用户的不同指标。

我们很高兴能够继续发展这种集成，并利用更多由 GA 跟踪的数据。
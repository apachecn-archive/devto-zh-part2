# 探索用于喜欢和书签的 API

> 原文：<https://dev.to/kehers/exploring-apis-for-likes-and-bookmarks-4afb>

我[建立了一个服务](https://waytab.io/)，提醒人们他们喜欢或收藏的东西(推文、转发、文章、图片等)。以下是探索这些数据的一些 API 的注释。

### 推特(喜欢)

*   初恋。我在 Twitter 的 API 上构建了很多东西。我也被烫过很多次，不过话说回来，没有 Twitter 喜欢的书签提醒工具算什么？
*   API 仍然在 OAuth 1.0a 上。我所评论的所有其他服务都在 OAuth 2.0 上。
*   顺便说一下，Twitter 现在有了[书签](https://blog.twitter.com/official/en_us/topics/product/2018/an-easier-way-to-save-and-share-tweets.html)，但我怀疑人们真的用它来代替“喜欢”。一个想法是允许人们将他们的 Twitter 书签连接到 Waytab。不幸的是，在撰写本文时，Twitter 还没有一个 API。(我怀疑他们会这么做)。

### Unsplash(喜欢)

*   照片资源带有代表照片主色的`color`值。这在很多方面都很有用。一个例子是在加载照片时用这个作为背景色。
*   App 需要提交审核。如果你坚持他们的使用政策，过程是顺利的。

### 口袋(未读文章)

*   与其他 OAuth 2.0 APIs 不同，requests 需要您的`consumer_key`和`access_token`。
*   同样诡异的是，获取【未读】文章的 HTTP 方法是`POST`。

### Github(带星号的回购)

*   我对 Github 的唯一疑问是只允许一个授权回拨 URL。这意味着本地测试，我必须创建一个单独的应用程序，可以有一个本地主机回调 URL。

### Pinterest(用户 pin)

*   所有授权回拨 URL 必须在 SSL 上，包括本地主机。更简单的方法是使用一个像 ngrok 这样的应用程序通过隧道连接到您的本地主机。
*   App 需要提交审核。

## 未完成的服务:

### 插接板(书签)

*   我想包括至少一个书签服务。我想到的是 Pinboard(现在人们还用什么？).不幸的是，这个 API 不是 OAuth，所以我放弃了它。我无法处理用户密码。

### Youtube(稍后观看)

*   Youtube 弃用了他们的 watch later API。因为这是我最感兴趣的列表，所以我放弃了添加服务。我可能会添加“喜欢的视频”，但只有当我得到大量的请求。另一个可能有趣的列表是订阅频道的未观看视频。我还没有找到一种方法来拉虽然。

### Dribbble(喜欢)

*   Dribble 目前没有授权访问他们的 like API:/

### Spotify(喜欢)

*   我将不得不在 Spotify 上创建一个“商业应用”。(“如果……您的应用程序或产品收取购买费用，使用广告促销或利用免费增值模式进行追加销售；您作为法人实体或公司受到法律保护”)。由于没有明确的成本影响，我放弃了。

### Instagram(喜欢)

*   Instagram 的 API 此刻实在是太乱了。目前这是一个大禁忌。另外，脸书的文档中没有提到喜欢。
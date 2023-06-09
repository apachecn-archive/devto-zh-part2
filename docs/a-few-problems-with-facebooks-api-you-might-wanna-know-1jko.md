# 你可能想知道脸书空气污染指数的一些问题

> 原文：<https://dev.to/zerquix18/a-few-problems-with-facebooks-api-you-might-wanna-know-1jko>

这是我第一次发帖子！我有点害羞，但是@katafrakt 关于他使用脸书 API 的经历的帖子激励我根据我的经历写这篇帖子。

脸书的 API 很棒，它允许你以多种方式与平台互动，许多服务主要使用它从脸书获取数据。然而，你用得越多，就越会发现它有一些缺点。我想尽可能清楚地描述一些影响我的主要不利因素。

如果你正在考虑使用脸书的 API 或者已经在使用了，我希望这篇文章能帮助你。

## 突然变化

与大多数人不同，脸书不害怕变化。它鼓励改变。在某些情况下，他们会大规模禁止人们，告诉他们这是一个错误，一个月后[告诉他们，他们被禁止后，数百人投诉](https://developers.facebook.com/bugs/114169349430679/)！我知道这和即时文章有关，但还是和开发者平台有关。如果你打算用它们赚钱，请记住这一点。

在其他一些情况下，他们会突然降低他们的 API 比率限制。当**数百**因为他们的代码被破坏而抱怨时会发生什么？整整一个月后，[告诉他们这不是一个错误](https://developers.facebook.com/support/bugs/169774397034403/?disable_redirect=0)，它突然为每个人修复了！

## 非常艰难的 App 审核

如果你想从脸书请求一些额外的数据(除了用户的电子邮件和姓名)，你需要从脸书请求许可。这太棒了。叫做 [app 审核](https://developers.facebook.com/docs/apps/review/)。您将告诉他们您需要哪些权限，然后审核人员将验证您的应用程序是否正确使用了这些权限。听起来不错...，理论上。

问题是，这个过程预计会持续 2-3 天，但可能会持续几周到几个月，最近有很多开发人员在他们的团队中抱怨，因为他们的用户群仍然在等待。

也就是说，**如果你有幸拥有一家公司或者为一家**工作，因为 app 审核要求你是经过验证的合法商家。我说的验证，是指[通过脸书](https://developers.facebook.com/docs/apps/review/#business-verification)验证，这是一个额外的过程。你需要上传一份公司的法律文件并签署一份合同。

如果你是一个独立的开发者，正在使用脸书的 API 开发一些东西，你的产品可以发布到 Facebook 页面(或者需要来自脸书页面的内容)，广告管理，或者来自你的用户的数据，你需要在发布之前成为一个公司，或者手动添加你的用户为“测试者”，这样他们就可以访问这些功能。

这就产生了一个先有鸡还是先有蛋的问题。如果你想向全世界发布你的产品(然后成为一家公司)，你需要脸书的许可，但是要获得脸书的许可，你首先需要成为一家公司。

我不能说这是好是坏。由于我的项目，这影响了我，但这个决定是在剑桥分析丑闻之后做出的，所以它似乎是合理的。

## 编码时出现问题

脸书的 API 非常灵活。它使用 GraphQL，所以您只请求您需要的数据。但是，在某些特定的情况下，有几件事是它的文档中没有提到的。

例如，如果你想上传一个照片故事到脸书页面，你需要用参数`url` :
向`{page-id}/photos}`发送一个 POST 请求

```
curl -i -X POST \
 -d "url=https://www.facebook.cimg/fb_icon_325x325.png" \
 -d "caption=test photo upload" \
 -d "access_token=<user_photos_user_access_token>" \
 "https://graph.facebook.com/v2.11/me/photos" 
```

Enter fullscreen mode Exit fullscreen mode

这个`url`参数需要是互联网上的公众形象。这使得不可能从本地主机上传东西或者只是一个私有图像。但是在去脸书总部投诉之前，还有一个`source`参数，只在 [`{page-id}/feed`页面](https://developers.facebook.com/docs/graph-api/reference/v3.1/page/feed#publish)中提到:

[![a](img/87c784ac199c3fdb7ee36e1df0a8723c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P6lrIQcn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0lw7uzkny2omm1dn7f07.jpg)

这让我(可能还有其他一些人)如果想从本地主机测试他们的应用程序，就把东西上传到另一个网站。

此外，如果你想上传多张照片，你需要对每张照片提出请求，这将有助于你的应用程序更快地获得速率限制。

## 最终建议

*   准备好应对重大变化。一个很酷的技巧是，每当生产中出现异常时，就给自己发一封电子邮件。
*   您的速率限制不是针对每个用户的。对于每个用户，您每小时最多可以进行 200 次查询。因此，如果有两个用户，一个用户可以进行 400 次查询。这可能是好是坏取决于你的项目，但这也意味着**如果你不够小心的话，单个用户只需按 F5 足够多次就可以为其他人破解你的应用**，所以准备好你的缓存。
*   不要把你的用户数据卖给第三方。
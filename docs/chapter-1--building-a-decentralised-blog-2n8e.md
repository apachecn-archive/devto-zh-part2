# 第 1 章//建立一个分散的博客

> 原文：<https://dev.to/luke/chapter-1--building-a-decentralised-blog-2n8e>

此前:[https://dev.to/luke/building-a-decentralised-blog-3oec](https://dev.to/luke/building-a-decentralised-blog-3oec)

尝试以去中心化的方式重新发明个人博客之轮，这是有趣的两周。

到目前为止，我已经:

*   为杂志设计了一个架构，
*   为工作选择的库和框架，
*   并创作了杂志的前两页。

目前，我已经用随机数据填充了 journal，以测试这种美学，重点是确保视图能够响应不同的媒体大小。

twitter icymi 上有一些更新:

> ![unknown tweet media content](../Images/821c09778419c5a352d72c9e216faca5.png)![Luke Barnard profile image](../Images/cf2ccc4f261337838444da4c0cb82567.png)卢克巴纳德@卢克巴纳德 _![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)我已经开始设计默认的杂志主题了。fonts.google.com，没有你我该怎么办？
> 
> Markdown 渲染通过[github.com/rexxars/react-…](https://t.co/7sPaZXwtg2)，语法高亮来自[highlightjs.org](https://t.co/yLw4iOP066)。2018 年 7 月 18 日上午 00:46[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1019382964717871105)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1019382964717871105)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1019382964717871105)

> ![unknown tweet media content](../Images/ffec6b1028f2129428b4b2fba154d815.png)![Luke Barnard profile image](../Images/cf2ccc4f261337838444da4c0cb82567.png)卢克·巴纳德@卢克·巴纳德 _![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)杂志上的更多进展:
> -带有“订阅”按钮的作者卡片
> -带有可扩展线程的评论部分
> -响应式封面图片，使用 CSS“object-fit:cover”
> 
> [pravatar . cc](https://t.co/4lxj96MS6M)用于随机头像

> ![unknown tweet media content](../Images/20ba8fa6d6abeb083cbd2eaa5debd0b0.png)![Play butt](../Images/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/ext_tw_video/1023159958311976961/pu/pl/j7jtAGIngctVNxv8.m3u8?tag=4" type="application/x-mpegURL"></video>![Luke Barnard profile image](../Images/cf2ccc4f261337838444da4c0cb82567.png)卢克·巴纳德@卢克·巴纳德 _![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)期刊获得一个带有类别和浮动侧栏的主页！2018 年 7 月 28 日上午 10:56[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1023160271941054464)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1023160271941054464)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1023160271941054464)

目前为止的故事是这样的。

## 建筑

该架构的创建考虑到了关键特性。这些是:

*   物品的即时装载，
*   与搜索引擎和社交媒体网站预览的网络爬虫兼容，
*   能够链接到特定的文章。

所有这些都是通过在服务器端呈现内容的 web 服务器实现的。创建一个允许 SSR 同时保持数据去中心化的架构不是不可能的，但是有点棘手。

最终，文章和用户数据将存储在一个分散的存储中，由[矩阵](https://matrix.org)支持。特别是一个 SDK 最接近能够访问 matrix 协议的整个功能集，并且是我非常熟悉的一个——matrix-js-SDK。

考虑到这一点，我策划了一个计划，用这个 SDK 将 matrix 包装在一个特定于期刊的后端后面，这个后端将充当 matrix 的客户端。它将代表一个可以访问所有公共博客的特殊用户(在同一个 matrix 主服务器或其他服务器上)，但其机制仍不清楚，我仍在考虑中。

当用户以必须授权的方式与 journal 交互时，他们将在浏览器中运行 matrix-js-sdk 支持的 matrix 客户端。这将允许他们直接向矩阵服务器提出请求(不通过日志后端),作为他们想要的用户登录。这使他们能够发布博客、发送评论和订阅他人的博客。

> 当和我女朋友谈论她使用博客网站的方式时，她明确表示她希望能够订阅用户的博客。

像这样的特性需要通过矩阵的非对称“读/写”API 和通过日志后端的“读”API。作为一个架构，这可能很难维护，但至少将日志后端公开的公共数据与矩阵后端直接公开的私有数据分开。

尽管已被认证为 matrix 用户，但与直接在 matrix 上访问相比，访问服务器端呈现的博客列表和博客帖子仍然是有益的。

## Next.js

基于 React 的 SSR 的一个选项是 Zeit 的 [Next.js](https://nextjs.org/) 。它为开发 React 应用程序提供了一个框架，该应用程序可以在服务器端呈现，但也支持动态客户端更新。下图显示了它是如何适应该架构的。

```
 (3) 
    journal backend-----[matrix]
      |  |                 |
  (2) |  |         ________| (4)
      |  |        |  
      |  |        |  next.js
  ____|__:________|_________
 |                          |
 | journal web UI frontend  |
 |__________________________|
       | :
   (1) | |(2)
       | |
   web browser 
```

Enter fullscreen mode Exit fullscreen mode

图一。

图 1 展示了 next.js 如何融入 journal 的架构，封装了浏览器与之通信的 web 前端。

标记的连接:

1.  Web 浏览器请求页面、javascript 等。来自 next.js
2.  Web 浏览器和 next.js 后端请求数据
3.  日志后端请求公共矩阵数据(日志机器人)
4.  next.js 后端发出经过认证的矩阵请求

有了这个想法，我决定最终开始根据几个月前我画的草图为杂志写视图。结果是有希望的；见文章开头的推文。

## 未来

接下来是用户认证/注册，然后是博客创建和评论。所有这些仍然严格地在前端，下一步将是为日志后端实现一个 API，可能使用 GraphQL。

反正目前就这些了。有什么问题吗？欢迎发表评论。

关注我了解更多更新🚀
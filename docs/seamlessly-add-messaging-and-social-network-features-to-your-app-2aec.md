# 消息和社交网络功能 SDK。你怎么想呢?

> 原文：<https://dev.to/rophilogene/seamlessly-add-messaging-and-social-network-features-to-your-app-2aec>

快速介绍: [MySocialApp](https://mysocialapp.io?ref=devto-discuss) 是一个库，它可以在几个小时内为任何应用程序带来社交网络功能，而不是几个月。

这里主要特点:

*   [新闻源/活动流](https://docs.mysocialapp.io/v1.0/reference#news-feed-activity-stream-1)
*   [评论](https://docs.mysocialapp.io/v1.0/reference#list-comments-from-post) / [喜欢](https://docs.mysocialapp.io/v1.0/reference#news-feed-list-likes-from-post)
*   [私人信息](https://docs.mysocialapp.io/v1.0/reference#private-messaging-1)
*   [通知](https://docs.mysocialapp.io/v1.0/reference#notification-1)
*   [实时通知](https://docs.mysocialapp.io/v1.0/reference#receiving-notification-event-1)

[完整的功能文档在这里](https://docs.mysocialapp.io/reference)

代码示例:

```
// message with user mention + hash tag
String message = "Hello, my name is [[user:" + myProfile.getId() + "]] this is a post with an image and #hashtag :)";

// build message
FeedPost post = new FeedPost.Builder()
        .setMessage(message) // add text message
        .setImage(new File("/tmp/my_image.jpg")) // add image
        .setVisibility(AccessControl.PUBLIC) // set post visible from all
        .build();

// publish post on public feed
Feed feed = session.getNewsFeed().create(post);

// like the post
feed.addLike();

// build comment
CommentPost comment = new CommentPost.Builder()
        .setMessage("Here a comment") // add comment message
        .setImage(new File("/tmp/my_image.jpg")) // add comment image
        .build();

// comment the post
feed.addComment(comment); 
```

Enter fullscreen mode Exit fullscreen mode

这里是一个应用程序的快速演示，100%使用我的社交应用程序 API
[https://www.youtube.com/embed/_FSg-1aEKkw](https://www.youtube.com/embed/_FSg-1aEKkw)

# 为什么要用 MySocialApp？

1.  在几个小时内为任何应用添加社交功能，而不是几个月
2.  API 与您的服务集成(Web hook、Web socket、Push APNs + FCM、RESTful)
3.  专注于让你的应用独一无二并节省大量时间的东西

# 语言

目前我们支持 3 种语言(Java，Swift，Kotlin)和 2 种框架(iOS，Android)。我们将在年底支持 8 种语言+ 5 种框架。

# 问题

你对这项服务有什么看法？这是你近期或远期需要的东西吗？您的反馈对我至关重要。谢谢你
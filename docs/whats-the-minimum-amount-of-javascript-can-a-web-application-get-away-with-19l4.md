# 一个 web 应用程序至少需要多少 JavaScript 代码？

> 原文：<https://dev.to/alchermd/whats-the-minimum-amount-of-javascript-can-a-web-application-get-away-with-19l4>

所以我读到了“臃肿”的应用程序，以及我们，尤其是 web 开发人员，是如何引入依赖关系的，好像这不算什么，因为现代计算机可以很好地处理它。令人担忧的是我们牺牲了太多(？)*性能*为了*方便*它可能会回来困扰我们，是时候我们再次把性能放在优先事项之前了。经过一些思考，我可以看到双方的吸引力——尽管后一个*我认为*更理想，也是我想问你们所有人的话题。

## 典型的例子:MyFaceTweetSter

[![](../Images/a9329e57db05da112c5e3e2146bbaccd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PNX_F4i7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://635.gtbank.com/wp-content/uploads/2018/06/Social-Media-Graphic.jpg)

今年是 2005 年。MFTS 已经成为社交媒体网络之王。他们获得了数百万的收入，每个大学生都在使用它，首席执行官已经可以看到几年后一部关于他的公司的电影。这一切都很好，直到*汤姆·祖克多西*犯了一个致命的错误:当他重构 MFTS 的`inbox.php`脚本时，他意外地使用了`in_array ($needle , $haystack)`。(哪个顺序不对。或者是？IDK 甚至知道)。这显然撕开了时空平面的一个空洞，在时间上冻结了整个 MFTS 建筑。PHP4 相当狂野。

快进到 2018 年，时空空白已经被填补。汤姆活着回来了，他想夺回他的王位。他启动了自己的 Windows XP 电脑，迅速研究了 WTH 在开发社区的进展，并决定在`<modern-mvc-framework />`移植 MFTS。他花了一个月的时间(花了两个星期谈判，因为`MyFaceTweetSter.com`已经过期并被扣为赎金),他已经准备好发射了！

*“哇哇哇，坚持住！”*，他的主要投资者贾斯汀·汀布莱克说。*“如今，每个开发者和他们的狗都使用 JavaScript。不如我们雇佣 dev.to 的人，向他们请教，这样我们就能跟上 JS 生态系统的步伐了。”*。这就是我们的切入点。我们将努力使 MFTS 的 UX 现代化，而不是把所有的包装都塞进去。所以目前，在传统的 web 应用程序中，每个动作都会触发页面重新加载。核心功能如下:

```
## The Usuals
 - Registration
 - Authentication
 - Feedback forms
 - etc.

## Friends
 - Search through the users
 - Send a "friend request" to a user
 - "Unfriend" a user

## Posts
 - A user can make a post (with photos / embedded media)
 - A chronological list of posts are presented in the home page
 - Paginated with 10 posts each
 - A user can like/unlike a post
 - A user can comment on a post

## Messaging
 - A user can send a message to one of their friends
 - A user can reply to a message sent to them
 - The messages are displayed as "threads"

## Profile
 - A user can upload a "profile picture"
 - A user can put in their personal information to be display in their profile
 - A user can update the said information
 - A user can browse through their friends profile

<!-- Feel free to add more "core" features! --> 
```

Enter fullscreen mode Exit fullscreen mode

你认为哪些是使用 JavaScript 的好选择？在提供优质 UX 的同时，我们能做到的最低限度是多少？十年前拥有大量追随者的 MFTS，在完全不使用 JS 的情况下，能够与今天的社交媒体平台竞争吗？
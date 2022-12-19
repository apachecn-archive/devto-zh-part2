# 发射颤振。供参考

> 原文：<https://dev.to/nitya/launching-flutterfyi-2n0e>

TL；DR；

> 如果你对 [Flutter](https://flutter.io) 的开发感兴趣，一定要去看看 [Flutter。参考消息](http://www.flutter.fyi)，我正在创建一个博客系列来跟踪我对这个框架、工具、最佳实践和用法的探索。
> 
> 如果你有你认为我应该涉及的话题或问题，让我知道。这篇文章的其余部分只是为了给你一个我为什么这样做的感觉。

# 我的飘忽之旅

如果你在 Twitter 或 dev.to 上关注我，你现在可能已经发现我对谷歌的新移动 SDK[Flutter](https://flutter.io)非常感兴趣。

我的旅程始于 2017 年 12 月在 [DevFest NYC](https://devfestnyc.com) 听到 [Faisal Abid](https://www.twitter.com/faisalabid) 谈论刚刚在 Alpha 发布的 Flutter。

休息期间，我安装了 SDK，看了视频，做了几个代码实验室。我对这个平台的潜力感到非常兴奋，以至于我在 1 月份的 2018 年风城发展节上谈到了它。

液体错误:内部

我一定做对了什么，因为这个视频成为了会议播放列表中点击量最大的视频。我还在 dev.to 上写了一篇关于*在 30 秒内摇摆*的帖子(顺便说一句，这篇帖子让我在每周时事通讯上排名第三，并为我赢得了[前 7 名](https://dev.to/badge/top-7)徽章，所以耶！)

[![nitya](../Images/a6e00edfd1e4b944bfbb89be7e5f9b5b.png)](/nitya) [## 30 秒后颤动

### Nitya Narasimhan 博士 1 月 16 日 181 分钟阅读

#inthirtyseconds #flutter](/nitya/flutter-in-30-seconds-1nk1)

受此鼓舞，我做了一些以前从未做过的事情。我核对了遗愿清单上的一项，并在最后一刻向奥雷利·OSCON 提交了一份提案。

液体错误:内部

为了让事情变得更简单，我记得了不起的 Kelsey Hightower 曾主动提出帮助第一次提交者审阅他们的草稿并给出建设性的反馈。

> ![Kelsey Hightower profile image](../Images/41431a2f929af05551b94a7343cf3bcd.png)凯尔西·海托华@凯尔西·海托华![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)如果你需要帮助起草你的 OSCON 2018 CFP 给我发个 DM，我会帮你解决的。[twitter.com/kelseyhightowe…](https://t.co/1TrzRzs1YV)2017 年 12 月 13 日下午 16:46凯尔西高塔@凯尔西高塔OSCON 2018 CFP 开幕啦！我们要回波特兰庆祝我们的 20 周年纪念日，但是没有你就不一样了。提交那份报告！https://t.co/j3RvqwBIx0[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=940986243990544384)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=940986243990544384)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=940986243990544384)

实话实说吧。我没想到我会被录取——但我也报名参加了纽约市版的全球多元化 CFP 日活动，我们鼓励在科技领域代表性不足的少数族裔到 T2 提交演讲！。不采纳我自己的建议似乎很虚伪。所以我做到了！

快进接受/拒绝通知和 OMG OMG OMG 的决定性的一天。

液体错误:内部

一边是兴奋，另一边是极度的恐慌。我做科技演讲已经很多年了，但这次是在 OSCON。然后事情开始变得真实。Flutter 团队(祝福他们)决定是时候加快发布进度了。

他们在二月份的世界移动通信大会上发布了第一个测试版。好吧，这是测试版更新。没什么大不了的。我能应付。

液体错误:内部

但是短短两个月后，他们发布了第二个测试版。等等，这是一个重要的更新。我们现在都是关于[镖 2](https://www.dartlang.org/dart-2) 的。再次引发恐慌症。这改变了什么？

> ![unknown tweet media content](../Images/6d864385d86d9e23b79f69d937140718.png)![Flutter profile image](../Images/bd61a45bca7de05d70809d635aed620f.png)Flutter@ flutterio![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)我们刚刚宣布了我们的第二个测试版，其中包括更容易安装，默认支持 Dart 2，等等。
> 
> 看这里->[goo.gl/hrz7ps](https://t.co/7gxCIZ2qtY)2018 年 04 月 09 日下午 19:41[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=983429716479807489)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=983429716479807489)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=983429716479807489)

事实证明这是一个重要的更新，但迁移路径并不坏。Flutter 团队非常体贴地将他们的 SDK 与' ' ' flutter''' CLI 一起发布，使[更新像输入](https://flutter.io/upgrading/) '''flutter upgrade ' ' '一样简单。

到现在为止，我确信这是一项值得关注的技术。于是在 2018 年 4 月，我在 [IWDNYC 2018](https://iwd2018.splashthat.com) 办了一个迷你颤振工作坊。鉴于我同时安排了关于*故障*和*公共演讲*的研讨会，我预计观众会很少，所以把这个安排在了最小的房间。我错了。我们的房间挤满了人，度过了一段美好的时光！

液体错误:内部

但是等等。五月来了，我们完全进入了谷歌输入输出疯狂模式。你不知道吗？我们又一次发布了 Flutter beta 3，这次距离上次更新不到一个月。

液体错误:内部

我们有一个材料设计刷新-等待！Flutter 现在是材料设计组件开发的一级目标(与 Android、iOS 和 web 并列)？这是巨大的。威尔·拉什和玛丽·夏的演讲很好地展示了这在实践中的意义。(奖励点:我最近发现威尔是在谷歌纽约总部工作的！耶！)

液体错误:内部

然后是一个名为 Emily 的开发者拥护者的双倍剂量的现场编码练习。令人惊叹的艾米丽·夏克和艾米丽·福尔图娜带领我们浏览了一个用 Flutter 和 Firebase 构建的相当丰富的移动应用程序——仅用了 310 行代码。

> ![unknown tweet media content](../Images/d0cbfb345a8447c69f5f6ca5889b4413.png)![Nitya Narasimhan profile image](../Images/4208c4de2f096d66a25a3bf682f26860.png)Nitya Narasimhan[@ Nitya](https://dev.to/nitya)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)最终外卖来自[@ flutterio](https://twitter.com/flutterio)talk by[@ bounchingshep](https://twitter.com/bouncingsheep)[@ AlchemySki](https://twitter.com/AlchemySki)
> -Flutter 正在内测
> -刚刚 310 LOC 搭建这个演示 app
> -用

最后*如果你想了解状态管理在 Flutter 中是如何工作的，以及如何*将 UI 设计*视为小部件合成中的一个练习，你需要观看的*演讲。菲利普·赫拉切克和约翰·苏利文在建筑元素方面做得非常出色，使得这个框架*具有反应性*。

> ![unknown tweet media content](../Images/11158a6c3624dd71bf76a47f6368e7b2.png)![Nitya Narasimhan profile image](../Images/4208c4de2f096d66a25a3bf682f26860.png)Nitya Narasimhan[@ Nitya](https://dev.to/nitya)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)成功了 [@flutterio](https://twitter.com/flutterio) 说话:[@ filipheracek](https://twitter.com/filiphracek)和 [@mjohnsullivan](https://twitter.com/mjohnsullivan) 用代码做着颤动深潜。
> 
> 通话状态管理& widget 树！🎉2018 年 5 月 10 日下午 17:36[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=994632315803332608)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=994632315803332608)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=994632315803332608)

有以颤振为主题的美食让我保持精力充沛..

液体错误:内部

和 Flutter plushies，这些都是赠送给早期用户和爱好者的。我忍不住画了下来..

> ![unknown tweet media content](../Images/10323f2a12dacb9bf48711076a2d6143.png)![Nitya Narasimhan profile image](../Images/4208c4de2f096d66a25a3bf682f26860.png)尼蒂亚[@尼蒂亚](https://dev.to/nitya)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)请看。现在我有了一个 10 分钟演讲的标题..以及与之匹配的幻灯片。2018 年 5 月 11 日 23 点 21 分[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=995081454282530818)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=995081454282530818)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=995081454282530818)

那天晚上晚些时候，当我带着自己的小玩具去参加 Udacity 聚会时，我知道我的下一个学习目标是什么..

> ![unknown tweet media content](../Images/d54942632e4999ce6403583b53a30096.png)![Nitya Narasimhan profile image](../Images/4208c4de2f096d66a25a3bf682f26860.png)Nitya Narasimhan[@ Nitya](https://dev.to/nitya)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)昨晚的 [@udacity](https://twitter.com/udacity) 校友活动也让我们明确了下一步的学习目标..我的旅行伙伴@flutterio 让这个选择变得很容易..😁顺便说一句，我打算叫她“点”,因为那里有太多的“破折号”😂2018 年 5 月 11 日下午 13:42[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=994935730492211201)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=994935730492211201)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=994935730492211201)

这是幸运的，因为 Flutter 团队与 Udacity 合作发布了一个完美的 2 部分课程，以帮助开发者*用 Flutter* 构建原生移动应用。哦，而且是免费的。

> ![Nilay Yener profile image](../Images/e800c8e3bd3657435114a542839bb17a.png)尼莱耶尔@ nlycskn![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)[@ unPythonistaMas](https://twitter.com/unPythonistaMas)嗨卡洛斯，有几个不错的资源可以入手:Udacity 课程:[udacity.com/course/build-n…](https://t.co/VBmyck0w2h)下午 18:27-2018 年 5 月 23 日[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=999356123785318400)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=999356123785318400)

然后回到家里，我在纽约萨拉托加温泉的当地技术会议上做了一个关于 Flutter 的 10 分钟闪电演讲。

液体错误:内部

巧合的是，“Dash”(颤动的吉祥物)也在那里，等等，这些是 dev.to 贴纸吗？为什么是的，是的，他们是！(感谢 [@jess](https://dev.to/jess) )

> ![unknown tweet media content](../Images/29a384993292679083c9f29825ff4434.png)![Nitya Narasimhan profile image](../Images/4208c4de2f096d66a25a3bf682f26860.png)Nitya Narasimhan[@ Nitya](https://dev.to/nitya)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)如果你正在参加[@ SharatogaTech](https://twitter.com/SharatogaTech)meetup——我们得到了贴纸！
> 
> 谢谢[@ the practical Dev](https://twitter.com/ThePracticalDev)[@ flutterio](https://twitter.com/flutterio)[@ Firebase](https://twitter.com/Firebase)[@ gdg](https://twitter.com/gdg)-[@ jesslenyc](https://twitter.com/jessleenyc)我想这是最后一个 Dev 了😁2018 年 5 月 16 日 21 点 57 分[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=996872393195950088)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=996872393195950088)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=996872393195950088)

如果你想开始学习 Flutter，这个演讲的[幻灯片提供了状态的快速回顾和资源链接，包括#io18 演讲和 codelabs。](http://bit.ly/2018-05-flutter4dev)

> ![Nitya Narasimhan profile image](../Images/4208c4de2f096d66a25a3bf682f26860.png)Nitya Narasimhan[@ Nitya](https://dev.to/nitya)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)Woot！！我的 1 张幻灯片回顾！非常感谢 [@SharatogaTech](https://twitter.com/SharatogaTech) 让我发言——然后过去。😂
> 
> 也有很多好问题。一个好的开始。幻灯片在这里:
> 【bit.ly/2018-05-flutte…】T21
> 
> 他们*将会*更新用于自学的资源..[twitter.com/SharatogaTech/…](https://t.co/VJ3fX1DM12)2018 年 5 月 16 日 22 点 51 分沙拉托加 tech talks@沙拉托加泰克https://t.co/3AKDBEIKQe[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=996885920279416832)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=996885920279416832)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=996885920279416832)

这是忙碌的几个月，但我的 2018 年奥斯卡演讲还有不到两个月就要开始了——自从我提交提案以来，发生了很多变化。

于是我决定开始 [*的飘起。参考消息*](http://www.flutter.fyi) 博客——既是追踪我在探索这个领域中的见解的资源，也是那些参加我的演讲或活动并想了解更多信息的人可以参考的资源。

[![FlutterCamp Logo](../Images/d0c671c88ab2345c2f6c8fb07fb77518.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ToqyN0Tk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-Hwkk-mLQsD8/WuTq_vO7lvI/AAAAAAABTNQ/4nMMRYtb2YcYAtOI0aUG2OZ6jNAaZhSQwCLcBGAs/s200/fluttercamp.png)

## 不要脸的塞

在即将于 2018 年 6 月 4 日举行的 [GDG NYC I/O Extended 2018](https://www.meetup.com/gdgnyc/events/244123474/) 活动中，Flutter 将成为两小时研究小组会议的焦点。我们还计划在本月晚些时候举办一整天的[扑腾营](https://www.meetup.com/gdgnyc/events/244123450/)研讨会；日期暂定为 2018 年 6 月 9 日，但我们仍需要解决赞助和后勤问题，敬请关注。

如果你住在大纽约地区并且正在开发 Flutter，请发表评论并保持联系！我喜欢联系和聊天，并有可能邀请你来我们的活动中发言或在#FlutterCamp 指导初学者。

直到那时..旅程还在继续..
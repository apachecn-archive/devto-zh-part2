# 使用推送波束发送关键交易推送通知

> 原文：<https://dev.to/missismada/deliver-critical-transactional-push-notifications-with-pusher-beams-2079>

[![Pusher Beams](../Images/76c0369a4c5009ba406d61f042aa395b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cwa97jJH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gw2y2h6n6kp30jedehyl.png)

我们很高兴地宣布[推杆光束](https://pusher.com/beams)已经退出测试，并普遍适用于您的生产应用！

我们感到自豪的是，超过 10，000 名开发人员正在使用 Beams 发送重要的事务通知。

以下是光束包含的内容:

*   统一的 [API](https://docs.pusher.com/push-notifications/reference/publish-api) 向 iOS、Android 和 MacOS 设备发送推送通知
*   用于管理 iOS 和 Android 应用的设备令牌生命周期的托管服务
*   [Android](https://docs.pusher.com/push-notifications/reference/android) 和[iOS](https://docs.pusher.com/push-notifications/reference/ios)SDK 包含最新的 FCM 和 APNs 更新
*   [兴趣](https://docs.pusher.com/push-notifications/concepts/interests)使用灵活的发布/订阅模式发送通知
*   [洞察](https://docs.pusher.com/push-notifications/concepts/insights)直接从客户端报告确认和未完成事件
*   具有实时通知生命周期事件日志的调试控制台

你可以今天[试用](https://dash.pusher.com/authenticate/register?ref=pn-blog)，阅读[文档](https://docs.pusher.com/push-notifications)，或者开始阅读 [iOS](https://pusher.com/tutorials/push-notifications-ios) 或 [Android](https://pusher.com/tutorials/push-notifications-android) 教程。

# 发送通知后获得可见性

当我们发布测试版推送通知 API 时，我们了解到开发人员在发送通知后缺乏可见性。苹果和谷歌的通知网关——APNs 和 FCM——都不提供送达保证。

通知是应用程序的生命线——在递送途中、新闻报道、最喜欢的球员得分或电话会议开始时提醒用户——但开发人员通常不知道通知是否已被递送或打开。

当通知被发送到具有 Beams 的设备时，一旦接收到通知，客户端 SDK 将报告回一个**确认事件**。如果用户点击通知打开应用程序，客户端 SDK 将报告一个**打开事件**。

所有打开的 Beams 通知中有一半是在一分半钟内打开的，这展示了无论用户身在何处都能收到通知的强大功能。

你可以通过 [Insights](https://docs.pusher.com/beams/concepts/insights) 或者在**调试控制台**中的实时事件日志来查看你的应用的汇总确认和打开事件。

# 停止浪费时间用网关脚手架

APNs 和 FCM 可以免费使用，但是我们从开发者那里听说围绕这些网关构建基础设施是令人头痛的。事实上，超过 80%的 Beams 开发人员告诉我们，在他们开始使用 Beams 之前，他们尝试过通过 APNs/FCM 直接集成通知。

以下是使用光束节省时间的原因:

*   四分之三的开发人员能够在 30 分钟或更短的时间内用 Beams 建立测试通知服务。
*   今年夏天，APNs/FCM 更新了由 Beams 管理的十分之一的设备令牌。开发人员不需要担心，因为我们的 SDK 会在后台更新设备令牌。
*   该 API 已准备好进行扩展。你可以每分钟向 100 万台 iOS 设备，每 17 秒向 100 万台 Android 设备发送通知。

在 Pusher，我们以提供基础设施为荣，开发人员依靠这些基础设施来构建优秀的应用程序，我们迫不及待地想看看您用 Beams 构建了什么。

# 梁的下一步是什么？

我们计划为开发人员提供更大的灵活性，以向用户发送通知，提高服务运行状况的透明度，并提高您所有设备的可送达性。

Beams 有一个活跃的开发人员社区——社区支持的特别大喊[React Native SDK](https://github.com/b8ne/react-native-pusher-push-notifications)——我们很高兴听到您对我们下一步应该构建什么的想法！

加入 Slack 上的 [Pusher 社区](https://feedback-beta.pusher.com/)或使用我们在 [docs](https://docs.pusher.com/beams) 或 [dashboard](https://dash.pusher.com/) 上的对讲机聊天。

如果你喜欢你所看到的，并且你感觉很慷慨，用一条 [tweet](https://ctt.ac/j3b65) 帮助传播这个消息，或者在 Github 上启动我们的 Beams [iOS](https://github.com/pusher/push-notifications-swift) 和[Android](https://github.com/pusher/push-notifications-android)SDK。
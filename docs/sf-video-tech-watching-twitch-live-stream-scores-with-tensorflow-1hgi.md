# SF Video Tech:用 TensorFlow 观看 Twitch 直播流分数

> 原文：<https://dev.to/tedcarstensen/sf-video-tech-watching-twitch-live-stream-scores-with-tensorflow-1hgi>

今年早些时候，Demuxed 在我们的 SoMa 俱乐部举办了他们的 SF 视频技术活动。Mux 的 Nick Chadwick 发表了激动人心的演讲和近乎完美的现场演示。如果你想亲自参加下一次活动，请在这里回复[。](https://www.meetup.com/SF-Video-Technology/)

去年年底，Twitch 的竞争对手 [Mixer](https://mixer.com/) 推出了一项新功能:Hypezone。Hypezone 是一个总是播放玩家未知的最接近胜利的战场流的频道，一直是 Mixer 上最受关注的频道之一。受技术挑战的启发，Nick 构建了一个简单版本的 Hypezone (Hypebot3000 ),为 Twitch streams 做同样的事情。

在这个演讲中，Nick 概述了他是如何解决这个问题的，从有效地观看 twitch 流和与 OCR 斗争，到最终使用 TensorFlow 创建一个工作版本。他讲述了屏幕捕捉直播流、预处理它们以及实现一个简单的 TensorFlow 模型来提取当前游戏分数的基础知识。他做的都是现场直播！

[https://www.youtube.com/embed/uDBsRyM6L7I](https://www.youtube.com/embed/uDBsRyM6L7I)

*有兴趣加入 Heavybit？我们的项目是同类项目中唯一专注于将开发者产品推向市场的项目。需要开发人员牵引、产品市场匹配和客户开发方面的帮助吗？[立即申请](https://heavybit.typeform.com/to/tP7Lh7)，开始向世界级专家学习。*
# 在线视频流媒体网站使用 TCP 还是 UDP

> 原文：<https://dev.to/_bigblind/do-online-video-streaming-sites-use-tcp-or-udp-3nj8>

这个问题源于看 twitch streams 时的一个烦恼。我经常参加 streamer 在玩 [jackbox 游戏](https://jackboxgames.com/)的流。看到 streamer 的屏幕上正在发生什么是非常重要的。我的网络连接很慢，所以 Twitch 经常缓冲。当它恢复时，它从它停止的地方恢复。

当讨论 TCP 与 UDP 时，UDP 的典型用例通常是电话会议，在这种情况下，如果您错过一帧视频，您并不在乎，您只是希望它快一些。在线直播视频流似乎与此没有太大区别，然而 Twitch 的视频播放器似乎表现得好像它正在通过 TCP，等待直到它接收到足够的数据来显示接下来几秒钟的视频。

这是怎么回事？
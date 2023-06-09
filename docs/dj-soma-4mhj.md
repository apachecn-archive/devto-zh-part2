# 【词条】Dj 索玛

> 原文：<https://dev.to/docoprusta/dj-soma-4mhj>

# 我造了什么

一个“分布式”音乐播放器 web 应用程序。

播放器在服务器上，局域网上的任何人都可以将歌曲添加到播放列表中。

如果有人向播放列表中添加了一首歌曲，他/她在给定的时间内(默认为 1 分钟)不能添加新的歌曲。

有一台设备具有管理员权限。有了这个设备，等待时间可以改变，歌曲的删除也是可能的。

有一个自动播放功能(该应用程序为此使用 youtube API)，但有时我对提供的下一首歌不满意。它应该得到改进，所以这个功能被标记为测试版。

## 演示链接

视频:

*   [视频 1](https://youtu.be/tz_K8ClsJMs)
*   [视频 2](https://youtu.be/gRuZNHXfE1Y)
*   [视频 3](https://youtu.be/ES3oOVrdOCU)

### 如何运行此应用程序

我在 linux mint 18 上测试了它，但我认为它应该可以在每个 linux 发行版上工作。(docker 附加/dev/snd 卷，在 windows 上不可用)

#### 码头工-化合物

下载 [Docker 合成文件](https://drive.google.com/open?id=1cPFyWOQXEXnErd0sHa8JGciLYz9BoQYg)并运行`docker-compose up -d`

#### 不带 docker-compose

`docker container run -d -p 8080:8080 docoprusta/dj-soma-frontend-pusher`

`docker container run -d --privileged -v /dev/snd:/dev/snd -p 5000:5000 dj-soma-backend`

## 链接到代码

[前端](https://github.com/docoprusta/dj-soma-frontend-pusher)
T3】后端

# 我是如何建造的

事实上，这是我做了一半的兼职项目之一。我试着用烧瓶插座，但它有点不可靠，很难使用。我在这里读到了 pusher 竞赛，我意识到它非常适合我的应用程序:)

我在开发过程中学到了很多，例如:youtube API，如何使用 pusher，提高了我的角度知识等。

我想进一步开发应用程序，重构代码，添加测试，也许添加新功能。

我使用的堆栈:

后端:

*   [推手❤️](https://pusher.com)
*   [Python 3](https://www.python.org)
*   [烧瓶](http://flask.pocoo.org)
*   [MPV 播放器/libmpv](https://mpv.io)
*   [youtube-dl](https://github.com/rg3/youtube-dl)
*   [python-mpv](https://github.com/jaseg/python-mpv)

前端:

*   [推手❤️](https://pusher.com)
*   [角度 5](https://angular.io)
*   [自举 3](http://getbootstrap.com/docs/3.3/)
*   [Youtube API](https://developers.google.com/youtube/v3/)

## 我是如何使用推杆的

每个客户端有一个频道(频道名称是客户端的内部 ip 地址)和一个广播频道。

客户端通道用于剩余时间通知，广播/管理通道用于其他一切。

广播频道消息:

*   歌曲添加:当有人添加一首新歌时，推送者为每个客户发送新歌的数据。
*   歌曲删除:类似于歌曲添加
*   时间位置已更改(歌曲的当前时间位置):每首歌曲该消息发送 10 次
*   音量改变:当有人使用音量滑块时，其他客户端会收到来自 pusher 的关于音量滑块新值的消息
*   自动播放已更改:如果有人选中/取消选中“自动播放”复选框，Pusher 将向客户端发送带有新值(真/假)的消息
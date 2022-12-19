# 已尝试检查 Agones

> 原文：<https://dev.to/neguse/agones-2j2o>

# 什么是 Agones

*   在 Kubernetes 上运行的定义的服务器托管系统
    *   可以用 Kubernetes 驱动 AWS 的 GameLift 之类的东西的软件
*   开发主体是谷歌和 Ubisoft
*   开放源代码，Apache License 2.0

# 现在的状态

*   发布的最新版本是 v0.1。 目前正在开发 v0.2
*   处于 alpha 发布状态，不是产品就绪

# outfitting

*   AGones 服务器用 Go 编写，ServerSDK 提供 go 和 C++的服务器
*   Agones 服务器和 Game Server 的通信通过 TCP(gRPC )进行
    *   有通知准备完成的 Ready ( )、通知进程活着的 Health ( )、通知进程结束的 Shutdown ( )
    *   对游戏服务器和游戏客户端的通信没有特别的限制，协议可以是 TCP 也可以是 UDP

# 试着做了

*   在 Kubernetes 上启动 Agones 集群
    *   [https://github . com/Google cloud platform/agones/blob/master/docs/installing _ agones . MD](https://github.com/GoogleCloudPlatform/agones/blob/master/docs/installing_agones.md)
*   用 Agones 启动 GameServer 的地方
    *   [https://github . com/Google cloud platform/agones/blob/master/docs/create _ gameserver . MD](https://github.com/GoogleCloudPlatform/agones/blob/master/docs/create_gameserver.md)
*   impressions
    *   Kubernetes 因为不知道，所以不太清楚
    *   不知道如何指定 GameServer 的缩放比例(进程数)
    *   Sidecarとは？
    *   已终止的 GameServer 进程是否一直处于关闭状态？ 怎么自行重启？

# 参考网站

*   开源代码库
    *   [Google cloud platform/agones:Kubernetes 上多人游戏的专用游戏服务器托管和扩展](https://github.com/GoogleCloudPlatform/agones)
*   博客文章
    *   [谷歌云平台 Japan 官方博客:支持在 Agones ―― Kubernetes 上构建游戏服务器的开源项目启动](https://cloudplatform-jp.googleblog.com/2018/04/introducing-Agones-open-source-multiplayer-dedicated-game-server-hosting-built-on-Kubernetes.html)
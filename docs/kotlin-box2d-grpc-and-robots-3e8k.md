# Kotlin、Box2d、gRPC 和机器人

> 原文：<https://dev.to/viniciusccarvalho/kotlin-box2d-grpc-and-robots-3e8k>

# 想法

我一直是 robocode 的粉丝，但似乎有一段时间那个社区没有新的更新了。随着用于流通信的 grpc 的推出和 kubernetes 的兴起，编写一个可以处理作为 pods 部署的机器人的游戏服务器似乎不是一个遥远的想法。

所以我从现在的设计开始:

*   服务器是用 [gRPC](https://grpc.io) 编写的，因此可以使用任何绑定到它的语言来创建客户机。
*   客户端对事件(击中、敌人检测到、击中墙壁)做出反应，并向服务器发出动作(开火、节流、旋转)。
*   服务器处理所有的物理(使用和学习 [jbox2d](https://github.com/jbox2d/jbox2d) 一个惊人的 [box2d](https://box2d.org/) 物理引擎的端口)，并执行规则(重新加载速度，健康)
*   除了 gRPC 之外，基于 web 的端点有助于控制服务器的生命周期
*   一个基于 websocket 的仪表板，用于绘制可视化世界

# 付诸行动

我选择 gRPC 是因为它支持双向流。由于引擎世界每秒更新 30 次(30 FPS)，这很容易由 gRPC 消息流处理。此外，客户端可以很容易地对双向流上的事件做出反应。

```
service GameService {
    rpc connect(stream Action) returns (stream FrameUpdate);
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，您的 bot 只需要创建一个客户端来连接到服务器，处理事件和发出动作。这实际上取决于每个 bot 实现来决定什么做得最好。

## 服务器端

在服务器端，处理模拟的主类叫做`ArenaService`，它包含所有的机器人和一个试图每秒至少运行 30 次的`suspended`函数。游戏模拟中非常重要的一件事是[让你的主循环正确](http://higherorderfun.com/blog/2010/08/17/understanding-the-game-main-loop/)，你需要确保你在帧之间计算一个增量步长，它不会总是间隔 1/30 秒，这样你的物理计算就不会疯狂。

然后，这个类只在每个机器人上循环，更新它的状态，然后`steps`进入这个世界(box2d 特性，允许它计算碰撞并更新 fixture 特性，如速度)，最后它将所有排队的事件广播给机器人。

## 相互竞争的消费者/生产者

我碰到的第一个问题是`World`类。原来它不是线程安全的。因为每个机器人都可以创建一个像`FIRE`这样的事件，向世界发射一个炮弹，所以我很快就遇到了并发问题。

我第一次天真的尝试是使用锁，然后管理这一切就变成了一场噩梦。

但是因为主循环是我们运行游戏逻辑的唯一地方，我用一个`ConcurrentQueue`把它变成了游戏逻辑，这样对世界的任何改变首先被堆叠到队列中，然后只有主循环中的一个线程修改它。

以下面的代码为例，它是通过连接到`World`实例的监听器触发的。它能检测出一个`bullet`和一个`wall`之间的碰撞。然后，它将一个`WorldEvent`堆栈到队列中，稍后将销毁来自`world`的`bullet`,并更新保存模拟中所有活动项目符号的`ServerProjectile`集合。

```
FixtureType.WALL -> {
                if (targetData.type == FixtureType.BULLET) {
                    worldEvents.offer(WorldEvent(WorldEventType.DESTROY_BULLET, mapOf("id" to targetData.context["id"]!!)))
                }
            } 
```

Enter fullscreen mode Exit fullscreen mode

# 当前状态

我仍然在尝试这一切，很多问题来自于并发的修改和对 box2d 缺乏更好的理解(我正在学习更多关于这个引擎的知识)

你可以在[https://github.com/viniciusccarvalho/robo-cloud](https://github.com/viniciusccarvalho/robo-cloud)上找到这个项目，但它仍然非常不稳定，没有构建，仍然有太多未解决的问题，不能称之为一个真正的项目。一旦我完成了围绕 box2d 的所有实验，并且对物理引擎有了足够的信心，我将进一步完善它，使之成为一个一致的版本。

这是一个项目现状的视频。物理学是好的，光线投射探测子弹在坐标系统改变后仍然是一个失误

[https://www.youtube.com/embed/OH3iljmvDRQ](https://www.youtube.com/embed/OH3iljmvDRQ)
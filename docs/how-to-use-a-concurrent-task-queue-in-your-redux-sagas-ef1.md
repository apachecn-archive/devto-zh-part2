# 如何在你的 Redux-Sagas 中使用并发任务队列

> 原文：<https://dev.to/puemos/how-to-use-a-concurrent-task-queue-in-your-redux-sagas-ef1>

#### 了解什么是并发任务队列，最佳用例，以及如何编写一个。

队列是最常用的数据结构之一。

你可能每天都在用它购物(甚至是在网上),或者给朋友发短信。

并发任务队列是一个非常强大的模式，可以真正帮助您处理任务或提高性能。

### 让我们从基础开始

#### 什么是队列？🚶🚶🚶‍

队列是一种线性结构，在一端添加值，在另一端删除值。这一原则产生了先进先出行为(FIFO ),这是队列的定义特征。两个基本的队列操作是入队(添加到后面)和出列(从前面移除)([源](https://stanford.edu/~stepp/cppdoc/Queue-class.html))。

[![](img/13e58147821401fe8ea59c7ae4d978c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--piT7WWKj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/405/1%2ALi-9xbwJWffXSSERtTX86g.png) 
*表示一个 FIFO(先进先出)队列(维基百科)*

#### 好的，我们什么时候用？

当您需要保持事件的顺序并按该顺序处理值时，请使用队列。

#### 太好了，你说服我了！但是我为什么需要并发性呢？

正如我上面提到的，一个队列一次能够处理一个值。但有时还不够快。

#### **考虑下面的情况**🙌：

你在你最喜欢的杂货店，刚刚到达收银台，但不幸的是，有许多人在等待。为了加快这个过程，商店又开了几个收银台，每个额外的收银员都有自己的队列。所以你只需要选择一个。如果其中一个收银员遇到了技术问题或者他们太慢，那么即使其他位置是空的，队列也会被延迟。

[![](img/a1621647919f2696663ca20a18f475f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ciV49jjg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AR4Dvy6CM9tbCiALKvVrshA.png)
*(@ andreagiuliaderba)*

并发任务队列来救援！💪

出于我们的目的，我们将只使用一个队列。这样，每当一个空位变得空闲时，我们将从队列中取出一个人，并将他/她发送到空闲的空位。

[![](img/4b286e7b2f37c0847fd57e4c6fdc991f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EzBqh2VR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Anidy8yr16nMUBA153rhwEQ.png) 
*单个并发队列(@andreagiuliaderba)*

万岁！🎉

### 让我们来看一个用例

上周，我正在开发一个谷歌 Chrome 扩展，它可以嗅探和下载 HLS [streams](https://en.m.wikipedia.org/wiki/HTTP_Live_Streaming) (HTTP 直播流)。

HLS 流由多个块组合而成，这些块被一个接一个地获取，并作为单个视频流传输到您的浏览器。每个流中可能有数千个文件，您需要将它们全部下载。

我们将使用我们心爱的**队列**来加快进程，并确保一个缓慢的获取不会阻碍其他的获取。

### **TL；大卫:这是代码**
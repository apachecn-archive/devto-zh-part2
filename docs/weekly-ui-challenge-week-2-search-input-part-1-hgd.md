# 每周 UI 挑战，第 2 周:搜索输入，第 1 部分

> 原文：<https://dev.to/jlr7245/weekly-ui-challenge-week-2-search-input-part-1-hgd>

本周的[每周用户界面挑战](https://dev.to/geoff/week-2-day-1-design-a-search-bar-mo6)是设计一个搜索栏，包括自动完成和以前的搜索缓存。

这实际上非常接近我已经建立的东西:

[![](../Images/1a9a23020d9b5e8c9daded2cebdcf84e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dqtqam3U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/usc4c9ujmp0w93rc5ld6.gif)

我对这个搜索栏的设计已经很满意了。然而，它的功能还有待改进。它不是动态获取立法者信息，而是通过一个必须手动更新的大型 JSON 对象进行过滤。不够理想！如你所见，它不会缓存以前的搜索。

这个项目( [callcongress.how](https://callcongress-how.herokuapp.com) )是我一开始就想重建的网站——它有一些严重的缺陷，急需彻底检修。我喜欢在社区中而不是在我自己身上做这个项目的想法，并且我已经打算开始写我的开发过程。所以，让我们开始吧！

### 草图&设计注意事项

因为我正在开发一个已经建立起来的搜索功能，所以我没有做任何高保真的草图，只有这个:

[![](../Images/538b5e4f26d110a37a39de58eac05170.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o3d2MaNv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vtdonbd6w3wzj9umxwov.JPG)

### 思绪&疑问

*   用户的搜索在什么时候会被缓存到“最近搜索”中？用户选卡的时候？当用户停止输入一段时间后。
*   也许有某种搜索按钮功能会很好。卡片可以有最少的信息，结果页面会有更多的信息。
*   想实现记忆化和去抖技术来管理自动完成的获取。

### 技术堆栈

*   反应/还原/路由器
*   Redis(目前是我最喜欢的技术之一)
*   快递/邮政快递/pg-承诺
*   flex box(flex box)的缩写形式
*   ProPublica 国会 API

到目前为止，存储库位于这里。到目前为止，它有一个认证样板和我们的政治家路由器的开端。

### 时间轴

我比这项挑战的官方时间表晚了一天。我还在想，这里的大部分繁重工作将是后端工作，我目前没有做太多，但真的很喜欢，所以这对我来说很好。因此，这里是我认为我需要采取的步骤，以实现这一点。

**第一阶段**

*   创建临时立法者表
*   创建搜索表单
*   创建与表单应采取的操作相关的操作/减少器

**第 0 阶段**

*   实现模糊搜索
*   实现本地存储来保存用户最近的搜索

**第一阶段**

*   改进数据——此时，我应该尝试从 ProPublica API 中获取实际数据
*   重新设计搜索表单，以便在用户键入时进行 API 调用
*   造型！

**第二阶段**

*   提高性能——实现 Redis 缓存来保存用户过去的搜索，并通过不查询数据库来节省时间
*   结果页面-用户可以使用搜索页面上的迷你卡将立法者添加到他们的观察列表中，或者转到包含更多信息的结果页面
*   所有问题

### 关闭思绪

这是一个真正雄心勃勃的项目，试图在一周内完成，我已经*也许*延伸了挑战应该是什么，但我很高兴能够解决它。电话会议。我对 How project 有一段时间的强烈感受，我期待着有机会改进它。
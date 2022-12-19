# 多动症复仇者的每周漫游

> 原文：<https://dev.to/wolfhoundjesse/adhd-avenger-weekly-wanderings-1d12>

*这篇文章是从我最近恢复的博客[文学现象](https://wolfhoundjesse.wordpress.com)上删减下来的，基于这样一个假设，你可能没有兴趣知道我这周在听什么，在读什么，或者我的小孩在做什么样的古怪事情。*

# 某个未知数的第一个

为了反思我的一周并重新投入写作，我决定每七天记录一部分我的内部和外部遭遇。如果你通读我的 WordPress 博客的其余部分，你会发现写作之间的差距，有时是几年。“某个未知数”现在有意义吗？很好。

# 本周科技

今天标志着我新工作第三周的结束！我一直在记录一个遗留代码库，我发现一些工具使这个过程变得非常容易:

*   这周我用 mermaid 创建了三个序列图，用起来真的很开心。文档有时是不确定的，所以我访问回购寻找答案。我在自述文件中发现了 205 个未解决的问题、12 个拉请求和一个帮助请求。这可能就是我一直在找的开源项目吧！

*   与 mermaid.js 一起，这个插件提供了 VS 代码的预览，并支持多种格式。对于序列图来说，它工作得很好，但是当我试图制作一个流程图时，迷你预览严重地妨碍了它。这让我想到了另一个新的可视化工具。

*   [draw.io](https://draw.io) -这个东西就是太神奇了，可以融入到合流里。我最终完成了流程图，然后我继续创建实体关系的可视化。

## 分离认证和授权

我已经有一段时间没有听到关于 [PolicyServer](https://policyserver.io/) 的任何消息了，但是我一直在从事一个 Dockerized 示例项目，其中包括 IdentityServer4、PolicyServer 以及使用 RabitMQ 和 MassTransit 的消息传递。我计划为单独的对话提供两个微前端应用程序，一个是与[克里斯·戴维斯的 eev](https://github.com/chrisdavies/eev) ，一个是与[Canopy](https://github.com/CanopyTax/single-spa)的 single-spa。如果这听起来雄心勃勃，那就去[企业集成模式](https://www.enterpriseintegrationpatterns.com/)研究一些更复杂的拓扑。你可能也会经历一些网页设计的怀旧情绪。

## 不要喂巨魔

我浏览了所有时间的热门 dev.to 帖子，在那里我找到了这篇关于在本地存储中存储 jwt 的风险的文章。

[![rdegges image](img/751d3a70466613d2e14ba00f2738fd4e.png)](/rdegges) [## 请停止使用本地存储

### Randall de gges 1 月 30 日 1811 分钟阅读

#programming #security #javascript](/rdegges/please-stop-using-local-storage-1i04)

当然，它有一个 clickbait 标题，但我发现内容非常丰富。继续看评论，我发现这个话题是一个非常不受欢迎的观点。攻击来自各个角度(例如:*“在你的小论文中使用迷因有点讨厌，除非你只有五岁。”*)，但 [@rdegges](https://dev.to/rdegges) 自始至终都保持着圆滑和愉快。我印象深刻！

## 更多关于认证系统

现在你已经认识到了一个主题。我在 2016 年阅读了一篇关于身份认证系统的文章，因为我已经看到了这两种系统的使用，我想更深入地了解每种系统的优缺点。在我的上一份工作中，缩放并不是一个真正的问题，所以学习所有可以成为一个因素的方法是很有趣的。很好奇用哪个的人多。

# 如此而已。
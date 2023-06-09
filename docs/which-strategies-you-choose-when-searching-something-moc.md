# 当你搜索某样东西时，你会选择哪些策略？

> 原文：<https://dev.to/khanhtc1202/which-strategies-you-choose-when-searching-something-moc>

我们在网上做的最多的事情之一就是搜索，每个人都有自己的搜索策略。我的情况是:

1.  在谷歌上搜索:))
2.  在第一页的结果中检查答案，如果没有，请移至第二页。
3.  在第二页的结果中检查答案。
4.  如果我在谷歌搜索结果的第二页仍然没有找到任何有用的答案，那就转到 Bing，做我在谷歌搜索中做的事情。
5.  如果还是找不到，转到另一个搜索引擎比如 Yandex，Ask，Duckduckgo，...

当然，每个人(可能按地区)都有自己喜欢的搜索引擎。由于这是你最喜欢的搜索引擎，它工作得很好，适合你的任务。但在某些情况下，可能不会。因为它是面向所有人的搜索引擎，所以在某些情况下，返回值不是您想要的结果，而是最常见的结果。

例如，我试图在 Github 上搜索关于`yasuhiro matsumoto` ( [@mattn](https://dev.to/mattn) -我的偶像之一)，但它在结果的第一行返回了`yukihiro matsumoto` (ruby 的创造者)。

[![](img/dd2a7b5ebdb118f2196b6725aced319f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--29QaEvUg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nklmjxrtqoz0871ltpve.png)

当我在 Bing 上试用时，结果更糟。

[![](img/b238f87180e0579cf7b422f1a1649621.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f4ssz5kS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nwoljxq4wjymy9q1zcyz.png)

Ask 的回报似乎更好，但仍然没有给出我想要的第一级。

[![](img/b5e2e33cfd0cb5c34c32e18e8133f1e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xh4daaGF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fb7kw5aeue0whqewc07h.png)

所以我添加了一个简单的过滤器:提取所有其他搜索引擎的结果，并按照我定义的规则进行合并(例如，获取所有交叉匹配的 URL——出现在两个或更多搜索引擎结果中的 URL 将是可信的 URL)。

[![](img/57954f8e9249912fdb6269117320bf20.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c7uQlMB1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hbgbehcwcxv6vy2w7aib.png)

我创建了一个实现上述想法的简单程序，它叫做`Boogeyman`——一个控制台应用程序(我是 cli 工具的粉丝)。源代码在 [github](https://github.com/khanhtc1202/boogeyman) 上，二进制可执行文件在[这里](https://github.com/khanhtc1202/boogeyman#download)可用。

所有策略都在`domain/ranker.go`中定义。目前，有三种策略:

1.  Top:返回每个搜索引擎的排名靠前的结果。
2.  交叉匹配:通过多个搜索引擎返回交叉匹配的结果。(出现在 2 个或更多搜索引擎中)
3.  All(限制为 20):返回所有来自搜索引擎的 URL。

请随意实施您自己的搜索策略，或者添加新的收集器，从您最喜欢的搜索引擎收集结果。

新收集器必须实现`Collector`接口

```
// file: adapter/persistent/service/collector.go
type Collector interface {
    GetSearchEngineType() domain.SearchEngineType
    Query(keyword domain.Keyword) (*domain.SearchEngine, error)
} 
```

下面是在交叉匹配策略下，用`yasuhiro matsumoto`关键词在 Boogeyman 上搜索的结果。

[![](img/4865f5a69c42dabbc58ca913a351cac2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gJWvnE6h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v74gul60223kn2j0kko2.png)

和夜魔玩得开心点:))
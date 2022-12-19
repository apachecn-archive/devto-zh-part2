# Golang 忍者简讯-第 10 期

> 原文：<https://dev.to/jexiacom/golang-ninja-newsletter---issue-10-5e3j>

每周[马尔滕·贝泽默](https://medium.com/@veger)7 月 15 日[免费加入](https://www.getrevue.co/profile/golang-jexia/issues/golang-ninjas-newsletter-issue-9-119028?)或添加[时事通讯 rss](http://fetchrss.com/rss/5b27d6348a93f858388b4567788630470.xml)

[![](../Images/7972cd544e9250c65782b3f1ba394cd3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cHFBQDLx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0r4kshz09w5whrvmmtop.jpg)

通过 Maarten Bezemer 为 Golang Ninjas 提供的每周简讯，了解最新动态并领先于同行。

## 基本原则

**[从地上切片起来](https://dave.cheney.net/2018/07/12/slices-from-the-ground-up?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

灵感来自与一位同事关于将切片用作堆栈的对话。对话变成了对围棋中切片工作方式的讨论。

**[理解 Golang 中的上下文包](https://medium.com/@parikshit/understanding-the-context-package-in-golang-b1392c821d14?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

Go 中的上下文包在与 API 和慢速流程交互时可以派上用场，特别是在服务于 web 请求的生产级系统中。

## 最佳实践

**[F1 2017 遥测系统，使用 Go、InfluxDB 和 Grafana](https://medium.com/coinmonks/building-my-own-telemetry-system-for-f1-2017-game-using-golang-influxdb-and-grafana-48dedbd2cdc1?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)T3】**

我在我的 F1 2017 游戏(PS4)中发现了一个功能，在菜单中导航时，我播下了一个名为 UDP 遥测设置的选项，其中包含许多网络配置。

**[使用接口和依赖注入进行未来验证设计](https://medium.com/dm03514-tech-blog/you-are-going-to-need-it-using-interfaces-and-dependency-injection-to-future-proof-your-designs-2cf6f58db192?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

接口的使用是一种易于理解的技术，它能够创建可测试和可扩展的代码。

## 教程

**[简化云原生开发](https://micro.mu/blog/2018/07/04/go-config.html?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

微博是微服务和分布式系统的资源。

**[使用 Go 进行 web 组装应用](https://sebastian-holstein.de/post/2018-07-05-go-wasm-application/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

一周前，Go 1.11 第一个测试版出来了。对我来说，这是一个特殊的版本，因为它是第一个支持 WebAssembly (WASM)的版本。

**[Go WebAssembly:绑定结构到 JS 引用](https://medium.zenika.com/go-webassembly-binding-structures-to-js-references-4eddd6fd4d23?gi=4269d691a23e&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

Go 1.11 将提供对 WebAssembly 的支持(预计在 2018 年 8 月)，同时还将提供全新的 syscall/js 包。

## 工具

**[布法罗-天蓝色](https://medium.com/azure-developers/https-medium-com-marstr-introducing-buffalo-azure-f7243670c7f9?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)T3】**

对于 Go 开发者来说，Buffalo 是一个 Ruby-on-Rails 风格的快速 web 开发框架。

**[依赖注入进去](https://outcrawl.com/go-dependency-injection/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

在 Go 中处理依赖注入的小实用程序包(在 tinrab/kit 中)。目标是简单，并让它很好地适应我目前的副业项目。

**[Go 1.11:地鼠的 web assembly](https://medium.zenika.com/go-1-11-webassembly-for-the-gophers-ae4bb8b1ee03?gi=e4dff26f7fe6&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

2017 年 2 月，go 团队成员 Brad Fitzpatrick 在 golang/go 上发布了 WebAssembly 支持问题。

**[用 Go](https://hackernoon.com/writing-a-reverse-proxy-in-just-one-line-with-go-c1edfa78c84b?gi=6b5cffc0b74d&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)** 只用一行写一个反向代理

把你的编程语言挂在门口，来欣赏我所遇到的最好的标准库。

**[G.E.R.T:一个基于 Go 的嵌入式应用工具包](https://dspace.mit.edu/bitstream/handle/1721.1/112838/1015201398-MIT.pdf?sequence=1&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

本文介绍了一个新的嵌入式工具包，Golang Embedded RunTime (GERT)，，它是专门为并发的裸机嵌入式应用设计的。GERT 是 Go 运行时的修改版，可以在裸机 ARMv7a 系统 上引导并执行 Go 代码。

**[使 Jepsen 在线性时间内检查可线性化性](https://github.com/rystsov/fast-jepsen?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

测试分布式系统的一致性保证。Jepsen 执行操作，注入故障，收集历史，然后尝试查看历史是否可线性化。

**[固执己见，P2P 网络协议栈为分散协议](https://medium.com/perlin-network/noise-an-opinionated-p2p-networking-stack-for-decentralized-protocols-in-go-bfc6fecf157d?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

问题是，还没有一个开发 P2P 应用程序的标准化解决方案被任何社区广泛采用。

## 观点

**[我打了代码，代码赢了](https://medium.com/@markbates/i-fought-the-code-and-the-code-won-7860a1d7fd90?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

我成为“专业”开发人员已经将近 20 年了。这段时间我学到了很多东西。我想告诉你一个关于我生命中最后几周的故事，那时我忽略了那些生活教训。

**[弱记忆模型的操作语义](https://www.duo.uio.no/bitstream/handle/10852/59130/nwpt17.proceedings.mmgo.pdf?isAllowed=y&sequence=2&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

一方面，内存模型必须足够宽松，以允许通用的硬件和编译器优化。另一方面，越宽松，开发者就越难对他们的程序进行推理。为了减轻负担， 弱内存模型应该提供所谓的数据竞争自由保证，这允许 根据顺序一致性进行推理，只要程序是无数据竞争的。

**[拜拜微服务:从 100 个问题儿童到 1 个超级巨星](https://segment.com/blog/goodbye-microservices/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

Segment 是你一直想要的分析 API。这是一次安装所有你喜欢的分析工具的最简单的方法！

## 录像

**[带 Golang 的简单 Web App&Web assembly](https://www.youtube.com/watch?feature=youtu.be&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter&v=t6f7438oMmM)T3】**

您将看到如何使用 Golang 和 WebAssembly 构建一个简单的 webapp。

**[黑仔机器人 101 跟围棋](https://www.youtube.com/watch?feature=youtu.be&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter&v=ryA1M1iA8pw)**

杰娜·萨库纳吉斯·萨塔纳拉奇展示了“黑仔机器人 101 与围棋”。对于大多数开发人员来说，机器人技术一直是一门难以捉摸的艺术。

**[测量内存分配以及如何减少它们](https://www.youtube.com/watch?time_continue=6&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter&v=rfXSrgIGrKo)**

在这一集里，我们将学习如何测量一个程序正在执行的内存分配，以及如何通过同步来减少内存分配。池，以及如何通过 pprof 来识别哪段代码正在分配内存。

## 播客

**[围棋时间#79:史蒂夫法兰克王国](https://changelog.com/gotime/79?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)的新围棋品牌战略**

史蒂夫·法兰克王国参加了这个节目，告诉了我们关于 Go 新品牌战略的一切(别担心，地鼠哪儿也不会去！)

每周[马尔滕·贝泽默](https://medium.com/@veger)7 月 15 日[免费加入](https://www.getrevue.co/profile/golang-jexia/issues/golang-ninjas-newsletter-issue-9-119028?)或添加[时事通讯 rss](http://fetchrss.com/rss/5b27d6348a93f858388b4567788630470.xml)
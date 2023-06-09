# 区别是什么:持续集成、持续交付和持续部署。

> 原文：<https://dev.to/david_j_eddy/whats-the-difference-continuous-integration-continuous-delivery-and-continuous-deployment-183l>

[*从我的博客上传的十字架。](https://blog.davidjeddy.com)

DevOps 有一堆新的目标、实践和术语。其中许多被混淆、误用，或者仅仅是被那些由于 DevOps 的技术本质而没有内化具体差异的人错误地定义了。在这里，我们用通俗的语言分解了三个最常见的术语，并试图提供一个清晰的例子来区分这三个术语。

## 连续累计

持续集成特指不断将变更集合并到真理源中的过程。通常，这是开发人员将更改后的代码推送到共享的远程位置。例如，一个由 12 名开发人员组成的团队每天可以处理 18 个不同的 GiT 分支，包含超过 67 个提交。变化真大！在一个工作周的过程中，提交的变更数量很容易超过 300 个。当部署一个新版本的时候，所有这些变更都必须合并到一个单独的分支中。如果发布的压力还不够大，团队还必须确保所有的工作都在一起。我保证，每周 300+的变化会导致问题。这个问题意味着熬夜，处理来自企业主的压力，由于害怕发布日而感到紧张，企业主不信任你，也不信任你的团队按时按目标交付的能力。在这种情况下没有人会赢。

<figure>[![](img/ffeb35e1c1e7c82e2657c83cdb0ed100.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kfKtrN_i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/09/Nonsensical_1_top.png) 

<figcaption>我被告知文章中的图像要保持读者的注意力。</figcaption>

</figure>

进入持续集成。以该组织中的同一个开发团队为例，但是不要等到发布日才在一天中多次组合变更。变更被*持续地集成*到目标 GiT 分支中，以确保合并问题(当一个文件被不同的人以两种方式变更时)在变更发生时立即得到处理。不是在试图处理释放的时候。(这是 DevOps 中‘左移’概念的一部分。)当发布日到来时，在发布事件发生之前很久，变更就已经被合并并发布了。

## 连续交货

作为一个概念，持续交付是从无到持续集成的逻辑进展，并向完全自动化迈进了一步。为了提供连续的交付，需要适当的自动化质量保证步骤，例如自动化测试、监控、变更和度量。为什么？因为连续交付意味着应用于一个软件的任何变更都随时准备交付。按下按钮，软件应该部署到生产环境中。你 100%必须有一个高水平的保证一切正常，没有任何损坏。

<figure>[![](img/4e8f1d23cdcaf5dc5ba7936008f5b678.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q65PLQ95--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/09/Nonsensical_2_bottom.png) 

<figcaption>我很喜欢来自[【https://chadhagen.com/Nonsensical-Infographics】](https://chadhagen.com/Nonsensical-Infographics)</figcaption>

</figure>

的无厘头信息图

我在心理上将持续交付与持续部署分开的方式是将它想象成一个 UPS 包裹:在支付、处理、测试、处理和准备使用之后，交付就在你的家门口；连续部署软件包被解包、安装和使用。

## 持续部署

这就把我们带到了今天的最后一个概念:持续部署。三者中我认为最容易定义和概念化的。顾名思义，持续部署是指*将*软件持续部署到用户(即真实的人)可以使用该应用程序的地方的过程。

<figure>[![](img/a46006f8df91d1f2a9f4b20ed0e5efaa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GkVSDJQP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/10/square_nonsense_infographic_med.jpeg) 

<figcaption>它们看起来那么整齐。</figcaption>

</figure>

连续部署是自动化的圣杯，也是许多企业最难采用的。但是，在取得持续集成成功后，这似乎很容易。那么为什么送货如此难以接受呢？因为这意味着非常单一的部署分支得到部署。没有人为干预。没有质量关。没有变更控制委员会的批准。如果接收到变更，则部署变更。

## 示例实现路径

作为一名开发人员，您正在开发特性 1234。作为工作流程的一部分，您可以将功能-1234 隐藏在应用程序中的功能标志后面。午餐前和下班时，你会立即提交你的变更并纠正任何合并冲突。这就是持续集成。

作为组织内自动化策略的一部分，您必须创建单元测试、集成检查和端到端场景，以便允许提交到原始存储库中。如果自动化全部通过，则允许将提交应用到源存储库。这是连续交货。

<figure>[![](img/b8c6096956bab5bc40ea2eb9db780ef3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--heqFuFi2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/nonsensical_1_web_crop_1.png) 

<figcaption>最后一个，我保证。</figcaption>

</figure>

鉴于上述情况，工作稳定，组织看到了快速接收最终用户反馈的价值，并相信工程团队会严格控制停机，并在出现问题时将爆炸半径降至最低(从而使组织能够在竞争中领先)。假设所有的自动化质量保证和测试指标都通过了，代码变更会立即部署到一个实际的生产环境中。这就是持续部署。

## 资源

*   [去流](https://jeffkreeftmeijer.com/git-flow/)
*   [DevOp 成熟度调查](http://devopsmaturity.com/)
*   [https://stack ify . com/continuous-delivery-vs . continuous-deployment-vs . continuous-integration/](https://stackify.com/continuous-delivery-vs-continuous-deployment-vs-continuous-integration/)
*   [https://www . atlassian . com/continuous-delivery/ci-vs-ci-vs-CD](https://www.atlassian.com/continuous-delivery/ci-vs-ci-vs-cd)
*   [https://puppet . com/blog/continuous-delivery-vs . continuous-deployment-what-s-diff](https://puppet.com/blog/continuous-delivery-vs-continuous-deployment-what-s-diff)
*   [https://semaphoreci . com/blog/2017/07/27/what-is-the-difference-continuous-integration-continuous-deployment-and-continuous-delivery . html](https://semaphoreci.com/blog/2017/07/27/what-is-the-difference-between-continuous-integration-continuous-deployment-and-continuous-delivery.html)

## 结论

Dev(Chat/Git/Sec/No)Ops 是一个不断变化的地方。术语和首字母缩略词很快变得令人困惑。我希望这有助于澄清一些最常用的短语。

<figure>[![](img/b620d79b3d228d314590feacec09ceb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6bW4Ew-l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/10/path-success.jpg)

<figcaption>devo PS 领养的每一个故事。</figcaption>

</figure>

这并不容易，但是如果你记住这样一个事实，即'**每一项**业务都是一项**软件**业务'(瓦茨 S. Humphrey)，我们就会越早认识到软件工程就像任何其他形式的工程一样，越好。为了获得相同的回报，我们需要应用相同的原则。更大的可见性、更强的产品、灵活的内部结构、更快的速度，以及作为工匠职业的尊重。这让组织变得优秀；谁不想[成为优秀的](https://www.theguardian.com/culture/2018/sep/10/how-we-made-bill-and-teds-excellent-adventure-stephen-herek-alex-winter#i)。

你觉得 DevOps 怎么样？您是否有在组织中实施 DevOps/CI/CDs 的经验？你对组织和市场的感知破坏有什么看法？
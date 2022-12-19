# 有什么区别:A/B 测试与蓝/绿部署？

> 原文：<https://dev.to/david_j_eddy/whats-the-difference-ab-testing-vs-bluegreen-deployment-3p77>

Cross 从[我的博客](https://blog.davidjeddy.com)发布。

## 介绍

在今天的技术世界中，许多单词和短语来来去去，甚至根据上下文改变意思。在本文中，我们来看两个听起来相似，但有些相关的不同术语:A / B 测试和蓝/绿部署。

## A / B 测试

<figure>[![](../Images/80a36dece5001f80e43a921ef310e13c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--alN21AMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/ab-testing.png) 

<figcaption>来源于[https://www.optimizely.com](https://www.optimizely.com)<——酷人。</figcaption>

</figure>

**什么事？**

根据维基百科的解释，A / B 测试是比较同一个主题的两个版本，并测量结果反应。雪佛兰还是福特？两者都是汽车制造商，两者都制造轿车，两者都有 4 个车门和座椅系列。一个有彩色窗户，而另一个有一个别致的收音机。你选哪个？恭喜你，你刚刚接触到了 A / B 测试(我知道这是人为的，但我希望你明白这一点)。两个非常相似，略有不同的东西，衡量用户回复率。

**它不是什么。**

它不是神奇的营销工具，也不是万能的工程解决方案。

## 蓝色/绿色部署

<figure>[![](../Images/f6ab48a12741287394768fea397fb0c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bGdnl1K2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/blue_green_deployments.png) 

<figcaption>来源于[https://martinfowler.com/](https://martinfowler.com/)<——聪明的纨绔子弟。</figcaption>

</figure>

**什么事**

“蓝绿部署是一种技术，它通过运行两个相同的生产环境(蓝环境和绿环境)来减少停机时间和风险。”cloudfoundry。我自己也说不出比这更好的了。两种环境，都是生产。一个版本可能是 1.0.0(绿色)，而蓝色版本是 1.0.1。很多时候，流量会慢慢增加到蓝色，同时观察用户行为中的错误或不良变化。一旦所有流量都从绿色(1.0.0)版本中移走，环境就会关闭。在这一点上，“蓝色”变成“绿色”，循环重新开始。

**不是什么**。

它不是神奇的工程工具，也不是万灵药。

## 比较

<figure>[![](../Images/ee84f1364106757c831069168b2bbd1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PKjABEs0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/AI8Tg15.jpg) 

<figcaption>有时候我想做什么。</figcaption>

</figure>

**什么叫相似**

同一个核心产品的两个版本同时起作用。衡量反应并做出相应的反应。成功的 A / B 测试结果可能直接导致蓝/绿部署。部署失败可能会导致额外的 A / B 测试功能需求

**有什么不同**

A / B 是一种行为监控范式。蓝/绿是一个工程过程。就是这样。这两者可以，而且经常一起使用，都是为了同一个最终目标:改善产品的用户体验。

## 额外资源

**蓝/绿部署:**

*   [https://docs . cloud foundry . org/dev guide/deploy-apps/blue-green . html](https://docs.cloudfoundry.org/devguide/deploy-apps/blue-green.html)
*   [https://sweetibharti . WordPress . com/2016/11/11/red-black-deployment-on-AWS/](https://sweetibharti.wordpress.com/2016/11/11/red-black-deployment-on-aws/)
*   [https://martinfowler.com/bliki/BlueGreenDeployment.html](https://martinfowler.com/bliki/BlueGreenDeployment.html)
*   [https://rollout.io/blog/blue-green-deployment/](https://rollout.io/blog/blue-green-deployment/)
*   [https://octopus . com/docs/deployment-patterns/blue-green-deployment](https://octopus.com/docs/deployment-patterns/blue-green-deployments)
*   [https://stack overflow . com/questions/45259589/红黑部署和蓝绿部署有什么区别](https://stackoverflow.com/questions/45259589/whats-the-difference-between-red-black-deployment-and-blue-green-deployment)

**A / B 测试**

*   [https://en.wikipedia.org/wiki/A/B_testing](https://en.wikipedia.org/wiki/A/B_testing)
*   [https://vwo.com/ab-testing/](https://vwo.com/ab-testing/)
*   [https://www . optimize ly . com/optimization-glossary/a b-testing/](https://www.optimizely.com/optimization-glossary/ab-testing/)
*   [https://unbounce . com/landing-page-articles/what-is-ab-testing/](https://unbounce.com/landing-page-articles/what-is-ab-testing/)
*   [https://blog.hubspot.com/marketing/how-to-do-a-b-testing](https://blog.hubspot.com/marketing/how-to-do-a-b-testing)
*   [https://TechCrunch . com/2014/06/29/ethics-in-a-a-data-driven-world/](https://techcrunch.com/2014/06/29/ethics-in-a-data-driven-world/)

## 结论

像生态位生态系统中的许多主题一样，概念可以有不同的标签，但在其他生态位领域中的意义是相同的；或者完全不同，尽管它们有相同的名称(*cough * Lambda *cough*)。使用范围狭窄的概念标签时，要注意上下文和受众。
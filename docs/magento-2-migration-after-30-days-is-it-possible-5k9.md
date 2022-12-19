# Magento 2 天后迁移:可能吗？

> 原文：<https://dev.to/tigren5/magento-2-migration-after-30-days-is-it-possible-5k9>

毫无疑问，有很多理由将 Magento 1 迁移到 Magento 2，尤其是 Magento 已经发布了最新的惊艳版本——[Magento 2 . 2 . 4](https://www.tigren.com/magento-2-2-4-release/)。与 Magento 1 相比，Magento 2 无疑带来了更好的设计和性能。如果回到几年前，由于不稳定，没有多少人敢升级到 Magento 2。Magento 2 的第一个版本有很多错误。但是现在，有一个不同的故事，当 Magento 似乎把所有的努力创新，改善和增强 Magento 2 的功能。

此外，您应该考虑尽快从 Magento 1 迁移到 Magento 2，因为 Magento 可能会在不久的将来停止为其“第一个孩子”提供支持。点击了解更多信息[。](https://www.tigren.com/magento-2-migration/)

除此之外，Magento 2 迁移是一个复杂的过程，需要你的时间、金钱和努力。对于 Magento 开发者来说，他们大概知道有多复杂；因此，这篇博文不是为他们写的，而是为 Magento 商店的老板写的，他们将把 Magento 1 迁移到 Magento 2。换句话说，在今天的帖子中，我们将向 Magento 商家展示如何在最佳的时间段内平稳、循序渐进地进行 Magento 迁移。

## Magento 2 迁移前需要了解的重要事项

[![](../Images/bddd58762b9d61318822cd287fca4886.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3RF6HKT8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.tigren.com/wp-content/uploads/2018/05/magento-2-migration.png) 
很多人认为 Magento 1 迁移到 Magento 2 的过程*类似于*更新到 Magento 1 的最新版本。

但是，绝对*错*！

更具体地说，Magento 迁移意味着你必须在 Magento 2 上从头开始建立一个新网站，但要从旧网站迁移数据。

您不能继续使用当前的 Magento 1 主题、扩展和代码。我们将在接下来的部分中详细讨论。现在，让我们深入了解 Magento 2 的迁移过程！

## 30 天将 MAGENTO 1 迁移到 MAGENTO 2...

根据我们的经验，完成 Magento 迁移过程平均需要 4-5 周(大约 30 个工作日)。但是，如果您的站点需要大量定制功能和主题定制，并且需要迁移大量数据，那么升级的时间会更长。让我们看看迁移会涉及哪些内容:

[**7 天:回顾旧址，为即将到来的迁移做准备<准备>**](#preparation)

[**5 天:主题迁移**](#theme)

[**5 天:数据迁移**T3】](#data)

[**3 天:延期迁移**](#extension)

[**5 天:自定义代码**](#code)

[**2 天:设置并配置新站点<配置>**T3】](#configuration)

[**3 天:测试，修复 bug，上线！<测试>** 测试](#testing)

### 1.准备

[![prepare for magento 2 migration](../Images/ab8f0549f0ce4ca6c144ea0856cfffea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZYe53jai--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.tigren.com/wp-content/uploads/2018/05/prepare-for-magento-2-migration.png)

在开始 Magento 迁移过程之前，请查看现有的 Magento 1 商店，并确定哪些是您“真正需要的”,哪些需要迁移到新站点。例如，尽管您的网站可能包含巨大的数据库资产，但是您需要迁移的内容包括产品、类别、客户和订单。此外，在扩展方面，只保留您认为必要的内容，删除多余的内容。

之后，强烈建议对 Magento 1 网站进行彻底备份，保留所有文件、文件夹和数据库，以应对糟糕的情况。

此外，我们建议您对 Magento 1 和 2 商店使用相同的托管服务器(可选)。

### 2.主题迁移

[![magento 2 theme migration](../Images/bbcf0e0f1508bd1fc2d0befc1c98b31f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g8hO0W3S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.tigren.com/wp-content/uploads/2018/05/magento-2-theme-migration.png)

正如我们之前提到的，不可能将主题和模板从 Magento 1 移动到 Magento 2 的新站点。对此有几种解决方案:

*   **第一种解决方案:**为 Magento 2 购买一个现成的主题来申请你的新网站。Magento marketplace 或 Themeforest、TemplateMonster、VenusTheme 等知名主题市场上有数百种吸引人的设计供您选择，...

优点:你的新网站设计将有很多选择，节省你的时间用于主题设计和编码，也大大节省了你的预算。虽然你需要花费 100-200 美元来获得一个设计良好的 Magento 2 响应主题，但你必须支付十倍的费用才能为你的商店获得一个独特的设计。

缺点:其他 Magento 电商店也可以买主题，和你的店面外观一样。要解决这个问题，您可以自定义主题以匹配您的品牌形象。主题定制的成本很大程度上取决于你想要定制的程度和你合作的 Magento 开发者/设计者。

*   **第二个解决方案:**找到并利用 Magento 2 主题进行免费下载。在上一篇博文中，我们列出了 *[100+个有用的免费 Magento 响应式主题和模板](https://www.tigren.com/100-free-magento-ecommerce-responsive-themes/)* 供你参考。

优点:这种解决方案的明显优势是，你必须投资 0.00 美元来实现新的外观。

缺点:与第一个解决方案相比，可供选择的 Magento 主题数量只有四分之一，甚至更少。此外，这些免费主题和模板的质量也没有保证。它们的功能大多非常有限。因此，在申请新网站之前，您应该仔细检查它。

*   **第三种解决方案:**从头开始创建新的网站设计。对于这个选项，你可以雇佣一个 Magento 代理或者仅仅是一个有 Magento 经验的网站设计师来按照你的要求进行设计。

优点:获得一个独特的网站设计，满足你所有的需求和要求。

缺点:您必须投入大量资金来部署这个解决方案。此外，可能需要 1-2 个月来完成 PSD 和前端编程的设计。

*   **第四个解决方案:**克隆您的 Magento 1 商店。如果你还保留着商店的设计(在 PSD 文件下)，你可以考虑克隆它为你的 Magento 2 商店创建相同的外观。

优点:保持现有的 Magento 网站设计，让所有顾客都熟悉。

缺点:网站克隆的时间取决于你当前设计的复杂程度。然而，克隆一个网站，一般来说，也需要相当长的时间(平均至少 1 个月)。此外，你最终得到的是一个与旧设计相似(70-80%)但不完全相同(100%)的设计。

*   **第五个解决方案:**克隆你感兴趣的任何网站，并为你的新 Magento 2 网站创建类似的外观。

优点:创建一个新的 Magento 2 网站设计，这是类似于你最喜欢的一个，也给你的买家带来新鲜感。

缺点:像第四种解决方案一样，这种方法会耗费你的时间和成本。

#### 【Magento 主题迁移的最佳解决方案是什么？

尽管介绍了许多解决 Magento 主题迁移问题的方案，我们还是强烈推荐第一个方案。总的来说，使用一个现成的 Magento 2 主题将花费最少的时间(寻找合适的主题)，金钱(拥有主题)和精力(安装和定制主题)。我们的大多数客户现在都选择了这个选项。

此外，在你的新 Magento 2 网站上部署主题的总时间(包括安装、配置和小的定制)平均为 4-5 天。

### 3.数据迁移

[![magento data migration](../Images/530ab7b8e18829f5030d583979e4f7fc.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--_EqXURyH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.tigren.com/wp-content/uploads/2018/05/magento-data-migration.png) Magento 数据迁移是将您的所有数据(包括产品、类别、客户、订单、评价、评论等)转移到新 Magento 2 站点的过程。诚然，这是 Magento 2 升级过程中最关键的阶段，要求您拥有丰富的知识和经验来进行迁移。否则，一个小小的错误就可能导致严重的数据丢失而无法恢复。

...
**[【阅读全文】](https://www.tigren.com/migrate-magento-1-to-magento-2/)**
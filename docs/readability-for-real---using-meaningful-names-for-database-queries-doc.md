# 真实的可读性——在数据库查询中使用有意义的名称

> 原文：<https://dev.to/alvarocavalcanti/readability-for-real---using-meaningful-names-for-database-queries-doc>

[![Readability](../Images/d4de4038809d1538e3dca2b665506185.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nmzfTXBV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nhxqq6rplki35xzttfw2.png)

# 可读性至关重要

任何编程过一段时间并不得不处理别人代码的人都知道这一点(特别是当这个人是你自己，六个月前)。试图理解一堆看似无辜的代码行可能会令人沮丧，但最终会想到“这个人当时在想什么？”。

我遇到过一个与下面类似的场景，我设法在代码中暴露了它晦涩的业务规则，而没有使用注释。你看，我强烈反对使用注释。对我来说，它们应该作为最后的手段，可维护性是主要的论点。我们都知道注释很容易与代码不同步。

# 场景描述

假设您有一个基于订阅的业务，您的客户注册不同的计划，可以按月或按年计费。这个系统已经运行了几年了，你需要在更新前六个小时对订阅进行批处理。但这不应适用于所有计划，您需要排除“Partner Plus”计划的订阅，包括年度和月度重复。

起初，这似乎很简单，但事实是在过去的几年里，事情发展得相当自由，你没有一个简单的 ID 用于每个计划。事实上，什么是“合作伙伴加”计划的整个概念取决于不同的事情。该计划的名称应该有“合作伙伴”字样，但不仅限于此:“合作伙伴敏捷博览会 15”、“合作伙伴加”、“新合作伙伴加”等。此外，价格是可变的，但至少总是大于每月 249.00 美元和每年 2490.00 美元。

# 编写代码

考虑到这一点，您可能会得到类似这样的代码(这是 Django 应用程序的 Python 代码):

```
 subscriptions = Subscription.objects.filter(
        period_end__lte=datetime.now(timezone.utc) + timedelta(hours=6),
        active=True,
    ).exclude(
        "plan__plan_id__contains": "partner",
        "plan__recurrence": "MONTHLY",
        "plan__price__gt": Decimal('249.00')
    ).exclude(
        "plan__plan_id__contains": "partner",
        "plan__recurrence": "ANNUALLY",
        "plan__price__gt": Decimal('2490.00')
    ) 
```

Enter fullscreen mode Exit fullscreen mode

根据我之前的描述，这段代码很好地完成了工作。它预测未来六个小时，并排除符合 Partner Plus 定义的计划，对吗？

但是，如果六个月后一个新的团队成员被指派去修改这个代码呢？她能理解**为什么**要进行排除吗？什么样的对象被排除在外？她很可能需要四处打听一下。

但是现在看看这段重构后的代码:

```
 next_6_hours = datetime.now(timezone.utc) + timedelta(hours=6)

    partner_plus_monthly = {
        "plan__plan_id__contains": "partner",
        "plan__recurrence": "MONTHLY",
        "plan__price__gt": Decimal('249.00')
    }
    partner_plus_annually = {
        "plan__plan_id__contains": "partner",
        "plan__recurrence": "ANNUALLY",
        "plan__price__gt": Decimal('2490.00')
    }

    subscriptions = Subscription.objects.filter(
        period_end__lte=next_6_hours,
        active=True,
    ).exclude(
        **partner_plus_monthly
    ).exclude(
        **partner_plus_annually
    ) 
```

Enter fullscreen mode Exit fullscreen mode

现在，变量包含了上下文，即使新团队成员没有完全理解，至少她在询问时可以了解更多信息，她可以从“为什么我们需要从该查询中排除 partner plus 计划？”。

可读性驱动的重构的问题是，由于看似“低影响”，它们往往会被忽略，因为，你知道，“这几乎是相同的代码”，“团队中的每个人都知道这意味着什么”，“一个注释行就可以做到”，等等。

对此，我要说:挑战你自己！写作是关于交流的。不是写散文就是写代码！

# 一个人去很危险，带上这个

在你离开之前，我将把下面的链接留在这里(在我以后所有的可读性文章中),因为这是一篇开启了我对这个问题的思考的文章，我会时不时地回到它上面: [FunctionLength，作者 Martin Fowler](https://martinfowler.com/bliki/FunctionLength.html)
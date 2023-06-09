# 数据库视图实际上并不存在

> 原文：<https://dev.to/thatjoemoore/database-views-dont-exist-4ho7>

我一直对数据库视图有误解，即使在与相当有经验的开发人员交谈时也是如此。人们似乎认为，在关系数据库中，视图是实际存在的东西。他们没有！

现在，让我解释一下。显然，当你创建一个视图时，某些东西是存在的。但是许多开发人员似乎误解了它的存在。在试图解决性能问题时，我经常会遇到这种误解——“嘿，如果您的连接如此糟糕和缓慢，为什么不创建一个视图呢！这将使您的 SQL 更快，没有连接或任何东西！”

当我说视图不存在时，我的意思是，在任何给定的时间，没有存储的数据说“这是这个视图中的内容！”视图不是在基础表更新时不断更新的表。

# 那么什么是视图，真的吗？

视图是存储的查询。当您创建数据库视图时，数据库会存储您提供给它的 SQL。然后，当您来查询该视图时，数据库获取存储的视图查询，添加针对该视图的额外查询，并执行它。就是这样！本质上，视图只是一个自动的查询重写器，让您编写比其他方式更简单的 SQL。这有点像写一个函数来隐藏一堆可重用的讨厌代码。这些代码仍然会被执行，只是被隐藏在一个更简单、可重用的外表后面。

# 请举例！

[![Money, Please! from Parks and Recreation](img/107d5e53203f1dddba042342cf3c1761.png)T2】](https://i.giphy.com/media/A7WK7FddTxKfu/giphy.gif)

假设您有两个闪亮的表，汽车和司机，它们有多对多的关系:

[![Cars and Drivers have a many-to-many relationship](img/06714f87f93f3ae5a3f8a5935e2daf79.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KTsOlaTe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/debr0zf279ql47d03jbq.png)

人可以开很多车，一辆车可以被很多人开。

现在，假设我需要能够查询每个开蓝色丰田车的人的名字。我的 SQL 可能会是什么样子？

```
select distinct(d.name) from car_drivers cd
left join car c on c.vin = cd.vin
left join driver d on d.driver_id = cd.driver_id
where c.color = 'blue' and c.make = 'toyota'; 
```

Enter fullscreen mode Exit fullscreen mode

(这不是这个查询的最佳版本，但是我们很快就会谈到它。)

虽然这是一个微不足道的例子，但仍然有很多事情要做好。在现实世界的例子中，这些连接可能会很快失控。

因此，让我们创建一个视图，这样我们的查询就可以更简单！

```
create view cars_and_drivers_vw as
select c.vin, c.color, c.make, c.model, c.year,
       d.driver_id, d.name, d.birthday
from car_drivers cd
left join car c on c.vin = cd.vin
left join driver d on d.driver_id = cd.driver_id; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我的查询变成:

```
select distinct(name) from cars_and_drivers_vw
where color = 'blue' and make = 'toyota'; 
```

Enter fullscreen mode Exit fullscreen mode

不错！简单！漂亮！

## 但是到底发生了什么？

[![Don't Look](img/d2b73ecf118e9abe553ce5d43fdc2193.png)T2】](https://i.giphy.com/media/OhmsK4DDdWsE/giphy.gif)

当我们在 Oracle 数据库中设置所有这些并要求 Oracle 显示我们的简单查询的执行计划时，会发生以下情况:

[![Oracle execution plan - I'm truly sorry I can't write a good alt-text for this](img/d8743e520c383de03ca5524df21a42e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KMJFeoQI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v6kovvu95sru4vrrvfgo.png)

如果你不知道你在看什么，不要害怕！这就是我来这里的原因！

这向我们展示的是，甲骨文正在采取这种:

```
select distinct(name) from cars_and_drivers_vw
where color = 'blue' and make = 'toyota'; 
```

Enter fullscreen mode Exit fullscreen mode

然后把它变成这样:

```
select distinct(d.name) from car_drivers cd
left join car c on c.vin = cd.vin
left join driver d on d.driver_id = cd.driver_id
where c.color = 'blue' and c.make = 'toyota'; 
```

Enter fullscreen mode Exit fullscreen mode

*(咦，那看起来好眼熟！)*

不错！

# 别跟我说观点比较快！

[![Barney Stinson - Stop It!](img/b372b9744dc6430aa51dcba94cf24746.png)T2】](https://i.giphy.com/media/3BL45MJDZABqg/giphy.gif)

现在我们来看看让我写这篇文章的原因。视图不会提高性能！在我团队的一个更复杂的应用程序中，我们有一些非常大的表，它们有复杂的关系，甚至需要对它们执行更复杂的查询。每当我们遇到一个缓慢的查询时，有人建议我们可以用视图来解决这个缓慢的问题。通过创建一个视图来完成我们所有的连接，我们可以神奇地使我们的查询更快！然而，如果你意识到视图只是一个花哨的查询重写器，而不是一个持久的数据结构，你就知道这不是真的。使用视图不会比原始查询更快；事实上，这很容易让事情变得更糟。性能问题的答案不是视图；这通常是一个反复试验的过程:找到产生相同结果的不同查询，分析查询执行计划，并找到好的索引来帮助数据库更有效地查询。

作为一个可能比我们一直使用的慢速查询更有效的查询示例，这里有一个与我们的第一个查询等效的查询，但在大型数据集上可能更有效。

```
select d.name from driver d
where exists (
    select 1 from car_drivers cd 
    where cd.driver_id = d.driver_id 
    and exists (
        select 1 from car c
        where c.vin = cd.vin 
        and c.color = 'blue' 
        and c.make = 'toyota'
    )
); 
```

Enter fullscreen mode Exit fullscreen mode

# 物化视图呢？

[![Star Trek Beaming In](img/7be6823edc544d37eb026085b47294b4.png)T2】](https://i.giphy.com/media/13dOKCrLjkEb3W/giphy.gif)

你一定要提起这些，不是吗？

物化视图是 Oracle 和 Postgres(以及其他一些较小的引擎)中存在的一个概念。这个术语实际上是用词不当，Oracle 已经用“快照”代替了“物化视图”这个名称，因为它更准确地描述了正在发生的事情。

当您创建一个~~实体化视图~~快照时，数据库会立即运行您指定的查询，然后存储结果。重要的是，随着原始表的变化，物化视图的内容会发生**而不是**变化。每当您想要更新内容时，都必须手动刷新视图，这可能是一个缓慢的操作。因此，物化视图速度很快，但很快就会过时。它们主要用于非实时分析和分析，在这种情况下，您需要定期刷新视图，然后对其执行大量查询。

这种情况的一个例外(因为在 SQL 数据库中总是有一个)是 Microsoft SQL Server，它允许您创建一个“索引视图”，它类似于 Oracle 快照，只是对基础表的更新反映在视图中。然而，这也带来了对可用于构建视图的查询的限制。

# 后记

所以，很明显，我们没有使用视图来解决我们最近的性能问题。事实上，应用程序的最初版本使用视图进行这些查询，我们去掉了它们，因为它们实际上使我们的许多查询慢了很多。我们也不能用索引解决我们的具体问题，因为查询中最复杂的部分被分割在两个表中🤦。

相反，我们利用我们已经内置到应用程序中的一些机制来实现一小部分[CQRS](https://martinfowler.com/bliki/CQRS.html)——我们创建了一个小表来保存我们常见查询中重要的、难以优化的部分。我们在 DB 层中已经有了钩子，允许我们运行额外的更新，作为对主表更新的副作用，所以我们利用这一点来确保我们的查询表总是最新的。这允许我们添加一些非常好的索引，我们的一个查询从花费大约 20 秒到花费大约 0.15 秒。
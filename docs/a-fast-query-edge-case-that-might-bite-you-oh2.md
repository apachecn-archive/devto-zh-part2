# 一个快速查询边缘案例可能会咬你一口

> 原文：<https://dev.to/jermdavis/a-fast-query-edge-case-that-might-bite-you-oh2>

最近有一些关于[没有在你的网站代码](https://intothecloud.blog/2018/10/21/It-s-time-to-put-fast-query-to-rest/)中使用“快速查询”的讨论(我甚至可以说是咆哮 [![😉](img/b26450942c7c42752fe0b02f126abb48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fumfYCPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f609.png) )。我是这个想法的支持者——但我最近遇到了一个问题，它指出了为什么不总是很容易确信你没有间接利用它…

所以，为了谷歌的利益:

## 问题:

我正在和一个客户一起工作，他的生产网站使用了 Sitecore v8.2、[发布服务](https://dev.sitecore.net/Downloads/Sitecore_Publishing_Service.aspx)和[商务连接](https://doc.sitecore.net/sitecore_commerce/commerce_connect_components/commerce_connect_framework_components)的组合。他们有一个非常大的内容树，他们最近一直在讨论 Sitecore 支持的一些发布性能挑战。讨论的结果是支持人员指出他们已经在发布配置中启用了“[更新后代表](https://kb.sitecore.net/articles/740847)”。即使您发布了一个页面，该操作(每次发布后重建后代表)也要花费大约五分钟的时间——并且是他们看到的大部分延迟的原因。

在站点日志中发现此问题后，支持人员建议禁用此操作。支持人员询问客户端是否有任何使用快速查询的代码，因为快速查询使用后代表来查找内容。但是我的客户很高兴他们的代码没有包含任何类似的查询。

这一改变有助于解决性能问题，并且在一段时间内一切都很好。然而，过了一会儿，客户在他们的公共网站上发布了一个新的“商店”,并立即注意到，当您试图将产品添加到您的购物篮时，它就崩溃了。

经过一番挖掘，他们注意到 Commerce Connect 的`EaPlanProvider`类是他们问题的根源。在其新发布的网站上，它未能解决篮子操作的正确参与计划。在对这背后的代码进行一些反汇编和挖掘之后，我们可以看到这段代码中有两个操作会失败:

```
EngagementPlanItem engagementPlanItem = Tracker.DefinitionDatabase.Automation()
     .EngagementPlans[engagementPlanName]; 
```

在那里它试图找到约定计划的定义项，并且

```
EngagementPlanStateItem engagementPlanStateItem = engagementPlanItem.States[stateName]; 
```

试图在计划中找到正确的状态项。

在表面之下，这两行代码都调用了`Sitecore.Analytics.Data.Items.ItemRecords<TItemType>`对象的`GetName()`方法。当你看到它的内部结构时:

```
private TItemType FindByName(string name)
{
    Assert.ArgumentNotNull(name, "name");
    ItemUtil.AssertItemName(name);
    if (root != null)
    {
        string format = deep ? "fast://*[@@id = {0}]//*[@@name = {1}]" : "fast://*[@@id = {0}]/*[@@name = {1}]";
        string query = string.Format(format, EscapeQueryParameter(root.ID.Guid.ToString()), EscapeQueryParameter(name));
        Item[] array = root.Database.SelectItems(query);
        if (array != null && array.Length > 0)
        {
            Item[] array2 = array;
            foreach (Item item in array2)
            {
                if (templateFilter(item))
                {
                    return Wrap(item);
                }
            }
        }
    }
    return default(TItemType);
} 
```

它可以进行快速查询！

因此，客户的问题是，发布一个全新的商店也发布了与该商店配套的全新参与计划项目。由于发布服务现在被配置为不更新后代表，所以这个快速查询永远不会返回正确的项目，并且您会得到一个空引用异常…

## 后果……

由于发布服务在默认情况下禁用后代表更新，因此这似乎是一个也会影响其他 Commerce Connect 用户的问题。

因此，如果您在使用 Commerce Connect 和 publishing service 时遇到类似的问题，您需要考虑两种解决方法之一:

*   您可能需要更改“不更新后代”设置，以便在发布时更新此表。
*   或者，您可能需要记住在每次添加新的雇佣计划时手动更新后代表(通过 API 调用或控制面板 UI 提供的“清理数据库”操作)。

我向支持人员提出了关于这个问题的疑问，他们承认这是产品中的一个错误。希望在不久的将来我们能看到这个问题的解决方案。但与此同时，如果你有同样的问题，并想尽快解决，错误代码是 291129 和 291130。
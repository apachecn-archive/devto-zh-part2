# 查询乐趣

> 原文：<https://dev.to/bethanyhaubert/query-fun-1l54>

目前，我们正准备将客户的网站迁移到我们的新平台，这意味着安排升级和重新打印 DNS。为了方便大家，我们正在构建一个平台升级仪表板，按状态列出所有客户的站点升级。对于三种不同状态中的每一种，都有一个部分，这些部分中的站点按字母顺序列出。我负责编写查询。

这里有一个假想客户的网站:

```
{
    :id => 129,
    :name => "Shart Attack",
    :owner_id => 1
},
{
    :id => 234,
    :name => "Fart of Gold",
    :owner_id => 1
},
{
    :id => 245,
    :name => "Farty McFly",
    :owner_id => 1
},
{
    :id => 312,
    :name => "Fart Corner",
    :owner_id => 1
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是 PlatformUpgrades::SiteUpgrade 对象的列表

```
{
    :id => 1,
    :site_id => 234,
    :status => "scheduled"
},
{
    :id => 2,
    :site_id => 129,
    :status => "unscheduled"
},
{
    :id => 3,
    :site_id => 245,
    :status => "upgraded"
},
{
    :id => 4,
    :site_id => 312,
    :status => "scheduled"
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我想根据 PlatformUpgrades::SiteUpgrade 对象的状态*和*站点名称，以正确的顺序返回它们。最后，我创建了一个新类来设置和运行查询。

我也第一次直接使用了一点 Arel。这是一个帮助 Ruby 管理 SQL 的库。事实上，ActiveRecord 就是建立在这个框架上的。

您将看到我用用户和范围初始化了该类。目前这个查询只在一个地方使用，所以我将默认范围设置为*all*platform upgrades::site upgrades。我还做了一个定制的订购方法。

```
module PlatformUpgrades
    # Gathers SiteUpgrades for a user and orders them correctly (unscheduled, scheduled, upgraded)
    #
    # Examples:
    #
    #   PlatformUpgrades::SiteUpgradeQuery.new(user: user)
    #
    class OrderedSiteUpgradeQuery < Query
        # Public: Instantiates a new query.
        #
        # user - The user who owns the sites associated with the site upgrades.
        #        Include sites where they are collaborators or members
        # scope - The scope from which we base the query.
        def initialize(user:, scope: PlatformUpgrades::SiteUpgrade.all)
            @user = user
            @scope = scope
        end

                # Public: Runs a query
        #
        # Returns: An ActiveRecord::Relation 
        def find
            scope.joins(:site)
                 .where(sites: { id: user.all_sites.select(:id) })
                 .order(order_by_status)
                 .order(sites[:name].asc)
        end

        private

        attr_reader :user
        attr_reader :scope

        table_alias sites: Site, upgrades: PlatformUpgrades::SiteUpgrade

        def order_by_status
            Arel::Nodes::Case
                .new(upgrades[:status])
                .when("unscheduled").then(0)
                .when("scheduled").then(1)
                .else(2)
        end
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

所以现在当我运行`PlatformUpgrades::SiteUpgradeQuery.new(user: User.find(1)).find`时，我得到了我的 PlatformUpgrades:SiteUpgrade 对象，顺序正确:

```
{
    :id => 2,
    :site_id => 129,
    :status => "unscheduled"
},
{
    :id => 4,
    :site_id => 312,
    :status => "scheduled"
},
{
    :id => 1,
    :site_id => 234,
    :status => "scheduled"
},
{
    :id => 3,
    :site_id => 245,
    :status => "upgraded"
} 
```

Enter fullscreen mode Exit fullscreen mode
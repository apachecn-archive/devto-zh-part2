# 覆盖保存更改–EF 数据库优先–设置属性

> 原文：<https://dev.to/jesperreiche/override-savechanges--ef-database-first--set-properties-58kn>

实体框架可以是福也可以是祸——尤其是 EF 数据库第一。对于初创公司和小型的零负债项目来说，它真的创造了奇迹。你可以在很短的时间内完成这么多工作。但是一旦你不得不在公司结构和公司安全的约束下工作，那么你很快就会遇到它的限制。或者也许我只是非常不幸，因为每当我在大型企业环境中参与使用实体框架的项目时，我都不得不寻找特殊的解决方法。

EF database first 通常在程序不允许代码自动更新数据库时使用，或者在数据库和围绕数据库的发布程序已经就绪时使用。也许这个项目甚至有独特的数据库管理员。或者我应该换个说法。当选择 EF 数据库第一，然后最好有一个熟练的独特的数据库管理员。如果不是，那么你是在赌你的开发人员会成为比微软的整个 SQL 部门更好的 SQL 管理员。

通常 EF 数据库第一意味着你要用数据库给你的东西工作。你不能在保存之前对你的实体进行一般的处理。但在某些情况下，它甚至不这样做。例如，您希望通过(getdate())给出默认值的 Datetime 字段。这些都是英孚数据库先公然忽略的。如果您插入记录 vi MSSQL，它将遵守并插入默认值。但是如果从代码中插入，它只会插入日期时间。在你有规则的任何领域中的最小值。

这可以通过从 edmx 设计器中手动选择属性并将 StoreGeneratedPattern 更改为 Compute 来纠正。只要您仅从数据库更新模型，就会保存该值。但是，任何先使用过 EF database 和 edmx 的人都知道——什么时候——是的，这只是个时间问题——你的模型坏了，你无法修复它，最后的办法是从设计器中删除每个模型，然后从头再添加它们。然后突然之间，您不得不记住手动更新每个具有默认值的列，以符合您的数据库规则。不好看！并且非常容易出错。不好！

然后，因为从 edmx 创建的所有实体都是分部类，所以您可以创建一个分部类的伪集合来设置您的计算字段。但是这仍然感觉创建和维护都很麻烦——而且将来还容易出错。

你想要的是一个每次都遵守什么规则的解决方案。无论是现在还是未来。对于我的项目来说，这是一个所有表上都必须有的更新时间。团队的所有成员，甚至是架构师，实际上都已经确定了手动过程。从而让每个开发人员记住手动设置他们实体的更新时间——这是我绝对不喜欢的。

经过一番搜索，我发现绝对没有可行的解决方案。每个人都提出了另一组分部类的建议，这些分部类在构造函数中设置任何需要设置的属性。但是如上所述，它比手动设置稍微好一点，但也好不了多少。

因此，经过一点黑客攻击，我想出了下面的解决方案。这正是我想要的。它自动化了一些非常适合自动化的东西。关上未来潜在错误的大门。当然，您仍然需要遵守属性的命名标准，这样才能工作。但是这在 sql 更改的代码审查中比在代码中实体的更改更容易发现，在代码中您应该记住这个实体是否有更新时间。

```
 /// <summary>
 /// Partial class used to override SaveChanges
 /// </summary>
 public partial class MyDb : DbContext
 {
     /// <summary>
     /// As entity framework ignores default values as (getutcnow()) we need to manually set these from code.
     /// Therefore we have an override to the SaveChanges method that automatically checks for a property named UpdateTime
     /// And if it is found then it will set it to DateTime.UtcNow.
     ///
     /// Then we avoid each developer having to remember to manually put it in for each table/entity
     /// </summary>
     /// <returns></returns>
     public override int SaveChanges()
     {
         // Get entities that are either added or modified
         var changedEntities = ChangeTracker.Entries().Where(e => e.State == EntityState.Added || e.State == EntityState.Modified).ToList();

         // Run through entities and look for a property named UpdateTime
         changedEntities.ForEach(e =>
         {
             // Tro to get property "UpdateTime"
             var updTimeProperty = e.Entity.GetType().GetProperty("UpdateTime");
             // If the entity has this property then set it
             if (updTimeProperty != null)
             {
                 // Set to UTC now
                 updTimeProperty.SetValue(e.Entity, DateTime.UtcNow);
             }
         });

         return base.SaveChanges();
     }

 } 
```
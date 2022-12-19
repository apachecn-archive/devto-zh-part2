# 使用λ表达式的 LINQ 独特方法

> 原文：<https://dev.to/sebnilsson/linq-distinct-method-using-lambda-expression-5326>

如果你曾经想过滤一个集合得到一个独特的结果，你可能知道 LINQ 的扩展方法`.Distinct`。它对于包含容易比较的对象的简单数据结构很有用，比如我收集的`strings`或`ints`，但是对于更复杂的场景，你需要传入一个`IEqualityComparer`。这样不太方便。

更方便的是能够传入一个 Lambda 表达式，指定您想要通过哪个字段进行区分，就像这样:

```
var distinctItems = items.Distinct(x => x.Id); 
```

为此，您可以**将以下扩展方法添加到您的项目中** :

```
public static IQueryable<TSource> Distinct<TSource>(
    this IQueryable<TSource> source, Expression<Func<TSource, object>> predicate)
{
    // TODO: Null-check arguments
    return from item in source.GroupBy(predicate) select item.First();
}

public static IEnumerable<TSource> Distinct<TSource>(
    this IEnumerable<TSource> source, Func<TSource, object> predicate)
{
    // TODO: Null-check arguments
    return from item in source.GroupBy(predicate) select item.First();
} 
```

使用`IQueryable<T>`的扩展方法适用于像 Entity Framework 这样的 ORM，而`IEnumerable<T>`适用于所有类型的集合，内存中的或其他的，这取决于实现。

_ **警告** : **避免在 EF Core 版本 1.x 或 2.0** 中使用，因为`.GroupBy`-执行总是在内存中进行。因此，您可能会将数据库的全部内容加载到内存中。[仅在生产场景中使用 EF Core 2.1 及以上版本](https://blogs.msdn.microsoft.com/dotnet/2018/05/30/announcing-entity-framework-core-2-1/)。_
# 如何在 Moq 中设置返回内置类型的方法并实现 IEnumerable 接口？

> 原文：<https://dev.to/rajmondburgaj/how-to-setup-methods-which-return-a-built-int-type-and-implement-ienumerable-interface-in-moq--20of>

最近我试图做一些单元测试，在某个时候我意识到我的设计有问题，但同时又不能改变它。所以，我的想法是使用实体框架，并使用这个框架执行存储过程。所有这些过程都封装在一个名为`IProcedureService`的服务中。所以，一个普通的方法应该是这样的:

```
DbRawSqlQuery<UserVesselPermissionsResult> GetUserVesselPermissions(Guid userId, DateTime date); 
```

`DbRawSqlQuery`是来自实体框架的一个类，我不能修改它。我的问题是，我如何让这个`Moq`设置代码工作:

```
_procedureService.Setup(x => x.GetUserVesselPermissions(It.IsAny<Guid>(), It.IsAny<DateTime>()))
        .Returns((DbRawSqlQuery<UserVesselPermissionsResult>) /*...what*/); 
```

真正的问题是由于类本身只有一个内部构造函数，不能在`Moq`中实例化。那是因为`Moq`不能只用内部构造函数实例化类。此外，这个类可能有带一些参数的构造函数，我现在还不能提供这些参数(*一个例子可能是 DbConnection 或类似的东西*)。在这种情况下，我试图反编译源代码，我可以看到另一个名为`DbSqlQuery`的类，它继承自`DbRawSqlQuery`，除了内部构造函数之外，还提供了一个普通的构造函数。在这一点上，我发现了一个想法，如何使这一工作。这个想法是创造一个新的阶级，并从`DbSqlQuery`继承。我的类现在应该是这样的:

```
public class TestDbSqlQuery<T> : DbSqlQuery<T> where T : class
{
    private readonly List<T> _innerList; 

    public TestDbSqlQuery(List<T> innerList)
    {
        _innerList = innerList;
    }

    public override IEnumerator<T> GetEnumerator()
    {
        return _innerList.GetEnumerator();
    }
} 
```

正如你可以从代码中看到的那样，我已经实现了两件事:

1.  我确实有一个继承自`DbRawSqlQuery`的类，这是我所需要的。
2.  其次，我有一个可以实例化的类，并且在构造函数中接受一个参数，然后可以在内部欺骗这个参数。

我说的内部欺骗是指:-因为我的方法都返回了`DbRawSqlQuery`，它最后只是一个`IEnumerable`类型，那么我认为所有这些都将执行`GetEnumerator()`方法来获得结果。此时，我已经在构造函数中提供了一个列表作为参数，从这里我可以像上面一样通过覆盖`GetEnumerator()`方法轻松地返回所有元素。我有一个`List<>`作为参数，但这可以很容易地切换到`IEnumerable`或任何支持`GetEnumerator()`方法的类型。

现在，我的测试方法设置如下所示:

```
_procedureService.Setup(x => x.GetUserVesselPermissions(It.IsAny<Guid>(), It.IsAny<DateTime>()))
.Returns(new TestDbSqlQuery<UserVesselPermissionsResult>(new List<UserVesselPermissionsResult>
{
    new UserVesselPermissionsResult
    {
        PermissionId = 1
    }
})); 
```

最后我会得到预期的结果。

希望这个想法可以帮助那些努力设置返回`IEnumerable`类型的方法的人。
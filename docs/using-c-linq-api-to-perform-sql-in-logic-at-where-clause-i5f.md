# 使用 C# LINQ API 在 WHERE 子句中执行 SQL IN 逻辑

> 原文：<https://dev.to/alexruzenhack/using-c-linq-api-to-perform-sql-in-logic-at-where-clause-i5f>

我正与`User`和`Costumer`identifica o 一起做一个项目。第一个是由 web 应用程序生成的，第二个是由业务部门生成的。

所以我必须开发一个逻辑来使这些标识匹配。

`User`实体需要知道`Costumer`标识，为了进行匹配，首先我选择了没有客户的用户，并提取了一个电子邮件列表。然后，我根据这个邮件列表选择客户。这个语句可以转录成 SQL 语法，像这样:

```
SELECT *
FROM Customers
WHERE email IN (SELECT email FROM Users WHERE CustomerId IS NULL) 
```

Enter fullscreen mode Exit fullscreen mode

但是如何将 SQL 翻译成 C# LINQ API 呢？

1.  首先，从用户中进行选择，提取邮件列表
2.  选择有这些电子邮件的客户

这边:

```
var emails = _userManager.Users
    .Where(user => user.CustomerId == null)
    .Select(user => user.Email) // extract the emails from users
    .ToList();

var customers = _applicationRepository.GetCustomers()
    .Where(customer => emails.Contains(customer.Email)) // the Contains method carry the IN logic when translated to SQL script
    .ToList(); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了程序化的匹配。

就是这样！

### 引用

*   [使用 LINQ 执行“WHERE IN (Value1，Value2)”查询](https://weblogs.asp.net/dwahlin/using-linq-to-perform-quot-where-in-value1-value2-quot-queries)
*   [IN(Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/in-transact-sql?view=sql-server-2017)
*   [可查询的。包含方法](https://docs.microsoft.com/pt-br/dotnet/api/system.linq.queryable.contains?view=netframework-4.7.2)
*   [LINQPad - The。NET 程序员的游乐场](https://www.linqpad.net/)
# ASP.Net 清洁建筑

> 原文：<https://dev.to/rnowif/aspnet-clean-architecture-4eap>

当创建一个新项目时，设计一个干净、连贯和模块化的架构总是一个挑战。有一些指导方针可以帮助我们实现这个目标，但是实现起来并不总是那么简单。在这篇博文中，我将在 ASP.Net 的一个项目中提出 Bob 叔叔的[清洁架构](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)的实现。这个项目的源代码可以在我的 [GitHub](https://github.com/rnowif/Expenses) 上找到。

干净架构的主要思想是减少核心业务代码和外部世界(Web、数据库、框架)之间的耦合。为了做到这一点，该项目可以分为 3 个主要模块，将描述如下:`Domain`、`Web`和`Data`。

## 域

这个模块包含了所有的核心业务代码。它不依赖于除了。NET SDK 并包含子模块。

### `Entities`

它们是领域的构建块，封装了业务概念。实体不与任何 ORM 框架耦合。事实上，领域模型可以与数据库模型完全不同！

### `Use Cases`

按照 Bob 叔叔的定义，它们包含特定于应用程序的业务规则，协调实体之间的数据流，并实现更高级别的业务规则。

理解领域模型不应该泄漏到这个模块之外是很重要的。为了做到这一点，用例不应该把实体作为它们方法的参数，而是一个原始参数的列表。例如，提交新费用的用例如下:

```
public interface ISubmitExpense
{
    void Execute(Guid userId, string description, long priceWithoutTax, long priceIncludingTax);
} 
```

它负责构建一个`Expense`对象并应用相关的业务逻辑。

### `Repositories`

在领域模块中，存储库只是用例用来访问数据的接口，而不需要任何具体实现的知识:数据可以从数据库、文件或外部 Web APIs 中检索，它根本不应该影响核心业务逻辑。为了加强这一点，存储库将实体作为参数，并返回实体。如果需要，处理转换是具体实现的责任。

## 数据

数据模块包含数据库和 ORM 配置以及存储库实现。通常，我们会在这个模块中找到 EntityFramework 配置，以及将用数据库条目映射的带注释的类。存储库实现负责转换领域对象中的“数据库对象”:

```
public void Create(Expense expense)
{
  // Convert the domain object "Expense" into a database object "DbExpense"
  _dbContext.Expenses.Add(DbExpense.FromExpense(expense));
  _dbContext.SaveChanges();
} 
```

这可以防止 ORM 框架泄漏到域中。

## 网页

web 模块包含所有控制器。它负责处理 HTTP 请求，将 JSON 或 XML 有效负载转换为对象，并调用用例:

```
[HttpPost]
public void SubmitExpense([FromBody] SubmitExpenseCommand expense)
{
  // The controller just invoke the use case with data extracted from the body of the HTTP request
  _submitExpense.Execute(expense.UserId, expense.Description, expense.PriceWithoutTax, expense.PriceIncludingTax);
} 
```

它不应该包含任何业务逻辑。因此，控制器非常轻便，易于测试。

## 依赖注入

为了实现模块间的低耦合，接口被注入到不同类的构造函数中:

```
public class SubmitExpense : ISubmitExpense
{

  private readonly IExpenseRepository _repository;

  public SubmitExpense(IExpenseRepository repository)
  {
    _repository = repository;
  }
} 
```

管道由`Startup.cs`类处理，其中声明了所有接口的实现:

```
public void ConfigureServices(IServiceCollection services)
{
  // ...

  // Register "SubmitExpense" as the implementation of "ISubmitExpense"
  services.AddTransient<ISubmitExpense, SubmitExpense>();
  services.AddTransient<IExpenseRepository, ExpenseRepository>();
} 
```

## 结论

在这种架构中，重点放在领域上。每个其他模块应该适应它，但它不依赖于任何东西。这导致轻量级的类非常容易理解和测试。

此外，低耦合使得改变基础设施非常容易。事实上，如果我们决定使用 CLI 而不是 Web 应用程序，或者如果数据应该从外部 Web API 而不是 PostgreSQL 数据库中检索，域模块不会有任何变化。
# 存储库模式熟悉 Awesome 存储库模式

> 原文：<https://dev.to/kylegalbraith/getting-familiar-with-the-awesome-repository-pattern--1ao3>

存储库模式是另一种抽象，就像计算机科学中的大多数东西一样。这是一种适用于许多不同语言的模式。事实上，许多开发人员使用存储库模式，甚至没有意识到这一点。

在这篇文章中，我将转换一段代码。我们从从数据库加载单个记录的一段代码开始。一旦获取了记录，就将其返回给调用者。让我们来看看一些代码。

### 需要改进

我们从数据库中加载的记录是`PersonModel`。

```
public class PersonModel
{
    public string Name { get; set; }
    public int Age { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

从数据库中加载一个人的服务是`ICompanyLogic`。它由以下方法定义组成。

```
public interface ICompanyLogic
{
    PersonModel GetPersonByName(string name);
} 
```

Enter fullscreen mode Exit fullscreen mode

`ICompanyLogic`的实现由`CompanyLogic`处理。

```
public class CompanyLogic: ICompanyLogic
{
    private IPersonDataContext _personDataContext;
    public PersonService(IPersonDataContext personDataContext)
    {
        _personDataContext= personDataContext;
    }

    public PersonModel GetPersonByName(string name)
    {
        using(var ctx = _personDataContext.NewContext())
        {
            var person = ctx.People.First(p => p.Name.Equals(name));
            return person;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，这还不算太糟。我们有一个业务服务`CompanyLogic`,可以从数据库中检索一个人。

但是我们有了一个新的需求，即我们还需要一种从另一个数据库加载公司的方法。所以我们需要添加一个新方法并扩展`CompanyLogic`。

`CompanyModel`代表存储在公司数据库中的型号。

```
public class CompanyModel
{
    public string Name { get; set; }
    public int Size { get; set; }
    public bool Public { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们扩展了`CompanyLogic`,使其具有一个按名称返回公司的方法。

```
public class CompanyLogic: ICompanyLogic
{
    private IPersonDataContext _personDataContext;
    private ICompanyDataContext _companyDataContext;
    public PersonService(IPersonDataContext personDataContext, 
                         ICompanyDataContext companyDataContext)
    {
        _personDataContext= personDataContext;
        _companyDataContext = companyDataContext;
    }

    public PersonModel GetPersonByName(string name)
    {
        using(var ctx = _personDataContext.NewContext())
        {
            var person = ctx.People.First(p => p.Name.Equals(name));
            return person;
        }
    }

    public CompanyModel GetCompanyByName(string companyName)
    {
        using(var ctx = _companyDataContext.NewContext())
        {
            var person = ctx.Company.First(c => c.Name.Equals(companyName));
            return person;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们开始看到这个初始解决方案的问题。这里有一个不理想的事情的简短列表。

*   知道如何访问两个不同的数据库。
*   我们用我们的`using`语句复制了代码。
*   我们的逻辑知道人和公司是如何存储的。
*   `GetPersonByName`和`GetCompanyByName`不能在没有全部`CompanyLogic`的情况下重复使用。

除了所有这些事情，我们如何在当前状态下测试`CompanyLogic`?我们必须模拟数据环境，让个人和公司拥有文字数据库记录。这是可能做到的。但是我们的努力工作应该是测试我们的逻辑，而不是嘲笑数据库对象。

### 实现存储库模式

存储库模式在数据访问之上添加了一个抽象层。
一点点的抽象可以走很长的路。使用存储库模式，我们可以添加一个抽象薄层来访问人员和公司数据库。然后`CompanyLogic`或任何其他逻辑可以利用这些抽象。

让我们从创建我们的`IPersonRepository`接口及其伴随的实现开始。

```
public interface IPersonRepository
{
    PersonModel GetPersonByName(string name);
}

public class PersonRepository: IPersonRepository
{
    private IPersonDataContext _personDataContext;
    public PersonRepository(IPersonDataContext personDataContext)
    {
        _personDataContext= personDataContext;
    }

    public PersonModel GetPersonByName(string name)
    {
        using(var ctx = _personDataContext.NewContext())
        {
            return ctx.People.First(p => p.Name.Equals(name));
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以为公司做一些非常相似的事情。我们可以创建`ICompanyRepository`接口及其实现。

```
public interface ICompanyRepository
{
    PersonModel GetCompanyByName(string name);
}

public class CompanyRepository: ICompanyRepository
{
    private ICompanyDataContext _companyDataContext;
    public CompanyRepository(ICompanyDataContextcompanyDataContext)
    {
        _companyDataContext= personDataContext;
    }

    public CompanyModel GetCompanyByName(string name)
    {
        using(var ctx = _companyDataContext.NewContext())
        {
            return ctx.Company.First(p => p.Name.Equals(name));
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有两个独立的存储库。知道如何从人员数据库中加载给定人员的姓名。`CompanyRepository`可以从公司数据库中按名称加载公司。现在让我们重构`CompanyLogic`来利用这些存储库，而不是数据上下文。

```
public class CompanyLogic: ICompanyLogic
{
    private IPersonRepository _personRepo;
    private ICompanyRepository _companyRepo;
    public PersonService(IPersonRepository personRepo, 
                         ICompanyRepository companyRepo)
    {
        _personRepo= personRepo;
        _companyRepo= companyRepo;
    }

    public PersonModel GetPersonByName(string name)
    {
        return _personRepo.GetPersonByName(name);
    }

    public CompanyModel GetCompanyByName(string companyName)
    {
        return _companyRepo.GetCompanyByName(companyName);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

看，我们的逻辑层不再了解数据库。我们已经抽象出一个人和一个公司是如何被装载的。那么我们获得了什么好处呢？

*   存储库接口是可重用的。它们可以用于其他逻辑层，而不需要做任何改变。
*   测试简单多了。我们模拟接口响应，这样我们可以专注于测试我们的逻辑。
*   个人和公司的数据库访问代码在一个地方集中管理。
*   可以在存储库级别进行优化。接口已定义并达成一致。在存储库上工作的开发人员可以按照她认为合适的方式存储数据。

存储库模式为我们的数据提供了一个很好的抽象。这适用于各种语言。这个故事的寓意是数据访问应该是一个单一的责任接口。然后，可以将该接口注入业务层，以添加任何额外的逻辑。

### 饿着肚子学亚马逊 Web 服务？

有很多人渴望学习亚马逊网络服务。受到这个事实的启发，我创建了一个课程，专注于通过使用它来学习 Amazon Web Services。关注静态网站的托管、保护和交付问题。通过构建问题的解决方案，您可以学习 S3、API Gateway、CloudFront、Lambda 和 WAF 等服务。

AWS 周围有大量的信息。很容易迷失方向，在学习上没有任何进步。通过解决这个问题，我们可以简化信息，加快你的学习。我用这本书和视频课程的目的是与你分享我所学到的。

听起来有趣吗？查看登录页面了解更多信息，并选择适合您的套餐，[此处](https://www.kylegalbraith.com/learn-aws/)。
# 不要成为一个依赖的懒汉！编写更负责任的代码的三种方法

> 原文：<https://dev.to/makingloops/stop-being-a-dependency-slob-three-ways-to-write-more-responsible-code-36je>

你是一个依赖型懒汉吗？

跟踪用户界面*中的油泥*遍布你的业务逻辑层的抛光木地板？

我已经做到了。

我们急于满足最后期限，以狂野的西部风格编写代码，毫不羞耻地用`new`关键字硬编码对象实例化，将一些日志记录代码放入我们以前的实体方法中，或者通过要求类实现作为参数来强制依赖我们的类的客户端。

只要有用，对吗？

这篇文章标志着我们在《通过 C#进行自适应编码》一书中的下一步。第 2 章:依赖和分层的目标是向我们展示*为什么*当我们把应用程序的各个部分连接在一起时，我们应该更加小心和考虑，以及我们可以开始的一些方法。

我没有一段一段地总结每一章，而是从作者那里提取了 3 条有用的智慧，并把它们列成一个清单。

请记住，就像软件开发中的大多数建议一样，很少有一个时候你应该做某事，不管在任何情况下和*。接受每一个建议，在你的脑海里思考一下，想想什么时候有意义，什么时候没有意义。*

 *## 不跨层界限

依赖性的概念沿着从宏观到微观的范围移动。web 服务器可以依赖于数据库服务器，WinForms 应用程序依赖于 Windows OS，应用程序的组件可以由依赖于业务逻辑层和数据访问层的用户界面组成。这当然会延续到汇编、类、方法和变量依赖链。

宇宙就像一张巨大的依赖关系网，伙计…

现在，这个建议可能看起来显而易见，但它很关键，需要说出来。如果您(或您之前的开发人员)已经设计了一个由不同层组成的系统，那么就应该由您来负责将它们分离开来，这样您就可以从中获益。

你说有什么好处？

我们来举个例子。

假设你有一个三层的应用程序，一个 UI 层，一个业务逻辑层，一个数据访问层，你的 UI 层是由 ASP.NET Razor 页面或 WebForms 控件组成的。

您在业务逻辑层中编写了一个电子邮件地址格式验证方法，并且不假思索地将整个 WebForms `TextBox`控件传递给该方法，并让该方法处理实际文本的获取。这在当时对您来说很方便，但是现在您已经将实现细节(WebForms 平台)链接到您的业务逻辑层，使得它对不支持 WebForms 的平台不可用。

当然，更好的方法是将电子邮件地址作为普通的旧`string`传递。通过尽可能保持基本的参数类型，可以减少依赖性并提高可移植性。

## 通过在你的公共参数类型中加入同情心来避免“意大利面条式的潜力”

这与前一个技巧是一致的，在创建公共方法时要花些时间考虑它们未来的客户。

在本书中，作者使用了一个 API 方法来更改用户密码的例子。在这个例子中，`AccountsController` API 控制器依赖于`SecurityService`类来更改密码。`SecurityService`有一个`ChangePassword`方法，它将整个`User`对象作为参数。

```
public class AccountsController {

    ...

    [HttpPost]
    public void ChangePassword(int userID, string newPassword)
    {
        var userRepository = new UserRepository(); //yuck...
        var user = userRepository.GetByID(userID); 
        this.securityService.ChangePassword(user, newPassword); 
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

尽管这可能有逻辑上的意义，但问题是现在*每个想要使用该方法的*客户端必须知道如何创建`User`对象。注意上面的控制器方法是如何首先获得一个存储库并获得完整的用户对象*来调用* `ChangePassword` *方法*。

更糟糕的是，`User`类和存储库可能有它们自己的依赖集，所以你已经创建了我称之为*意大利面潜力*。现在，如果未来的开发人员发现自己急于实现密码更改功能，他们就有可能在各种程序集中使用依赖链。

这里更好的做法是让`SecurityService`自己创建`User`对象，并且只要求客户端传递用户的整数 ID。

```
public class AccountsController {

    ...

    [HttpPost]
    public void ChangePassword(int userID, string newPassword)
    {
        this.securityService.ChangePassword(userID, newPassword); 
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 通过使用方面来净化你的方法

术语*横切关注点*是指当你有一个方法，它与可能相关但最终是独立的任务交织在一起。最好的例子就是记录代码。

您已经有了一个更新用户电子邮件地址的方法，但是您还加入了一些审计代码来记录所做的更改，这样您就可以回过头来查看是谁在何时做的。

作者展示了一个很好的例子，用方法属性装饰器将日志记录代码(以及在事务中包装数据库调用所涉及的代码)转化为单独的方面。

替换这个:

```
public void UpdateEmail(int userID, string newEmail)
{
    Log.Write("user (ID:{0}) email changed to {1}", userID, newEmail);

    using (var transaction = session.BeginTransaction)
    {
        //code actually updating email.
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个:

```
[Log]
[Transactional]
public void UpdateEmail(int userID, string newEmail)
{
     //JUST code actually updating email.
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能更熟悉的另一个例子是。NET Web API。将你的模型验证分离到它自己的过滤器中，就像我的 [API 鸟瞰指南](https://purple.pizza/restful-api-visual/)中所示，保持代码干净清晰。

## 向前冲！

现在，您已经看到了一些我们在编码时可以更加敏锐和深思熟虑的方法，我知道下次当您在键盘后面时，您将会放慢速度，并仔细考虑您创建的依赖项。

你会尽可能地尊重层的边界，所以不要污染不同的组件。保持组件和层的松散耦合给我们带来了最好的机会，使未来的更改、可测试性和可重用性变得容易。

您将考虑您的公共方法的所有当前和未来的客户。什么参数类型会创建最少的依赖关系？你会强迫客户知道太多吗？

最后，你将留意*横切关注点*，寻找尽可能保持你的方法单一焦点的方法，以便它们能被其他人容易地理解(和测试)。*
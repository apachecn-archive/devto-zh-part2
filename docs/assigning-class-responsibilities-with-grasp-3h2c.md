# 准确分配班级责任

> 原文：<https://dev.to/bohdanstupak1/assigning-class-responsibilities-with-grasp-3h2c>

本文最初发表于 [Codeproject](https://www.codeproject.com/Tips/1229658/Method-can-be-made-static-may-hide-OO-design-flaw)

## 玩具举例

在代码库中漫游时，我遇到过一些 ReSharper 发出警告“方法可以变成静态的”的例子。尽管修复看起来很简单，但警告本身可能隐藏了与对象责任分配相关的更微妙的问题。如果你从未用过 ReSharper，请继续关注我。我希望你会发现很容易赶上。
我们来看看下面的代码

```
public class EmailConstructor
    {
        private const string Signature = "Regards";

        public string Construct(User recipient, string body)
        {
            var builder = new StringBuilder();
            builder.Append($"Hello {GetNiceUserName(recipient)}");
            builder.Append(Environment.NewLine);
            builder.Append(body);
            builder.Append(Environment.NewLine);
            builder.Append(Signature);
            return builder.ToString();
        }

        private string GetNiceUserName(User user)
        {
            return $"{user.Name}  {user.Surname}";
        }
    }

    public class User
    {
        public string Name { get; set; }
        public string Surname { get; set; }
    } 
```

Enter fullscreen mode Exit fullscreen mode

实际上，雷夏普发出了警告。
[![ReSharper warning](img/1edbcd1939e28b99bbb0b6336cc9f33e.png "ReSharper warning")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ywuXj0Ly--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codeproject.com/KB/tips/1229658/1.png)

## 特写羡慕

虽然乍一看事情似乎很明显，但实际上上面的代码是[特性羡慕](https://sourcemaking.com/refactoring/smells/feature-envy)的例子。作为帮助你在不使用 ReSharper 这样的工具的情况下发现这种情况的经验法则，你可以使用 Craig Larman 在他的书《应用 UML 和模式》中提出的原则之一。

> 信息专家将把责任放在拥有完成责任所需的最多信息的班级上。
> 根据这个原则，你可以将 warning my 的移动方法固定在`User`类中，如下所示，因为用户拥有更多的信息来以这样或那样的方式表示他的名字

```
public class EmailConstructor
    {
        private const string Signature = "Regards";

        public string Construct(User recipient, string body)
        {
            var builder = new StringBuilder();
            builder.Append($"Hello {recipient.GetNiceUserName()}");
            builder.Append(Environment.NewLine);
            builder.Append(body);
            builder.Append(Environment.NewLine);
            builder.Append(Signature);
            return builder.ToString();
        }
    }

    public class User
    {
        public string Name { get; set; }
        public string Surname { get; set; }

        public string GetNiceUserName()
        {
            return $"{Name}  {Surname}";
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 凝聚力高

很自然，问题从上面的例子中产生:“这会不会导致用户类膨胀，承担很多责任，例如，处理持久存储？”。答案是信息专家原则应该和高内聚原则一起使用。

> 高内聚通常用于支持低耦合。高内聚意味着一个给定元素的职责是紧密相关和高度集中的。将程序分成类和子系统是增加系统内聚性的活动的一个例子。或者，低内聚是一种给定元素有太多不相关职责的情况。内聚性低的元素通常难以理解、难以重用、难以维护并且不愿意改变。

因此，举例来说，如果我们想用持久存储交互来扩展我们的玩具代码，我们可能会提取高度内聚的函数，专门用于某种类型的[工作单元](https://docs.microsoft.com/en-us/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)模式。

## 结论

静态成员有一些缺点，比如它们使单元测试变得复杂。这就是为什么要小心考虑静电的原因。Craig Larman 结合 SOLID 的 GRASP 模式允许我们检查这样的情况，以便减少我们代码库的支持成本。
# 打着干的旗号

> 原文：<https://dev.to/janvanryswyck/all-in-the-name-of-dry-15n4>

不久前，我在做代码审查。在某个时候，我遇到了一些我经常看到的构造。当我描述和构建我的案例时，我想我也可以写一篇关于它的博客。我遇到的代码类似于下面的代码:

```
public interface IUser
{
    Int32 Id { get; }
    String FirstName { get; }
    String LastName { get; }
    Boolean IsValid { get; }
}

public class ValidUser : IUser
{
    public Int32 Id { get; }
    public String FirstName { get; }
    public String LastName { get; }
    public virtual Boolean IsValid => true;

    public ValidUser(Int32 id, String firstName, String lastName)
    {
        Id = id;
        FirstName = firstName;
        LastName = lastName;
    }
}

public class InvalidUser : ValidUser
{
    public override Boolean IsValid => false;

    public InvalidUser(int id)
        : base(id, string.Empty, string.Empty)
    {}
} 
```

这段代码有几个问题。我想提到的第一件事是，我不喜欢在接口上声明属性。但这不是我想在这篇博文中详细阐述的。真正让我感兴趣的是引导开发者做出这些设计选择的前提。而这个起始前提叫做干原理。

#### 干

DRY principle 是首字母缩写，代表“不要重复自己”。这一原则源于安德鲁·亨特和迪夫·托马斯写的优秀著作《实用程序员》。大约在同一时间，这个原则也在肯特·贝克所著的《极限编程的解释[》一书中有所陈述，被描述为“一次且仅一次规则”。](https://www.amazon.com/Extreme-Programming-Explained-Embrace-Change/dp/0201616416)

它指出:

在一个系统中，每一项知识都必须有一个单一的、明确的、权威的表述

不枯燥的代码在两个或更多地方表达了相同的算法(=知识)。如果你改变了一个，你必须记住在其他地方也要改变代码。对于执行这个变更的团队来说，一个新的开发人员可能不知道这些地方。因此，我们说这样的代码很难更改和维护。

DRY 原则是一个非常重要的设计规则，幸运的是，这一规则已经流行了很多年。据说，它通过将更改隔离到系统中必须更改的部分来促进代码的正交性。

#### 太干了

既然我们已经了解了 DRY 原则及其好处，让我们再来看看我们的代码示例。似乎有一个“有效用户”和“无效用户”的概念。当查看它们的实现时， *InvalidUser* 是从 *ValidUser* 类派生的。用面向对象的术语来说，当一个类从另一个类继承时，这基本上意味着一种“是”的关系正在建立。在我们的例子中，这导致一个“无效用户”也是一个“有效用户”，这至少可以说是有点可疑的。

我很确定这段代码是出于好意而写的。有问题的开发者想要坚持(过于)干燥的原则，以避免在*有效用户*和*无效用户*类上有相同的属性定义。但问题是。

在多个类上定义的同名属性不符合代码重复的条件！

所以我想知道为什么一些开发人员为了避免几个属性定义而大费周章，引入不必要的复杂性。回到我们的代码示例，我们注意到这种设计选择也有一些间接的后果。

通过从*有效用户*类派生*无效用户*类，我们基本上放弃了类型检查。我无法根据用户的类型来编写验证用户是否有效的代码。

```
IUser user = ... // Retrieve an instance of a user
if(user is ValidUser)
    Console.WriteLine("Woohoo, a valid user"); 
```

不管用户的类型如何，if 语句的条件将始终为真。这可能是添加了 *IsValid* 属性的原因。

```
IUser user = ... // Retrieve an instance of a user
if(user.IsValid)
    Console.WriteLine("Woohoo, a valid user"); 
```

#### 少干

在执行了一些重构之后，代码看起来稍微好了一点。

```
public interface IAmUser
{
    Int32 Id { get; }
    String FirstName { get; }
    String LastName { get; }
    Boolean IsValid { get; }
}

public class ValidUser : IAmUser
{
    public Int32 Id { get; }
    public String FirstName { get; }
    public String LastName { get; }
    public virtual Boolean IsValid => true;

    public ValidUser(Int32 id, String firstName, String lastName)
    {
        Id = id;
        FirstName = firstName;
        LastName = lastName;
    }

    public static IAmUser Invalidate(Int32 id)
    {
        return new InvalidUser(id);
    }
}

public class InvalidUser : IAmUser
{
    public Int32 Id { get; }
    public String FirstName { get; }
    public String LastName { get; }
    public virtual Boolean IsValid => false;

    public InvalidUser(Int32 id)
    {
        Id = id;
        FirstName = string.Empty;
        LastName = string.Empty;
    }
} 
```

我们去掉了继承结构，只实现了需要的属性。我们还在 *ValidUser* 类上添加了一个静态的 *Invalidate* 方法，该方法创建了 *InvalidUser* 类的一个实例。

还有几件事情可以而且应该改进，比如违反[接口分离原则(ISP)](https://en.wikipedia.org/wiki/Interface_segregation_principle) (同样，我不喜欢在接口上定义属性)，通过实现 Equals/GetHashCode 方法检查相等性，等等。…但我希望我表达了我的观点。
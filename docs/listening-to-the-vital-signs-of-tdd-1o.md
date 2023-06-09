# 倾听 TDD 的生命体征

> 原文：<https://dev.to/janvanryswyck/listening-to-the-vital-signs-of-tdd-1o>

了解我个人的人都知道，我喜欢定期去长跑。我整天坐在办公桌前工作。因此，作为我工作的一部分，我会每隔一天跑一两个小时。我已经在过去的中写过这个。

除了一个人从体育锻炼中得到明显的好处之外，我还学到了一些非常有价值的东西。我学会了如何倾听自己的身体。每个跑步者都知道长跑会有某种疼痛。随着时间的推移，一个人会发展出某种忍受这种痛苦的极限。我知道这听起来不怎么有趣，但确实很有趣。你可以相信我的话😀。

但随着时间的推移，我学会了如何解读我的身体向我发出的信号。这些信号可能非常微妙。呼吸和获得空气有多容易？我的腿感觉如何？我的肌肉感到紧张吗？如果有，达到什么程度？我在不断评估。例如，一两分钟后，我确切地知道我是否能跑得很快，或者我应该慢慢来。甚至在很多情况下，我能够预测即将到来的健康问题或伤害。在这种情况下，我可以选择降低锻炼强度，甚至完全停止锻炼。在任何情况下，当事情发生时，我必须采取相应的行动，防止情况恶化。

当我使用测试驱动开发编写代码时，我也有类似的经历。我写了一个小的失败的测试。我尽可能快地通过考试。然后是最重要的一步:我重构。非常短的连续循环，每个循环不应该占用超过几分钟的时间。但是为什么我指出“重构阶段”是最重要的呢？因为这是我倾听单元测试试图告诉我什么的时刻。正如倾听自己身体的信号是长跑最重要的部分一样，这同样适用于整个测试驱动的开发过程。为了编写可持续的软件，我们作为开发人员必须学会如何接受这些信号。但是我们到底应该听什么呢？

有经验的开发人员经常告诉你，单元测试驱动着设计，从某种意义上说的确如此。但在此之前还有一个重要的步骤。首先，单元测试提供了关于系统设计的非常有价值的反馈。从这个反馈，设计决策开始出现。当被测试系统的代码质量很差时，测试驱动的开发是非常不宽容的。当编写一个失败的单元测试需要很长时间时，这已经告诉我们在重构阶段需要考虑一些事情。从“红色、绿色、重构”循环中，“红色”和“绿色”阶段应该尽可能快地移动。“重构”阶段可能需要更长时间。

这通常是测试驱动开发的新手被这种训练有素的实践所阻碍的地方。“重构”阶段经常被减少或完全跳过。一旦编写和维护单元测试变得很困难，他们就会干掉信使。他们责怪测试本身，而不是倾听和责怪被测试系统的设计。就像跑步新手经常责怪他们忍受的过度疼痛，而不是仅仅根据他们的身体向他们发出的信号行动一样。系统的不良设计导致质量差的脆弱单元测试。

这里有一个小例子来说明这一点。

```
public abstract class CustomerHandlers
{
    public void Handle(RemoveCustomer command)
    {
        // Remove a customer ...
    }
}

public class RegularCustomerHandlers : CustomerHandlers
{
    public void Handle(CreateRegularCustomer command)
    {
        // Create a new regular customer ...
    }
}

public class RegularVipCustomerHandlers : CustomerHandlers
{
    public void Handle(CreateVipCustomer command)
    {
        // Create a new VIP customer ...
    }
} 
```

我们有一个系统可以模拟两种不同类型的客户:普通客户和 VIP 客户。创建其中一个包含不同类型的业务逻辑，但是删除客户对于这两种类型是相同的。实现该功能的开发人员决定为每种类型的客户创建不同的处理程序类。这些特定的处理程序类又派生自一个抽象基类，该基类提供了删除客户的实现。让我们来看看单元测试。

```
[TestFixture]
public class RegularCustomerHandlersTests
{
    [Test]
    public void TestScenario01ForRemoveCustomer()
    {
        // Test scenario 1 for removing a customer
    }

    [Test]
    public void TestScenario02ForRemoveCustomer()
    {
        // Test scenario 2 for removing a customer
    }

    [Test]
    public void TestScenarioForCreateRegularCustomer()
    {
        // Test scenario for creating a regular customer
    }
}

[TestFixture]
public class VipCustomerHandlersTests
{
    [Test]
    public void TestScenario01ForRemoveCustomer()
    {
        // Test scenario 1 (duplicate) for removing a customer
    }

    [Test]
    public void TestScenario02ForRemoveCustomer()
    {
        // Test scenario 2 (duplicate) for removing a customer
    }

    [Test]
    public void TestScenarioForCreateVipCustomer()
    {
        // Test scenario for creating a VIP customer
    }
} 
```

两个具体的处理程序类都使用了一个测试夹具。但是请注意，两个测试夹具都包含相同的单元测试来删除客户。对于开发人员来说，这是一个产品代码的设计看起来合理的例子，但是测试却不是这样。

假设我们需要更改删除客户的功能。如果我们想要使用测试驱动开发，我们应该首先改变这些单元测试中的哪一个。那些在*常规客户处理测试*中的，或者在*贵宾客户处理测试*中的，或者两者都有？这闻起来很可疑。

此外，开发人员一定注意到了为移除功能编写单元测试并不容易。抽象基类不能被实例化，因此必须使用具体的类来调用 Handle 方法。应该选哪个？RegularCustomerHandlers 类或 *VipCustomerHandlers* 类，或者创建第三个专门用于测试的类？到最后，大概两者选了一个。为了弥补这种不好的感觉，一旦功能完成，删除客户的单元测试就被复制到另一个处理程序的测试夹具上。

这就是当我们没有正确地接收到单元测试正在传播的信号时，我们最终会得到的结果。稍微好一点的设计可能如下:

```
public class RemoveCustomerHandler
{
    public void Handle(RemoveCustomer command)
    {
        // Remove a customer ...
    }
}

public class CreateRegularCustomerHandler
{
    public void Handle(CreateRegularCustomer command)
    {
        // Create a new regular customer ...
    }
}

public class CreateRegularVipCustomerHandler
{
    public void Handle(CreateVipCustomer command)
    {
        // Create a new VIP customer ...
    }
} 
```

这里每个命令都有一个特定的处理程序类。同样，单元测试现在看起来像这样:

```
[TestFixture]
public class RemoveCustomerHandlerTests
{
    [Test]
    public void TestScenario01ForRemoveCustomer()
    {
        // Test scenario 1 for removing a customer
    }

    [Test]
    public void TestScenario02ForRemoveCustomer()
    {
        // Test scenario 2 for removing a customer
    }
}

[TestFixture]
public class CreateRegularCustomerHandlerTests
{
    [Test]
    public void TestScenarioForCreateRegularCustomer()
    {
        // Test scenario for creating a regular customer
    }
}

[TestFixture]
public class VipCustomerHandlerTests
{
    [Test]
    public void TestScenarioForCreateVipCustomer()
    {
        // Test scenario for creating a VIP customer
    }
} 
```

这里，我们为每个处理程序类都有一个专用的测试夹具。

当很难编写单元测试时，它提示我们需要更改产品代码。我们需要重构代码，使其易于测试。非常容易测试的产品代码，允许我们在几分钟甚至几秒钟内编写一个单元测试，是对变化敏感的代码。这是我们应该争取的。

但是我们怎样才能学会倾听呢？不幸的是，我们只能通过实践来学习。练习，练习，然后再练习。有大量的[代码表](https://github.com/gamontal/awesome-katas)。在典型的工作场景之外不断练习是非常重要的。而且在您的日常工作中严格应用测试驱动开发。跑步也是一样。定期出去锻炼是你了解自己的方式，你的体能是什么，最重要的是你(目前)没有能力做什么。这是你可以提高的方法。走更长的距离或跑得更快。这是你前进的方式。

与学习测试驱动开发同样重要的是学习软件设计。同时学习这两者应该齐头并进。当您学习如何从单元测试中获取信号时，您也一定会学到一些关于代码设计的东西。尝试不同的设计方法。继续下去。一直都是。
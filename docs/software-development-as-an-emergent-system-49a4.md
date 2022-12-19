# 作为紧急系统的软件开发

> 原文：<https://dev.to/ruidfigueiredo/software-development-as-an-emergent-system-49a4>

涌现系统有时被描述为整体大于部分之和的系统。

我个人更倾向于把一个涌现系统看作是一个整体推理起来很复杂，但当你观察它的单个组件时却很容易理解的东西。

也许自然界中涌现系统的一个最典型的例子就是一群鸟完美协调地飞行。

[![image of a flock of birds flying together in a coordinated way](../Images/94cbcc2eb5f95ab4b97e7610f2989d57.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AWkPwwMw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/08/flock_of_birds.jpg)

如果你试图将鸟类的运动作为一个整体来建模，这将变得困难和复杂。它们的运动看起来几乎不可预测。

但是如果你通过模拟一只鸟来模拟这个系统，那么就变得容易理解了。

这正是针对这个问题(模拟一群鸟的行为)而做的，解决方案叫做 [Boids](https://cs.stanford.edu/people/eroberts/courses/soco/projects/2008-09/modeling-natural-systems/boids.html) 。

在 Boids 中，一群鸟的复杂行为是通过让每只鸟遵循三个简单的规则来实现的。

第一个是*分离*，说明每只鸟都会尽量与其他鸟保持合理的距离。第二条规则是*对齐*，这意味着一只鸟会试图与它附近的鸟对齐。最后，*凝聚力*这意味着每只鸟都将努力移动到附近其他鸟的平均位置。

令人印象深刻的是，仅仅使用这三个简单的规则，模拟看起来是多么逼真。你可以在这个视频中观看他们的行动，这个视频很好地解释了每个规则。

但是这和软件开发有什么关系呢？

软件是由个人开发的，任何合理规模的软件系统都很难作为一个整体来考虑。

不幸的是，不可能想出简单的规则来让我们预测一个特定的软件将如何发展。然而，从这个角度来看软件开发，一些方面可能有助于确定一个软件产品是否是一个高质量的产品，或者它是否会偏离轨道，成为没有开发人员想从事的工作。

想到的一件事就是“初始条件”。在 Boids 的例子中，这将是鸟在模拟中的初始位置。不同的起始位置可能导致一个大群体或多个个体群体。

在软件中，有几样东西可以被认为是初始条件，例如使用的技术类型，甚至是项目的初始结构。

我曾经参与过大型项目，在这些项目中，web 应用程序的默认 MVC 结构(一个文件夹用于控制器，一个文件夹用于视图，一个文件夹用于模型)被盲目采用。拥有一个包含数百个控制器的控制器文件夹很快就会失控。从某种意义上来说，事物变得难以命名，难以发现，难以处理。

Boids 模拟中隐含的一个方面是，每只鸟都完美地遵循它的规则。如果他们不这样做，鸥群就会分裂。

不能指望人们完美地遵循规则，尤其是因为在软件开发中没有完美的规则。

然而，如果我们有一些方法来衡量事情的进展，比如我们可以观察一群人，看看他们在哪里分裂，我们可以把事情推回到和谐。

但要做到这一点，我们需要能够衡量系统的健康状况。

如果你在。Net，并且你使用 Visual Studio，你可以访问一些代码指标，即可维护性指数、圈复杂度、继承深度和代码行数。

这些可能是有用的(尽管可维护性指数可能[有点可疑](https://docs.microsoft.com/en-ie/visualstudio/code-quality/code-metrics-values))。但是，它们有一点局限性，既不能更改也不能添加。

最近，我发现了一个似乎是为解决这个问题而制造的工具。它名为 [NDepend](https://www.ndepend.com) ，不仅有这些指标，还有数百个指标，它还允许您根据自己的需求进行更改，甚至添加自己的指标。

## n 独立

[NDepend](https://www.ndepend.com/) 是 Visual Studio 的付费扩展(有试用期)。

每次构建解决方案时，它都会分析您的解决方案，并以交互式图表和显示通过/失败规则的仪表板的形式提供见解。

[![NDepend's Dashboard](../Images/8cf199143209fd5fee5d6ea56e1d5517.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w_Nn9WwN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/08/NDependDashboard.png)

NDepend 中的规则类似于 Linq 查询(在 NDepend 中它们被称为 CQLinq，CQ 代表代码查询)，并且有 200 多个可用的规则。

它们也很容易阅读，例如，该规则为代码超过 10 行的方法创建一个警告:

```
// <Name>Keep methods short and concise</Name>
warn if count > 0 from method in Methods where method.NbLinesOfCode > 10 select method 
```

当有方法被该规则选择时，您将看到以下内容:

[![Tree view of methods matching more than 10 lines of code](../Images/03a2f18159f1d5c7a54cf3c8ae392d23.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o0yew8Af--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/08/Rule-match-example.png)

你可以点击任何一个方法，NDepend 将带你到它们在源代码中的位置。

您可以在 NDepend 的规则资源管理器面板中找到预定义规则和自定义规则:

[![Queries and rules explorer panel displaying the rule we've just created](../Images/1897512076e528e59c70635bd103833e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cKvEKs11--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/08/Queries-and-Rules-Explorer-Panel.png)

您可能已经注意到我在规则定义中使用了`select method`。您可以通过返回一个带有方法属性的匿名对象和我们想要添加的额外信息来添加额外信息。这些额外的信息将显示在用户界面上。

例如，让我们添加方法的可见性以及有多少方法是从有问题的方法调用的:

```
// <Name>Keep methods short and concise</Name>
warn if count > 0 from method in Methods where method.NbLinesOfCode > 10 select new {
    method,
    method.Visibility,
    method.MethodsCalled
} 
```

当您在规则浏览器中单击规则时，可以访问此信息。您甚至可以将鼠标悬停在每个“已返回”字段上，以获取额外信息:

[![Hover over MethodsCalled example](../Images/29be9b8a607909faa2e20393963c37b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i3kZK-UZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/08/MethodsCalled.png)

除了能够定制规则的显示方式之外，您还可以定义它们如何在技术债务方面影响项目。也就是说，在任意长的时间内。

假设我们认为在一个方法中，修复 10 行代码中的每一行平均需要 5 分钟(例如，通过将方法分解成更小的方法),我们希望量化这一点。

我们可以在规则的`select`语句中包含一个*债务*值来描述这一点:

```
// <Name>Keep methods short and concise</Name>
warnif count > 0 from method in Methods 
where method.NbLinesOfCode > 10 
select new { 
    method,
    method.Visibility,
    method.MethodsCalled,
    method.NbLinesOfCode,
    Debt = (5 * (method.NbLinesOfCode -10)).ToMinutes().ToDebt()
} 
```

这将添加项目的总债务，您可以在 NDepend 的仪表板中查阅。

这只是你可以用规则做什么的一个例子。我只提到了方法，但是您可以查询程序集、类型、字段、您创建的代码(您可以在 NDepend 中微调这意味着什么)、第三方代码等等。你也可以挑选出你想要考虑的规则，以防你不同意其中的一些。

此外，您可以将 NDepend 与构建服务器集成在一起，当您检测到一些您认为严重的问题时，让构建失败。这是通过一组名为*质量关口*的规则来实现的，您也可以对其进行定制和添加/删除。

除了规则之外，您还可以生成图形，提供对代码库的可视化洞察。

例如，您可以创建一个“代码度量视图”图表，它可以同时向您显示两个维度。例如，一个类中的代码行数(LoC)及其圈复杂度。LoC 由类占用的面积和颜色的复杂程度来表示:

[![Code Metrics View example where it is possible to see that the ManageController is the worst class in the project](../Images/6f00d63b77e68e578e8968ca4e6994e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4WwJO_Kg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/08/Code-Metrics-View.png)

从上面的例子中可以看出，就圈复杂度而言，`ManageController`是“最差”的类，就代码行数而言，它是第二大类。

NDepend 还有其他图形类型，可以用来更好地理解项目的依赖性、继承层次和测试代码覆盖率，这只是其中的几个例子。

然而，所有这些只能让你看到你的项目现在在质量方面的表现。

我最喜欢的 NDepend 特性是，您可以创建一个*基线*，然后查看项目如何相对于该基线进行发展(您可以在仪表板上进行配置)。

例如，如果您添加或删除*债务*或有或多或少的规则通过/失败，这将被突出显示，您可以真正有一种看到项目发展方向的感觉。

## 结论

软件开发是一门复杂的学科，尤其是当你考虑到它是由几个人一起工作完成的时候。

将它与紧急系统相比较是有用的，因为它提供了一个视角，我们可以将软件看作是进化的东西。

如果我们想知道我们正在构建的产品是否在质量方面保持稳定，那么能够度量这种发展是至关重要的。

我还描述了一个名为 NDepend 的工具，它正好服务于这个目的(据我所知，它没有竞争对手)。它提供了广泛的度量标准，并允许创建定制规则，同时支持与持续集成工作流的集成。
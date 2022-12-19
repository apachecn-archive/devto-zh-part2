# 重构遗留系统——第 3 部分:游戏计划和重构技巧

> 原文：<https://dev.to/jamesmh/refactoring-legacy-monoliths---part-3-game-plan-and-refactoring-tips-c56>

*注:[见我博客](https://www.blog.jamesmichaelhickey.com/refactoring-game-plan-and-tips/)的规范帖子 T3】*

所以你的工程团队确信你需要做一些重大的改变。未来发展的方向需要改进。事情不能一成不变。管理层也确信产品需要向新的方向发展。下一步是什么？

在对产品进行任何实际的更改或重构之前，下一步是规划重构。换句话说，你需要一个游戏计划。我还将讨论一些重构技巧，供您开始使用！

# 重构是否意味着重写？

我在 Reddit 上看到的关于这个系列的一个评论(相当多...)是指责我建议你应该重写你的整个代码库。我从来没这么说过。我也没有暗示你应该。真的，你可能不应该。

# 信任

作为复习，Michael 在他的书《有效使用遗留代码》中写道:

> 对我来说，遗留代码就是没有测试的代码。

为什么 Michael 如此关心从内部测试您的代码？(也就是说，不要让人一遍又一遍地测试你的**网站**——顺便说一句，这真的很贵)。有一个简单的问题可以回答这个问题:

> 如果你要以一种非平凡的方式改变你产品的一个核心特性，你对做出这种改变有信心吗？你会相信这个系统还会像以前一样运作吗？

如果你没有任何测试，那你怎么能自信呢？你怎么能**信任**你的系统呢？

进行测试就像有安全网和没有安全网在空中表演杂技一样。**哎哟！**

# 你的目标是什么？

你的第一个目标应该是开始在你的代码上实现单元测试。这是基础性工作。你需要能够改变你的代码，并有信心它仍然工作。

再次重申:

> 你的第一个目标应该是实现基于代码的测试。除非你有一个安全网，否则你不能自信地重构你的系统。

在此之后，你的目标可能会有所不同。如果你已经完成了第[步第 1](https://www.blog.jamesmichaelhickey.com/refactoring-legacy-monoliths-first-steps/) 和第[步第 2](https://www.blog.jamesmichaelhickey.com/refactoring-legacy-monoliths-part-2-convincing-management/) ，那么你应该有一个需要改变的清单。

在这一点上，我建议进行一次正式讨论(有正式的结果/文件),回答以下问题:

> 我们希望我们的系统在 1 年后会是什么样子？两年内？

也许我们应该使用像 ASP 这样的新技术堆栈。网芯？也许我们当前的代码架构不允许我们在其他平台中重用我们的业务规则(web vs. mobile API vs. desktop app)？这意味着我们需要整合我们的业务逻辑和规则。(注:这些情况都不需要重写)

# 处理依赖关系

最大的障碍是**依赖**，它(很可能)阻止您创建隔离的单元测试，并隔离您的业务规则和实体。

一旦开始，你会发现你开始告诉自己:

> 为了测试[事物 1]，我现在需要一个[事物 2]的实例。但是，[事物 2]需要[事物 3]的一个实例。

“Thing 1”可能是您想要测试的一个实体——比方说一个`Report`实体(它对一些表格数据进行建模)。

现在，假设“Thing 2”是另一个类- `LinkGenerator`(它为报告生成链接)。

`LinkGenerator`需要访问“Thing 3”，也就是`HttpSession`。

如果你想对`Report`实体进行单元测试，你需要:

*   需要的`LinkGenerator`的一个实例...
*   `HttpSession`的一个实例

**呃哦。**当你需要`HttpSession`的时候，你怎么能进行单元测试呢？单元测试不能在 web 服务器上运行！(嗯，他们不应该...)

抱歉地说(你已经知道了...)，这需要一些工作。你需要**打破依赖链。**

幸运的是，这是重构的主要目标之一。其他人已经为我们做了艰苦的工作。

# 依赖打破和重构技巧

让我们看看几个打破依赖的重构技巧。

### 1。把全局变成接口。然后给他们注射。

标题说明了一切。坚持我们的小例子，想象一下`LinkGenerator`有下面的方法(伪代码)。

```
public string GenerateLink()
{
     // ... some kind of processing
     var someValue = HttpSession["SomeKey"];
     // ... more processing
     var someOtherValue = HttpSession["SomeOtherKey"];
     return link;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们不能测试这个方法，因为它引用了只存在于 web 应用程序中的`HttpSession`对象。我们不希望我们的模型或业务实体知道 web(这与我们将业务实体与我们的数据表示隔离开来的目标是一致的)。

相反，通过注入一个接口，我们可以消除对实际`HttpSession`的依赖。

```
public string GenerateLink(IHttpSessionAccessor session)
{
     // ... some kind of processing
     var someValue = session.GetValue("SomeKey");
     // ... more processing
     var someOtherValue = session.GetValue("SomeOtherKey");
     return link;
} 
```

Enter fullscreen mode Exit fullscreen mode

我相信您可以想象接口定义会是什么样子。具体的类可能看起来像这样:

```
public class HttpSessionAccessor : IHttpSessionAccessor
{
    private readonly HttpSession _session;

    public HttpSessionAccessor(HttpSession session)
    {
        this._session = session;
    }

    // You could be fancy and use generics?
    public object GetValue(string key)
    {
        return this._session[key];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以在测试代码中这样做:

```
IHttpSessionAccessor session = new Mock<IHttpSessionAccessor>();

// Implement the mock
// Or just assign "session" with a dummy implementation of IHttpSessionAccessor.

LinkGenerator generator = new LinkGenerator();
string link = generator.GenerateLink(session);

// Assert ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以围绕`LinkGenerator`构建测试，并且有信心:

*   它实际上以我们期望的方式工作
*   任何重大更改都将被捕获(并修复)。

### 2。适应参数

想象我们上面的代码最初是这样的:

```
public string GenerateLink(HttpSession session){
     // ... some kind of processing
     var someValue = session["SomeKey"];
     // ... more processing
     var someOtherValue = session["SomeOtherKey"];
     return link;
} 
```

Enter fullscreen mode Exit fullscreen mode

怎么了?我们有和上面一样的问题。我们**仍然**需要一个`HttpSession`的实例。这意味着...我们需要一个运行的网络服务器。不好。

要解决这个问题，只需做与#1 相同的事情。把参数变成接口，访问接口而不是实际实现(`HttpSession`)。

### 3。提取方法

您可能熟悉这种技术。如果你有一段代码正在做多个块或者有多个职责，那么你需要把它们分开。取一段做一件事的代码，从中创建一个新方法。避免引用全局状态(比如`HttpSession`)，这样你就可以对你的新方法进行单元测试。

在哪里分解代码的好指标是:

*   在同一个代码块中，先说“做这件事”，然后说“做下一件事”的注释通常都是提取的候选对象。
*   C#区域是一个很好的指示符，表明你需要将代码分割开来！

### 结论

您需要关注的主要领域是:

*   制定一个游戏计划，描述**您希望您的系统在 1 年内达到的水平**
*   让您的软件**可信**
*   能够在对代码进行更改后拥有**信心**

依赖需要被打破。但这最终会把你带到一个地方:

*   你的代码是可测试的
*   你的整体设计(整体)更好
*   您可以信任更改后的系统/软件

感谢阅读:)请在评论中告诉我你的想法。你曾经亲身经历过这个过程吗？让我知道！

# 保持联系

别忘了在 [twitter](https://twitter.com/jamesmh_dev) 或 [LinkedIn](https://www.linkedin.com/in/jamesmhickey/) 上联系我！

# 导航您的软件开发生涯

一封电子邮件简讯，我将在其中回答订阅者的问题，并就以下主题提供建议:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？

听起来有趣吗？加入社区吧！
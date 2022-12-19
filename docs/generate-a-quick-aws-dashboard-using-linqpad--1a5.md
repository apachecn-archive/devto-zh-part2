# 使用 LINQPad 生成快速 AWS 仪表板

> 原文：<https://dev.to/shrayasr/generate-a-quick-aws-dashboard-using-linqpad--1a5>

## 上下文

Logic Soft 的主线产品- [WINBDS](http://logicsoft.co.in/product) 是一个本地软件，即我们在客户处安装它，并远程支持他们。

一段时间以来，这种情况已经发生了变化。对于小型安装，云上的一个实例通常更便宜、更好。所以我们已经开始建议人们在安装足够小的时候转向 AWS。

事实证明，如果我们努力在不需要的时候关闭实例，那么 AWS 上的 windows box 实际上是相当便宜的。SQL Server Express 对于这种免费(有限制)的安装来说绰绰有余

这再加上我们已经建立的一个小工具，打开和关闭实例，实际上已经为我们的客户和我们带来了双赢的局面。

这意味着我们需要一种方法来快速监控我们托管的实例的状态。我们没有托管太多实例，因此我认为有必要拥有一个全面的监控仪表板，也没有托管太少实例，因此我无法单独检查它们。

我需要一个低技术含量的解决方案来完成这项工作。

## Enter LINQPad

如果你是一名 C#开发人员，还没有在 LINQPad 上投入时间和金钱，那么你应该停止阅读这篇博客，直接进入正题。一旦你喜欢上了它，它就是那种可以将生产力提高 10 倍的工具之一。

对于门外汉来说，LINQPad 是一个允许你快速使用 C#/F#/VB 表达式/语句/程序的程序——如果你可以的话，是一个便签本。把它想象成一个拥有强大工具和功能的游乐场。

去[http://www.linqpad.net](http://www.linqpad.net)下载并玩它。

## 解

因为我几乎总是有一个 LINQPad 窗口在运行，所以我决定在 LINQPad 内部编写我的仪表板。如果你因为认为这个想法毫无意义而打算继续读下去，那么[再想想](https://twitter.com/Nick_Craver/status/822268228122669057)。

让我们从安装 [AWSSDK 开始。来自 NuGet 的 EC2](https://www.nuget.org/packages/AWSSDK.EC2/) 包。LINQPad 内置了 NuGet 集成，因此我们可以为我们编写的每个查询安装软件包。

我们有跨地区的实例，所以让我们使用一个列表来跟踪我们感兴趣的地区:

```
var regions = new List<RegionEndpoint>
{
  RegionEndpoint.USEast1,
  RegionEndpoint.EUWest1,
  RegionEndpoint.APSouthEast1
} 
```

Enter fullscreen mode Exit fullscreen mode

对于每个区域，让我们创建一个 AWS 客户端

```
foreach (var region in regions)
{
  var client = new AmazonEC2Client("<AWS Access Key>", "<AWS Secret Access Key>", region); 
```

Enter fullscreen mode Exit fullscreen mode

对于`Access Key`和`Secret Access Key`，推荐的做法是从 [IAM 控制台](https://console.aws.amazon.com/iam/home#/home)专门为此创建一个用户，并使用这些凭证。

### 在 IAM 上创建用户和策略

IAM 是 AWS 的身份管理平台。在这里，您可以创建用户、附加策略、管理角色等。基本上一切都与管理谁和他们如何使用你的 AWS 帐户。

对于我们今天的任务，我们必须创建一个仅具有编程访问权限的用户。我们创建的这个用户没有控制台访问权限，因为我们只希望通过这个用户构建一个控制面板。

让我们首先创建一个可以附加到任何用户的策略。策略描述了在指定资源上允许的一组操作。因此，当策略附加到用户时，该用户将只被允许执行策略中概述的操作。

对于这个用例，我们只需要能够检查实例的状态，所以我们要寻找的是`ec2:DescribeInstances`动作。我们希望能够在**上运行该操作的资源应该是**和`*`。不幸的是，`DescribeInstaces`不允许为特定的资源指定，所以我们*让*使用资源的`*`标识符([相关线程](https://forums.aws.amazon.com/thread.jspa?messageID=512129))。

我们的政策是这样的:

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Effect":  "Allow",  "Action":  [  "ec2:DescribeInstances"  ],  "Resource":  [  "*"  ]  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，将它分配给一个用户，并选择用户的`Access Key`和`Secret Access Key`。这是我们将用来创建客户端的内容

* * *

让我们回到客户端，并加入我们在上一步中获得的凭证。

从这里，我们调用`DescribeInstances`函数，该函数返回我继续操作所需的数据。一点点 LINQ，我们准备好了:

```
client
  .DescribeInstances()
  .Reservations
  .SelectMany(r => r.Instances)
  .Select(instance =>
  {
      var isUp = (instance.State.Name == InstanceStateName.Running);
      var upSince = isUp ? (DateTime.Now - instance.LaunchTime) : TimeSpan.FromSeconds(0);

      return new 
      { 
          Id              = instance.InstanceId, 
          Name            = instance.KeyName, 
          LastLaunchTime  = instance.LaunchTime,
          UpSince         = upSince,  
          IsUp            = isUp,
          State           = instance.State.Name.Value
      };
  }) 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，这里真正令人兴奋的部分是代码的简洁和可读性。没有解释*如何*做某事，即*做什么*。

完成这些后，我们就有了我们需要的数据，而且是我们需要的格式。下一个问题是，这是如何神奇地变成一个*“仪表板”*？

## LINQPad 的`Dump`功能

这个函数确实应该有一个单独的部分，在我看来，正是这个部分使得 LINQPad *这样的*成为一个令人信服的工具。

`Dump`是一个在 LINQPad 查询中的每个对象的**上都可用的函数。它试图以最好的方式展示你的对象。**

让我们考虑一个简单的列表:

```
new List<string> 
{
  "Ramu",
  "Shamu",
  "Suppandi",
  "Kaalia"
} 
```

Enter fullscreen mode Exit fullscreen mode

然后像这样调用上面的`.Dump()`函数:

```
new List<string> 
{
  "Ramu",
  "Shamu",
  "Suppandi",
  "Kaalia"
}.Dump(); 
```

Enter fullscreen mode Exit fullscreen mode

这是我们得到的结果:

[![List<string> dump output](../Images/14d422628d205eccb8251cfe18ddc023.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zIdgeXXr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/RLyQWxu.png)

简洁明了吧？

还有更好的。让我们撞一下，考虑一个物体:

```
new
{
  Name = "Ramu",
  Siblings = new [] { "Shamu" },
  Genre = "Comedy",
  Age = 5
}.Dump(); 
```

Enter fullscreen mode Exit fullscreen mode

我们得到:

[![Object dump output](../Images/fa638e5e7ab1c9b48cb51d8d551249ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iaMwGSgI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/DlwpphI.png)

很好地格式化成键值对，对象中的列表也格式化得很好。

可选地，如果我们给`.Dump()`函数提供一个字符串参数，它将把它显示为正在转储的内容的标题。例:

```
new
{
  Name = "Ramu",
  Siblings = new [] { "Shamu" },
  Genre = "Comedy",
  Age = 5
}.Dump("Ramu -- Details"); 
```

Enter fullscreen mode Exit fullscreen mode

[![Dump with title](../Images/06b9c9d9914f6f822b0c99b6e7d88e1a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--okbAeIeG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/INiW54e.png)

它不止于此。当在我们使用 LINQ 查询数据库的上下文中使用时，它变得更加强大。

一旦您创建了一个*连接*，您就能够将该连接放入一个查询中，并在您的数据库上的表上运行 LINQ 查询。考虑一个名为`cities`的表，它包含系统中的城市列表。要查询并获取第一行，您应该做:

```
Cities.FirstOrDefault() 
```

Enter fullscreen mode Exit fullscreen mode

去打印它们？`.Dump()`！所以:

```
Cities.FirstOrDefault().Dump(); 
```

Enter fullscreen mode Exit fullscreen mode

会给我们:

[![FirstOrDefault dump output](../Images/56d735d08e11634d094850eb1d1e4e59.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tOHVxmyM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/BtRXTHl.png)

就像我们之前打印的一样。

但是它变得*甚至*更好！考虑一个`Prescription`表，它与另外 3 个表有 FK 关系- `Representative`、`Dealer`和`School`。现在当我用
选择列表中的第一条记录时

```
Prescriptions.FirstOrDefault().Dump(); 
```

Enter fullscreen mode Exit fullscreen mode

[![FK dump output](../Images/b110d1381295998c4a55b2f14f1a3446.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dp26VUdW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/RFHZBEa.png)

它解决了这些依赖性，并把它们放在适当的位置！绝对缺德！

当你在探索一个数据库时，`Dump()`函数可能是一个福音。这也是 LINQPad 成为我的开发环境的核心部分的原因之一。

* * *

让我们回到我们停止的地方，我们现在有了一个对象列表。没什么特别的。

我们该怎么展示它呢？

[![DUMP all the things meme](../Images/6cb514f7bf07f4a43b456ba6334a15c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ryIP0KBP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/xVVkv3x.png)

我们将用`region`作为参数调用`.Dump()`函数。这将为我们提供一个格式良好的实例及其状态的区域列表。

```
client
  .DescribeInstances()
  .Reservations
  .SelectMany(r => r.Instances)
  .Select(instance =>
  {
      var isUp = (instance.State.Name == InstanceStateName.Running);
      var upSince = isUp ? (DateTime.Now - instance.LaunchTime) : TimeSpan.FromSeconds(0);

      return new 
      { 
          Id              = instance.InstanceId, 
          Name            = instance.KeyName, 
          LastLaunchTime  = instance.LaunchTime,
          UpSince         = upSince,  
          IsUp            = isUp,
          State           = instance.State.Name.Value
      };
  }).Dump(region.DisplayName); 
```

Enter fullscreen mode Exit fullscreen mode

屏气凝神凝神凝神凝神凝神凝神凝神凝神凝神凝神凝神凝神凝神：

[![Dashboard output](../Images/e44a3fd4b1be5f226dc620ca50b90d0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OUdFtGcF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/GvZlDlk.png)

厉害！它**工作**，它**可维护**，更糟糕的是，它**科技含量低**。除了一个问题之外，所有的问题都萦绕在我的脑海里:

*“你不会指望我一直运行这个来检查结果吧？”*

请放心，LINQPad 也为您提供了这方面的帮助:)

## lprun 用于命令行脚本目的

[lprun](https://www.linqpad.net/lprun.aspx) 是 LINQPad 提供的从命令行运行脚本的工具。

它的默认输出是 JSON 格式，但是我们可以通过使用`format`标志来改变它。设置`-format=html`输出**与我们在 LINQPad 中看到的**完全相同。这对自动化非常有用。

通过一个调度器任务，定期将输出数据发送到服务器，我们就可以开始工作了！

这就是我所做的，目前我们对此非常满意。也许当我们有更多实例时，我们会扩展到更好、更可操作的仪表板，但这适用于我们当前的规模。

## 外卖

*   LINQPad 太牛逼了。
    *   如果你是 C#/F#/VB 用户，那就用吧！
*   我们需要更多像 LINQPad 这样的工具来促进快速迭代
    *   REPLs 属于同一类别。所以，如果你正在使用的语言有 REPL，好好利用它
*   不要把事情复杂化，简单保证对大多数情况都有效——必要时你可以扩展
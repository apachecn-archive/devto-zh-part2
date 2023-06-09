# 为 Fitbit 操作系统制作应用程序:我的印象

> 原文：<https://dev.to/iansavchenko/making-an-app-for-fitbit-os-my-impressions-2ijb>

[![](img/87d6b322afca5d03414861236957e348.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5BTMOcWA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rrsnv9isebwyymhcykm8.jpg)

### [T1】简介](#intro)

今年 6 月，我碰巧参加了在斯德哥尔摩举行的 Fitbit 开发者聚会，并赢得了一个免费的 Fitbit Ionic 手环。事实上，我在圣诞节的时候看着它，觉得它可能是一个健身乐队购买的好选择。它拥有所有基本功能、无线支付和运行第三方应用的可能性。所以当我免费得到它的时候我真的很高兴！meetup 的目标是在开发者中推广 Fitbit 平台，我决定尝试一下，做一个应用。

### 一款 app 的创意

[![parking sign on my street](img/e84f0e0f519fc1af10e5c3ead96262b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hA8jZrvF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/57zst89smhoeco5ix5dn.jpg)

作为一名在瑞典的外籍人士，我经常看到人们如何使用周数作为描述时间的方式(一开始超级混乱)。此外，我住的街道上的停车标志禁止在奇数/偶数周停车，所以知道当前的周数在瑞典是至关重要的事情！我决定为此写一个[小但合适的应用](https://github.com/IanSavchenko/fitbit-week-number)，并完成一个完整的周期，直到发布到 Fitbit 应用库。

### 一切从哪里开始

[![Fitbit Studio](img/6320710dd7e0829e999a158e87c0a59a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jWX7xm-t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/48ixccg2j66v79n740u0.png)

你所有的 Fitbit 应用程序都在 Fitbit Studio 中启动。这是一个在线 IDE，也是目前为 Fitbit 操作系统创建应用程序的唯一选择。你用你的 Fitbit 账户(和你用来登录 Fitbit app 的账户一样)登录，然后创建一个项目:一个 app 或者一个钟面。

我不会描述如何为 Fitbit 制作应用程序，但会从我的(当然是主观的)观点出发，讨论这个过程的利弊。

### 什么叫伟大

#### 👍一切都用 JavaScript(+CSS，SVG，一些 JSX)

我相信这是该平台的一个巨大优势，进入生态系统的门槛可以相当低。每天上班写 JS，用了我很少的时间就上手了。

#### 👍许多[例子](https://dev.fitbit.com/build/tutorials/)和[文件](https://dev.fitbit.com/build/guides/)

虽然我不能说文档结构完美，但我可以在那里找到几乎所有我需要的信息。在写我的代码之前，我已经阅读了几乎所有的[文章](https://dev.fitbit.com/build/reference/)和[指南](https://dev.fitbit.com/build/guides/design-guidelines/)(我知道那部分不应该很大)，以确保我不会错过任何系统限制，我发现它很有用。例如，只有一个小的[注释](https://dev.fitbit.com/build/guides/user-interface/javascript/)不允许动态创建 UI 元素，这让我改变了一些实现计划。

#### 👍蛮大的社区和[论坛](https://community.fitbit.com/t5/SDK-Development/bd-p/sdk)

不幸的是，我唯一的问题没有得到任何回应(显然，我对此事看得太深了)，但其他话题相当活跃，人们可以在论坛的历史中找到很多好处。

#### 👍模拟器很棒

你可以在本地安装它们，当从 Fitbit Studio 进行部署时，它们非常快。因此，你不需要一个设备来制作一个应用程序(除了你，实际上，做:继续阅读)。

#### 👍设备调试良好

你需要将乐队连接到 Wi-Fi，乐队和 Fitbit Studio 之间的连接过程需要一分钟左右(有一天晚上对我来说完全不起作用，但只有一次，所以我们可以假装它从未发生过)。之后，它通常是稳定的，并且相当快(谢天谢地，我的应用程序的有效载荷只有 25 kB)。

#### 👍稳定性和质量

在 Fitbit 的生态系统中，并非所有东西都存在，但如果某个功能存在，它就能正常工作。我不能说在开发过程中有任何超级烦人的错误、问题或崩溃。在线 IDE Fitbit Studio 是有限的，但提供了代码编辑的基本需求，并按预期工作(尽管我对将代码仅信任于网页而不将其存储在本地表示怀疑)。

#### 👍对应用权限的精细控制

[![App permissions](img/1c1da47ac570162ca8f91b7b77389282.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C39R-vwR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1bsbsawe7cqhp1tnt05x.png) 
这是我作为 Fitbit OS 用户喜欢的，知道没有我的直接同意，应用程序不能做太多事情。成熟操作系统的“必备”。

### 没有什么了不起的

更新:现在我们有了 Fitbit 的 [CLI 工具，所以接下来的一些要点不再有效！耶！👏👏👏](https://community.fitbit.com/t5/SDK-Development/By-Your-Command-Meet-the-Command-Line-Interface/m-p/2914002/highlight/true#M5897)

#### 👎无代码版本控制(修复:不再受 Fitbit Studio 限制！👍)

所以现在我们回到注释掉代码，同时进行实验，并把它留在那里，因为“你永远不知道什么是有用的”。代码版本控制是当它不存在时，你知道你错过的东西。虽然没有它，没有快速的概念验证或演示应用程序，任何更大的项目都会变得困难。即使在我小小的“周数”应用程序中，我也会很乐意使用它。

留给您的唯一选择是将项目导出到一个归档文件中，您可以进一步将其提取到 git repo 中。谢谢你。

#### 👎无法导入项目

是的，您没听错:虽然您有导出功能，但您没有导入功能。因此，即使是导出的项目，您也需要将文件拖放到 Fitbit Studio 中才能将其导入回来(是的，只有“拖放”，我没有找到任何更直接的按钮)。

#### 👎Fitbit Studio lockdown(修复:不再受 Fitbit Studio 限制！👍)

由于它来自于前两点，你的 Fitbit 开发的唯一选择是 Fitbit Studio(对我来说，每次更改都拖放文件不是一个选项，抱歉)。没那么糟糕，但是每个开发人员都有自己的方式来使用自己喜欢的 IDE/编辑器和工具。[从 2018 年 1 月](https://community.fitbit.com/t5/Feature-Suggestions/Fitbit-CLI-for-SDK-for-Fitbit-OS-apps-Command-Line-Tools/idc-p/2380823/highlight/true#M141981)开始，Fitbit 团队就在承诺 CLI 工具，但现在还没有。

#### 👎没有分析和反馈

那对我来说是一个真正的阻碍。你发布了你的应用，但你永远不知道是否有人使用它，甚至不知道有多少人下载了它。这是如此“必须拥有”，以至于当我在 Fitbit Gallery 应用程序管理器中没有找到它时，我感到非常惊讶。我使用“Week Number”应用程序的目标是发布一些小东西，观察生态系统，获得反馈，并了解该平台是否值得进一步投资。这是不可能的。

理论上，我可以将一些分析工具集成到应用程序中，并使用来自乐队本身的 HTTP 请求 API 报告统计数据，但我必须为此向用户请求相应的权限。作为一个用户，我不明白为什么一个显示星期数字的应用程序需要互联网连接，所以我不会这样做。在最好的情况下，如果你的应用程序需要连接到互联网，这对于用户来说是显而易见的，作为一个开发人员，你可以“偷偷”在你的后端进行一些分析或测量。但是如果你做了一个免费的离线工具，你将一无所有。

在用户反馈和评级方面也是如此 Fitbit 平台上没有这种东西。

#### 👎只能从 Fitbit 应用程序访问 Fitbit 应用程序库

这是另一个技术限制，我认为这对第三方开发者和 Fitbit 平台本身都是不利的。只有使用 Fitbit 应用程序，用户才能看到应用程序库中有哪些应用程序。你不能像在其他移动平台上一样，在浏览器上这样做(Google Play、Microsoft Store 和 Apple Store 允许你在没有任何设备的情况下查看应用程序的描述，AFAIK)。现在，Fitbit 和应用程序发行商都失去了通过有机搜索获得新用户的可能性。

#### 👎乐队表演

它非常有限。这不是一个真正的缺点，但人们必须意识到这一点，并在这里和那里做优化，特别是如果你有动画。我的应用程序只有一个小动画，但我仍然必须确保没有其他执行阻塞动画周期。我在上面提到了 Fitbit 的模拟器有多好。他们太好了，所以性能问题可能会被隐藏。这可能适用于移动开发中的许多模拟器。他们的表现与设备不同，所以如果你有动画或大量的用户界面操作，你*希望*在一个真实的乐队上测试它，看看他们是否没有任何“打嗝”。此外，人们无法测试应用程序图标在 Fitbit 模拟器上的外观，这有点令人沮丧。当然，在你的电脑屏幕和设备上，颜色看起来是不同的。

#### 👎JS API 限制

我不会说这是一个大问题，但一些我自然期望的东西不见了。例如，用于处理时间和日期本地化格式的 API(首先，这是一个手表！).Fitbit 官方支持的语言列表包含大约 10 个地区，我相信有可能内置一些支持本地化的格式化 API。

#### 👎有限的使用案例

不是 Fitbit 的特殊问题，而是所有这类设备的普遍问题。我发现很难与智能手表进行更长时间的复杂交互(基本上，双臂都必须抬起，如果你需要做的不仅仅是轻击/滑动，那么在小屏幕上你需要做非常精确的手指运动)。因此，这种有限的 API(对于第三方开发人员来说总是有限的)只为小型实用程序或配套应用程序留出了空间。我认为，后者是最好的场景，可以让你的主要移动应用脱颖而出。否则，除了时间/日期/闹钟、锻炼/活动跟踪和无线支付(当然，还有周数！)我更喜欢用手机。但这也是我的主观看法。

### 结论

我真的很喜欢为 Fitbit 制作应用程序。我欢迎像开放平台这样的倡议，第三方开发者可以在这个平台上扩展设备的功能。虽然 Fitbit 的开发生态系统有很多很棒的东西，团队到目前为止做得很棒，但仍有改进的空间。即使在制作一个小小的应用程序时，我也感觉到了限制和缺乏我已经习惯的工具:代码版本控制系统和分析。对我个人来说，这两个程序很棒，在它们被修复之前，我可能不会开发其他 Fitbit 应用程序。

谢谢你读到这里。如果我做错了什么或者你有相关的经验，请在评论中告诉我！

[![Week Number app](img/767e7f1ddc2e9a275759781f67cba896.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ff3rZ5hz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/IanSavchenko/fitbit-week-number/blob/master/screenshots/ionic-animated.gif%3Fraw%3Dtrue)
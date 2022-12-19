# 🚀UI-licious 有什么新功能？IE11，突出交互，用户代理等等。

> 原文：<https://dev.to/uilicious/whats-new-in-ui-licious-ie11-highlighted-interactions-user-agents-and-more-1a2>

在这里的 [UI-licious](https://uilicious.com) ，我们不顾一切地致力于让每个人自动化 UI 测试，为每个人建立一个更好的网站。

如果你没有听说过 UI-licious，我们只是一个整洁的小工具，让你自动测试用户在你的 web 应用程序上的旅程-这就是工作。

<figure>

[![Testing is a piece of cake](../Images/d9f06576442e4c2186d27b48c7f5d2f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wMtMJFCH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/12ad7sjuaotvawcyy2vg.gif)

<figcaption>Testing is a piece of cake.</figcaption>

</figure>

那么 UI-licious 有什么新功能呢？

# 在 IE11 上运行测试

如果你不需要支持 IE11，那很好！

但是如果你坚持为 IE11 开发，那么终于有好消息了！

在两年构建 UI-licious 之后，我们现在支持在 IE11 上测试。🎉

我们非常高兴我们终于来到了这里。当我们第一次开始构建 UI-licious 时， [@picocreator](https://dev.to/picocreator) 和我决定的一个核心理念是，**测试必须独立于 UI 如何实现和 UI 服务的环境(即 OS &浏览器)**。我们认为这很重要的原因是**它使得测试更加可重用和可维护。然而，每个浏览器在渲染和实现浏览器自动化的 WebDriver 协议方面都有自己的特点，所以许多测试工程师求助于在他们的测试脚本中编写特定于浏览器的技巧来解决这些不一致。我不责怪浏览器没有使用 WebDriver 协议标准来获得正确的测试自动化，规范并不是最清晰的，而且它们一直在变化，真见鬼。**

从好的方面来看，我们永远不用担心 IE11 的规格会改变。

# 高亮显示为交互命令

UI-licious 的目标之一是使 bug 复制报告尽可能清晰、完整和明确。作为一名软件工程师，当错误报告中包含不完整或不清楚的信息时，我会非常恼火，我不得不追着报告者要求澄清。

因此，我们添加了高亮显示，向您展示在屏幕上执行`I.click`和`I.fill`命令的位置。我们将在其他命令中慢慢推出这一特性，请耐心等待！

<figure>

[![Highlights for interaction commands](../Images/c4733f052d06a94fc269a77dd3d4f910.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_TL-2pxs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4r4hv6phwhwyu67a88sv.png)

<figcaption>Now you can see better! 🤓</figcaption>

</figure>

# 全页截图

我们增加了新的命令`TEST.takeFullScreenshot`让你抓取当前页面的完整页面截图。*目前仅支持 Safari 和 IE11 测试。*但是，在你真正需要的时候使用它！一直抓取整页的截图会降低你测试的速度。

<figure>

[![Taking full page screenshots](../Images/130d68ea4f73053980d131b9ccb78655.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---kvxjEnF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5x93t3llzwgkck8cdqre.gif)

<figcaption>Hm… let’s do a bit of window shopping today…</figcaption>

</figure>

# 设置浏览器用户代理

如果您的 web 应用程序依赖于浏览器用户代理检测，您可以为测试运行设置用户代理。*目前仅支持 Chrome 测试。*默认情况下，用户代理字符串将由操作系统和浏览器自动确定。您可以使用`I.goTo("https://www.whatismybrowser.com/detect/what-is-my-user-agent")`来检查您的测试的用户代理

<figure>

[![How to set the browser user agent](../Images/3f285c4e164a73dca4862d9be3694503.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LWVYHwZO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gqsugjqchncnkcsks7yy.gif)

<figcaption>How to set the user agent for a test run</figcaption>

</figure>

# 编写自定义报告日志

虽然我们的目标是使 UI-licious 尽可能简单地编写测试，但我们不喜欢在为您的需求编写定制函数和报告日志的灵活性上妥协。为了支持编写定制报告，我们添加了三个日志命令供您使用- `TEST.log.pass`、`TEST.log.fail`、`TEST.log.info`。

以下是如何使用它们:
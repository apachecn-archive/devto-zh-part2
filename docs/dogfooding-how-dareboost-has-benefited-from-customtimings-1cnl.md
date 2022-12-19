# [dog fooding]dare boost 如何受益于自定义计时

> 原文：<https://dev.to/borisschapira/dogfooding-how-dareboost-has-benefited-from-customtimings-1cnl>

我们可能是网络性能专家，但我们仍然没有超能力。我们还需要工具来分析我们的网站和应用程序。因此，当我们发布新的自定义计时功能时，我们很自然地抓住机会在 Dareboost.com 上进行测试……并立即看到了好处。

<figure>

[![The Dareboost web application](../Images/da34b13b8f1343aa1b5a5f41f2ad4404.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rk1C4C84--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x9ismb2g4utuprih6kq8.png)

<figcaption>The Dareboost web application</figcaption>

</figure>

几周前，我们在您的页面分析报告和监视器中引入了一个新功能:[自定义计时](https://blog.dareboost.com/en/2018/05/custom-timings-monitoring/)，定制的标记，使用用户计时 API 放置在 JavaScript 中，然后您可以在 Dareboost 中显示这些标记，以可视化它们随时间的变化。

我们选择优先考虑这个功能，因为我们深信它可以让我们的客户更好地了解他们网站的性能。其实用起来就是这么简单，我们立马就找到了一个满足自己需求的实际应用！

为了解释这一点，我们先来看看 Dareboost 的引擎盖下。

## Dareboost:根据当前用户权限个性化的 AngularJS 应用

任何访问者咨询 [Dareboost 网站](https://www.dareboost.com/)都使用真实的 Web 应用程序。换句话说，用户的浏览器加载 JavaScript 代码，一旦执行该代码，就会接管浏览体验并模拟应用程序的行为，而不是在多个网页之间导航。例如，用户可以从一个屏幕切换到另一个屏幕，而无需重新加载侧菜单。

为了确保网页中的这种应用行为，我们使用了一个 JavaScript 框架:AngularJS。这个框架执行定制操作，使用 XHR 请求(XMLHttpRequest)来检索用户的上下文。

## 羊尾巴晃了两下

我们决定设置几个自定义计时:

*   AngularJS 开始引导时:`angular_boostrap_start`
*   当 AngularJS 被引导时(并开始做我们希望他做的事情来呈现 Dareboost 的接口):`angular_boostrap_end`
*   当我们要求 Angular 获取当前用户的上下文时:`userinfo_xhr_start`
*   当上下文被有效恢复时:`userinfo_xhr_stop`
*   当内容显示在主区域时:`view_content_loaded`

设置这些标记非常简单，以至于有些客户会问我们是否真的只有这些措施可以采取。自己检查一下:一旦你找到了代码的哪一部分对应于你想要监控的行为，就添加这个代码:

```
performance.mark("angular_boostrap_start"); 
```

Enter fullscreen mode Exit fullscreen mode

对于 Dareboost 开始收集这些新数据，我们真的没有什么可做的了:没有特定的配置，没有声明，或者其他任何东西。几天后，我们查阅了我们的监测数据，数据是有的。我们没想到会有这样的结果。

<figure>

[![Erratic timings for the userinfo_xhr_stop mark](../Images/cb218a59314200ca48c9a30e24f5b616.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zO43crZG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/70oz5wyumm5uuikxtqry.png)

<figcaption>Erratic timings for the `userinfo_xhr_stop` mark</figcaption>

</figure>

*事实上，我们做到了，因为我们已经经历了几次显著的减速。但在一个地块上看到它们是另一回事。

## 初等，我亲爱的华生！

这是怎么回事？由事件`angular_bootstrap_start`和`angular_bootstrap_end`标记的 AngularJS 引导阶段非常明显。但是，令人惊讶的是，我们在中间找到了上下文请求。实际上，这是公平的:AngularJS 的上下文请求和加载是异步的，但实际上是相互依赖的。事实上，请求必须等到 AngularJS 加载后才能启动。如果我们希望更好地跟进这一请求，我们肯定需要移动这一标记，以便更好地满足其目的。

接下来是主区域的自定义计时，这让 Dareboost 能够快速响应用户需求，因为他主要关注这个区域。

最后，我们看到了上下文请求响应的定制时间，这是不稳定和糟糕的:上下文恢复几乎需要两秒钟。两秒钟的延迟对于一个忙碌的用户来说是相当长的延迟！分析拒绝开始，潜在的不可用场景(因为他们在菜单中没有这个项目)…我们必须快速修复它。

<figure>

[![Immediate action by the development team solved the problem](../Images/add9c6d818ac15a0132fb487175e1cce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YvOfpI8O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9x71q7qhk2wsnq0j2tbh.png)

<figcaption>Immediate action by the development team solved the problem</figcaption>

</figure>

幸运的是，该团队及时发现了可以解释延迟的后端问题。在一个小版本发布后，情况明显好转:

<figure>

[![This is how Dareboost performs now](../Images/49ea5fe2e4d5b9a42ae0f529f84fc20c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ea3ckfxk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wls8aay4ziwwsgrbzv1r.png)

<figcaption>This is how Dareboost performs now</figcaption>

</figure>

这种情况已经返回到正常行为，主要内容与用户环境的确定同时出现。

## 总而言之

*   **设置自定义计时需要几秒钟**，并且不需要在 Dareboost 中进行任何特殊配置
*   自定义计时是一种新的指标，可以让**了解在**之前看不见的东西。
*   自定义计时特别适合**监控 spa 的性能**
*   鞋匠的孩子并不总是光着脚走！
# 在 rails 中生成授权的短期 URL

> 原文：<https://dev.to/iamcherta/generate-authorized-short-living-urls-in-rails--1cdo>

上周，我的团队在一个项目中接到了一个小任务。我最近不常写那么多代码，但是这个看起来简单明了。

问题很简单，允许客户从我们的 React 前端下载一个动态生成的文件(一个报告)。虽然听起来微不足道，但一些有趣的警告让这个问题变得一点也不简单。

如您所知，spa 使用 AJAX 与后端进行通信，大多数情况下，身份验证/授权是通过某种头部信息来完成的。在创新公司，我们经常使用 JWT，我们很喜欢它。

对于我们的应用程序，用户需要获得授权和认证才能获得报告，但作为 AJAX 请求响应的文件流只适用于 Chrome 浏览器。

问题现在很明显:

> 当涉及身份验证头时，如何使用 AJAX 将内容流式传输到客户机。

## 开始挖掘，停止挖掘

像往常一样，我谷歌了“js 从流生成文件”,因为我认为这是最简单的解决方案，只需抓住我已经为 Chrome 工作的内容，并做额外的工作，使其适用于所有其他浏览器。

也许甚至有一个 npm 包来解决这个问题，但是我很快意识到这不是一个正确的选择，并且把 npm 包扔给问题并不能解决它，相反，它会使阅读变得困难并且容易出错。

## 反思问题

我的问题不是通过 AJAX 传输内容并从这些内容中生成一个文件，而是实际上允许客户端下载一个文件(碰巧是在运行中创建的)，而不会损害应用程序的安全性，也就是说，不会向整个互联网开放一个资源。

考虑到这个新目标，我将文件下载重新设想为一个两步过程。

如果客户端应用程序请求创建文档，并且作为响应，它获得该资源的一个短暂的 URL。

这个想法是:

1.  从 React 请求一个文件“创建”,并获得一个签名的短期 URL。这是一个经过验证的请求。
2.  使用这个 URL，我可以在没有任何额外标题的新窗口中请求报告(因此没有我在 web 应用程序中使用的通常授权)。

关键是在步骤 1 中生成一个 URL，该 URL 在查询字符串中携带一个带有到期日期的令牌，我可以在“开放”端点上检查该令牌(步骤 2)。因此，我寻找一种解决方案，允许我签署数据，并使其在 X 时间后过期，你猜怎么着，JWT 正是这样做的。

唯一的关键区别是，我必须创建一个令牌并通过查询字符串发送它，因为在执行`window.open`时不可能发送头。

## 显示代码

该应用程序之前的代码非常简单，我们发出一个 AJAX 请求，然后用`send_data`流传输文件的内容。授权/认证是通过 Pundit / Knock 在一个`before_action`钩子上完成的。

[![My old rails code](../Images/ea7f9e2d8b05f3be649495ddfa636d3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--spaotNL_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AL95vE78HUv4L1zoevMpcVQ.png)

这在 Chrome 以外的浏览器上不起作用，所以我把这个过程分开，首先创建一个 30 秒的报告 URL，并在这个新的 URL 上提供文件。

[![The new create method](../Images/724f2477c928b39eb3e29ffeedfcaad3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oM74PzXP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A3O8Oig25GO1K_HmEePTMMQ.png)

我们给控制器添加了一个`create`方法。这个方法将负责使用一个签名的 JWT 令牌创建一个短的活 URL，这个令牌将在 30 秒后过期，我还对请求资源的用户 id 进行了编码。

客户端会得到一个类似于这样的 JSON 对象:`{url: "https://domain.com/reports/report_type?token=encryptedtoken"}`。

[![The modified show version](../Images/56b9f96612421205446f13c45e1f42bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--73nJ3bwk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AqDZwbFKHupObMgsvt2KICA.png)

这个节目稍作改变，它跳过了认证，它做的第一件事是用`JWT.decode`解码 JWT 令牌。如果令牌过期,`JWT.decode`将抛出一个`JWT::ExpiredSignature`。然后，如果需要的话，我可以从这个错误中拯救出来，并向我的用户返回一个 403。我也可以在没有令牌的情况下从`JWT::DecodeError`中解救出来。

如果一切都通过了，我知道 URL 是由我签名的，并且在我在 create 方法上设置的`exp`时间内。我可以稍后覆盖 pundit_user 并调用我的授权方法来获得额外的安全层。

## 结论

有了这个简单的想法，我们可以用一种足够灵活的方法来避免重写我们的客户端应用程序的大部分内容，从而拥有经过认证的、短暂存在的 URL。我希望你喜欢这个主意。

* * *

您是否正在寻找一个充满激情的团队来帮助您设想、设计和制造令人惊叹的产品？给我们写封短信。

## 关于运筹帷幄

Ingenious 是一家分布式产品设计和软件开发机构，在乌拉圭的蒙得维的亚和科罗拉多州的丹佛设有办事处，团队分布在五个以上的国家。我们创造产品和构建软件，人们希望用它们来挑战医疗保健、教育和政府等行业领域。
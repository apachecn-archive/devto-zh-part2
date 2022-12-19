# 为什么我们选择 Turbolinks 而不是建造一个水疗中心

> 原文：<https://dev.to/jerodsanto/why-we-chose-turbolinks-instead-of-building-an-spa-21o0>

> 注:*我[本来是在 2016 年](https://changelog.com/posts/why-we-chose-turbolinks)写的这个，今天重读了一遍，还是超级贴切。我们仍在使用 Turbolinks，没有更换的计划。尽情享受吧！*

[Changelog.com](https://github.com/thechangelog/changelog.com)*不是*一个 Rails app，但是*是*一个 [Turbolinks](https://github.com/turbolinks/turbolinks) app。想一想。

这意味着我们没有使用 Turbolinks，因为它是 [Omakase](https://www.youtube.com/watch?v=E99FnoYqoII) 。我们没有使用 Turbolinks，因为我们忘记从 Gemfile 中删除它了。**我们主动选择了 Turbolinks** ，安装了它，并将其集成到我们的应用程序中。我认为这让我们非常独特。

自从[开源网站](https://changelog.com/posts/changelog-is-open-source)以来，我们收到的第一个问题是，**“为什么是 Turbolinks？”** [<sup id="fnref1">1</sup>](#fn1)

这是我们选择它的原因(也许你也应该选择它)。

### 归结为一句话:~~勇气~~务实

人们很容易被新的时尚所吸引。我甚至比大多数人更倾向于这样，因为我们每周都会和《T2》的创作者们谈论他们的最新热门话题。但是，a)跟上开源和 b)把你的车拴在每一匹穿着闪亮新鞋昂首阔步走过的马之间有很大的区别。

我发现，当我消息灵通，但又对趋势保持谨慎时，我作为一名开发人员效率最高。我一直怀疑的一个趋势是事实上的单页应用程序。不要误解我的意思，spa 是许多应用的合法(通常也是最好的)架构。然而，当一个应用程序在不需要的时候被构建成一个 SPA 时，<mark>坏事会发生在好人身上</mark>。

根据我的经验，我们经常会问自己“ ***为什么*** 这个应用程序不应该是一个 SPA？”与其问我们自己" ***为什么*** 这个应用程序应该是一个 SPA？"应用程序的需求应该驱动架构，而不是开发人员的愿望。

当我问自己“为什么新 changelog.com 应该是一个温泉浴场”时，我面临的棘手问题是我们有一个(重要的)功能，它会尖叫:“SPA！”

一个持久的音频播放器，位于页脚，并在您浏览页面时继续播放。

[![persistent audio player action shot](img/45e951a70cb2621095c61806a6a23eb6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lc-njpNr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://changelog-assets.s3.amazonaws.com/changelog-player.png)

**但仅此而已**。没有*我们想象中的其他特征或需求*需要水疗。我真的想分叉代码库，增加我们的 JS 有效载荷，并且<选择一个前端框架< /gasp >以便我们可以有一个持久的音频播放器吗？

我内心的技术专家绝对想这么做。实用主义者知道有更简单的方法。

### 在菱国，事情发生了变化

早在 2012 年，当 Turbolinks [第一次与 Rails 4.0](https://geekmonkey.org/2012/09/introducing-turbolinks-for-rails-4-0/) 一起发布时，它给世界各地的开发人员带来了很多头疼的问题。

有些人认为这完全是个坏主意，但这并没有引起我的共鸣，因为 GitHub 正在非常成功地使用 [pjax](https://geekmonkey.org/2012/09/introducing-turbolinks-for-rails-4-0/) (Turbolinks 的灵感)。

其他人给了它一个机会，直到他们遇到了边缘情况，令人沮丧的错误，以及比宣传的更复杂的心理模型。我掉进了这个营地。

换句话说， <mark>Turbolinks 没有实现它的承诺</mark>，所以大多数人放弃了它，继续他们的生活(有些人嘲笑任何提到它的名字)。但图书馆背后的团队继续努力，一路改进。<mark>四年过去了，Turbolinks 发生了很大的变化</mark>。这里有一个功能和变化的快速列表，可以帮助您快速了解:

*   它经历了对 Turbolinks 5 的完全重写(参见 [Turbolinks Classic](https://github.com/turbolinks/turbolinks-classic) )
*   它不需要服务器端的请求检测或替代呈现
*   它不依赖于 jQuery 或任何其他库
*   它包括一个基于 CSS 的加载进度条
*   当资产发生变化时，它可以重新加载
*   它可以跨页面加载持久化元素
*   可以用 [npm/yarn](https://www.npmjs.com/package/turbolinks) 安装，用 webpack 加载

最后两个要点正是我们所需要的坚持不懈的玩家。如果您在第 5 版发布后还没有看过 Turbolinks，我建议您看看。

### 在 Phoenix 上使用它比阅读它的自述文件花费的时间更少

我错误地认为在 Rails 之外使用 Turbolinks 会很困难。以下是需要付出的代价:

1.  在 [our package.json](https://github.com/thechangelog/changelog.com/blob/master/package.json#L11) 中添加一个依赖项的快速链接
2.  在 app.js 顶部[导入 Turbolinks](https://github.com/thechangelog/changelog.com/blob/master/web/static/app/app.js#L1)
3.  在 app.js 底部[调用`Turbolinks.start();`](https://github.com/thechangelog/changelog.com/blob/master/web/static/app/app.js#L101)
4.  将`data-turbolinks-permanent`添加到[玩家分区](https://github.com/thechangelog/changelog.com/blob/master/web/templates/layout/_player.html.eex#L1)

就这样，我完成了，而且它真的工作了。也就是说，Turbolinks 并不是完全免费的。在编写应用程序的 JavaScript 时，有一些事情需要记住。请务必阅读并理解自述文件中的[构建您的 Turbolinks 应用程序](https://github.com/turbolinks/turbolinks#building-your-turbolinks-application)一节，您应该就没事了。

### 正如一位睿智的资深开发者曾经说过的:视情况而定

我知道，我知道。关于我们应该使用哪些工具和实践的平衡推理并不适合流行的写作，但它确实有助于好的系统。我不是在这里挥手宣布你应该放弃你目前的东西，使用涡轮连接。

我们的应用程序与您的不同有许多原因，其中一些原因使我们非常适合这种解决方案。我会列出一些，这样你就不用生气地给我们发邮件了:

*   最终，我们是一个内容第一的网站。很少有内容网站需要足够丰富的交互来实现 SPA。
*   我们没有太多的 JavaScript 和很少的(面向公众的)表单提交，所以我们还没有遇到 Turbolinks 可能会失败的一些更复杂的场景。
*   随着需求的增长，我们可能会需要客户端路由。我对此高度怀疑，但这是一种可能性。

最终，我们以最少的工程努力实现了目标，并推出了我们的网站，赢得了广泛好评。人们喜欢我们坚持不懈的玩家，我也喜欢 Turbolinks 帮助我们如此沉着地完成任务。如果你已经放弃了 Turbolinks，现在可能是给它第二次机会的好时机。

* * *

1.  第二个问题是，“怎么会这么快？”Turbolinks 有助于这一点，但它主要是仙丹的错。 [↩](#fnref1)

2.  我的第一次水疗是在 2010 年，当时我帮助 Grooveshark 从 Flash 切换到 web 应用。 [↩](#fnref2)
# 不要跟踪

> 原文：<https://dev.to/teej/do-not-track-75g>

我已经把数据分析从我的网站上删除了，但是我想这在将来可能还是有用的。

* * *

当我第一次开始为自己的网站写文章时，我渴望了解人们是如何找到我的博客的，哪些页面最受欢迎，以及如何使用谷歌分析中的实时仪表盘来查看来自世界各地的访问者。

这些东西肯定仍然让我感兴趣，但是随着时间的推移，我并没有真正以一种有意义的方式使用我得到的任何信息。我认为对于其他网站来说，它更有用，尤其是如果你需要运行一些广告，或者它是你收入的一部分，或者是你提供的一项服务，你想看看用户是否遇到任何问题。这是非常重要的东西，我不认为这有什么问题。然而，现在看来，它正在成为一种选择加入的体验，而不是选择退出的体验。

这是一个相当小的网站，因为它是以开发者为中心的，我想很多访问者都启用了某种广告拦截器。我自己也有一个，当然我会对我信任的网站禁用它，但在大多数情况下，它是默认打开的。我最近一直在考虑在我的网站的当前状态下完全取消跟踪，但首先我想看看是否有任何中间地带可以探索。输入`navigator.doNotTrack`。

* * *

我在 MDN 上找到了一些描述如何使用它的文档，不同浏览器的行为也有所不同。在 Chrome 和 Safari 中，这个值默认是`null`，而在 Firefox 中是`"unspecified"`。如果您调用`navigator.doNotTrack`，它返回一个字符串值`"1"`，那么这意味着请求不喜欢跟踪。我将更倾向于选择加入的方法，但是，如果它返回`null`，那么我将启用谷歌分析。您可以在浏览器设置中找到此选项。在 Safari 中，它在隐私栏下，在 Chrome 和 Firefox 中，你可以搜索“不要追踪”的设置。

以下是我目前的检查方式:

```
function hasDoNotTrackEnabled() {
  // some browsers have this in the window object
  let doNotTrack = navigator.doNotTrack || window.doNotTrack

  return doNotTrack === '1'
} 
```

Enter fullscreen mode Exit fullscreen mode

我使用 [ga-lite 包](https://www.npmjs.com/package/ga-lite)来捆绑所有东西，这样我就可以更清楚地控制跟踪是如何工作的。

```
import galite from 'ga-lite'
import { env, hasDoNotTrackEnabled } from './utils'

export const Tracking = {
  gaTrackingId: 'UA-XXXXXXX-X',
  shouldTrack: env() === 'production' && !hasDoNotTrackEnabled(),

  setup() {
    if (!this.shouldTrack) return

    galite('create', this.gaTrackingId, 'auto')
  },

  sendPageView() {
    if (!this.shouldTrack) return

    galite('send', 'pageview')
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

调用是一个我可以使用的实用函数，所以我在本地开发时不运行它。

所以现在当我调用`Tracking.setup()`和`Tracking.sendPageView()`时，它会根据`hasDoNotTrackEnabled`函数的结果来做。

我将继续这样做一段时间，并可能最终在某个阶段完全取消跟踪。只是重申一下，我不反对网站使用某种形式的追踪。这更多的是个人决定。如果到了需要像碳广告这样的东西来帮助支付托管费用的阶段，那就好了，如果我需要得到一个粗略的流量估计，我会重新考虑。说到底，它是一个可以使用的工具，我们不应该羞于利用它(当然，前提是不是出于邪恶的原因)。
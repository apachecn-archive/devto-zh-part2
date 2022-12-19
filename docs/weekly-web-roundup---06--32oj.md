# 每周网络综述- 06

> 原文：<https://dev.to/ardennl/weekly-web-roundup---06--32oj>

这个星期的综述来得比你习惯的晚了一点，抱歉！生活有时会碍事，所以我还没有写时事通讯或网站。我会随时向你汇报。无论如何，让我们看看那些真正推动了一些惊人的东西的人的工作。

*   [第 05 周](https://dev.to/ardennl/weekly-web-roundup---05-1i4j/)
*   [第 04 周](https://dev.to/ardennl/weekly-web-roundup---04-375j)
*   [第 03 周](https://dev.to/ardennl/weekly-web-roundup---week-03-5d74)
*   [第 02 周](https://dev.to/ardennl/weekly-web-roundup---02-1f39)
*   [第 01 周](https://dev.to/ardennl/weekly-web-roundup---01-15d6)

## 新闻

*   Chrome 65 beta 版已经发布，包括 *CSS Paint API* 和 *ServerTiming API* 。我还不完全确定 CSS Paint API 有什么用处，但是 ServerTiming API 可以用来通过 HTTP 头向浏览器传递性能计时信息。
*   谷歌的网络基础在不断改进，现在包括了由[艾迪·奥斯马尼](https://twitter.com/addyosmani)撰写的关于[代码分割和用 Webpack](https://developers.google.com/web/fundamentals/performance/webpack) 优化的章节。
*   JS 开发人员很高兴，因为在...管道。作为一个没有任何其他编程语言背景的人，我从未听说过这个概念。不过，这似乎大大提高了链接多个函数的可读性，我相信还有其他好处。
*   [PWA 即将登陆 Windows 10](https://blogs.windows.com/msedgedev/2018/02/06/welcoming-progressive-web-apps-edge-windows-10/) 。是时候了！
*   Vue.js 现在有一个官方新闻平台，你可以在上面找到关于这个框架的所有最新和最棒的东西。我认为这是一个伟大的想法，如果他们不断更新，这意味着它将像一个“单一的真理来源”一样工作。

## CSS / SVG / Fonts

*   数字设计师[拉斐拉·费罗](https://medium.com/@rafaelaferro)写了一篇[关于 CSS 网格布局](https://medium.com/deemaze-software/css-grid-layout-crossed-sections-fca9e956e725)以及如何开始的伟大文章。
*   Jen Simmons’[Layout Land](https://www.youtube.com/channel/UC7TizprGknbDalbHplROtag?pbjreload=10)视频正迅速成为我最喜欢的科技视频系列。在这一集里，她将带你通过 css-grid，transforms 和更多的东西重新创建一些经典的图形设计布局。[https://www.youtube.com/embed/OxrsO4aIjyc](https://www.youtube.com/embed/OxrsO4aIjyc)
*   设计师兼编码员[凯兹·布里斯](https://twitter.com/kezzbracey)创建了一个关于[手工编码 SVG 图标](https://webdesign.tutsplus.com/tutorials/how-to-hand-code-svg--cms-30368)的很棒的教程。这不仅比您预期的更有趣，而且有助于您理解 SVG 坐标。
*   [我是如何设计 CSS 网格的](https://dev.to/huijing/how-i-design-with-css-grid-4l3o)作者[陈晖靖](https://twitter.com/hj_chen)。关于惠晶如何用 CSS 网格创建布局的非常令人愉快和有启发性的阅读。他强调了 CSS 网格系统中的可视化方面，这可能会使创建可靠且可重用的代码更加容易

## Java Script 语言

*   杰克·阿奇博尔德在 JSConf 上做了一个[的精彩演讲。亚洲 2018](https://www.youtube.com/watch?v=cCOL7MC4Pl0) 。这完全是关于事件循环的，而且相当深入。[https://www.youtube.com/embed/cCOL7MC4Pl0](https://www.youtube.com/embed/cCOL7MC4Pl0)
*   “你有没有经历过试图将状态从反应树的顶端转移到底部的痛苦？”。如果你曾经和 React 一起工作过，你就会确切地知道 [Kent C. Dodds](https://twitter.com/kentcdodds) 在这里说的是什么。幸运的是[有一个“新的”上下文 API](https://medium.com/dailyjs/reacts-%EF%B8%8F-new-context-api-70c9fe01596b) ，在其中你可以使用一个提供者的上下文来传递道具。
*   香草 JS 倡导者克里斯费迪南迪最近发布了 vanillajstoolkit.com T2，这是一个代码片段、助手函数、样板文件、聚合填充、插件和学习资源的集合。我很兴奋克里斯为此创建了一个专门的网站，因为我多年来一直关注他的网站【gomakethings.com，他是我几年前抛弃 jQuery 的精神向导！
*   说到精神向导，阿克塞尔·劳施迈尔博士和他的博客 T2 一直是我理解阶段、提案、TC39 和整个作品的向导。在[中，JavaScript 一团糟——这是一件好事](http://2ality.com/2018/02/js-backward-compatibility.html)他解释了 JS 如何处理向后兼容性，以及我们如何处理 JavaScript 扩展的特性集。
*   Duncan Grant 在 medium.com 的[中总结了他喜欢 vue 的 10 点。当我开始玩 VueJS 时，很高兴读到为什么人们如此喜欢 Vue。](https://medium.com/@dalaidunc/10-things-i-love-about-vue-505886ddaff2)
*   丹·阿布拉莫夫[解释了在 stackoverflow.com](https://stackoverflow.com/questions/48563650/does-react-keep-the-order-for-state-updates/48610973#48610973)如何反应`setState`配料工作
*   Redux explainer 的文章永远不会足够多，Dave Ceddia 的关于在哪里和什么时候用 Redux 获取数据的文章是对我们经常处理的一些事情的详细解释。
*   在 freecodecamp.org 的 ES6 平台上，萨姆·威廉姆斯写了一篇关于[让你的代码更干净、更简短、更易读的文章](https://medium.freecodecamp.org/make-your-code-cleaner-shorter-and-easier-to-read-es6-tips-and-tricks-afd4ce25977c)。
*   [单页应用程序(SPA)不是灵丹妙药](https://blog.bloomca.me/2018/02/04/spa-is-not-silver-bullet.html)是[塞娃·扎伊科夫](https://twitter.com/blooomca)的一个友好提醒，并非所有东西都应该是 SPA，运行 JS 可能会很昂贵。他的例子只显示了第一次加载时下载的文件和数据，但我想这就是重点。如果我只想看一些内容，为什么我要下载并解析一大堆 JS 呢？
*   在全球范围内安装 NPM 模块是很麻烦的。这让我想起了使用 Ruby Gems 和随之而来的依赖地狱。所以[也许不要全局安装由](https://codeburst.io/maybe-dont-globally-install-that-node-js-package-f1ea20f94a00)[杰克·威尔森](https://codeburst.io/@jakobud)在 codeburst.io 上写的 node-js 包
*   GraphQL 是今年你不会停止听到的东西，而且有很好的理由。 [Sacha Greif](https://twitter.com/SachaGreif) 写了[GraphQL 应用中的五个常见问题(以及如何修复)](https://medium.freecodecamp.org/five-common-problems-in-graphql-apps-and-how-to-fix-them-ac74d37a293c)，我发现这对减少 graph QL 样板文件和避免性能问题非常有用。

## 一般

*   今天一篇葡萄牙语的文章！Wendell Adriel 是一名移居葡萄牙的巴西开发人员，尽管他来自一个完全不同的地方，但他在这里的经历和我一样愉快！他还简单介绍了一下葡萄牙的 it 市场、期望薪资以及如何在这里的市场中定位:[葡萄牙的 IT 市场——个人经历](https://medium.com/trainingcenter/mercado-de-ti-em-portugal-minha-experi%C3%AAncia-pessoal-3c01b783e30d)
*   在 css-tricks.com 的[网站雷同](https://css-tricks.com/website-sameness/)中，克里斯·科伊尔就当今有多少网站雷同提出了一些很好的观点。虽然我知道很多令人惊叹的网站设计，但我遇到的大多数设计都是 bootstrap 的变种。有了 CSS，我们就有了所有的工具来创造坚实而独特的设计。为什么不呢？Bootstrap 不可能是最终的网页设计，不是吗？
*   我有点喜欢美味大脑的博客。WordPress 插件开发公司已经创建了一段时间的稳固的博客帖子(更不用说稳固的插件了)，涵盖了各种主题。他们总是非常深入和诚实。这就是为什么我喜欢这个 [2017 年回顾](https://deliciousbrains.com/2017-year-in-review/)，它非常诚实，显示了经营和发展一个企业有多难。
*   由[凯文·鲍尔](https://twitter.com/kbal11)开发的前端复杂性的增长性质正是你所想的那样。尽管如此，它仍然非常值得一读，因为它既包含了巨大的机遇，也包含了复杂的因素！
*   当你是一个“数字游牧者”时，世界就是你的办公室——这是一幅黯淡但就我而言准确的数字游牧图景[凯尔·查卡](https://twitter.com/chaykak)

## 灵感

*   Mandy Kerr 发表了一篇关于创意文本效果的精彩演讲@ SingaporeCSS。她的视频里有一大堆很酷的想法，浏览一下就让我想尝试一下。尽情享受吧！[https://www.youtube.com/embed/9EU7urOl1LE](https://www.youtube.com/embed/9EU7urOl1LE)T3】
*   luuuge.fr 是一个推广音乐专辑的迷你网站。有一些冷音乐、精美的艺术品和富有弹性的 [popmotion](https://popmotion.io/) 动作。由[马克西姆·勒·布勒东](https://twitter.com/maximelebreton)创作
*   [lesfillesdu9novembre.com](http://lesfillesdu9novembre.com/)，一个超级酷炫流畅的作品集网站。
*   严重地..OMG [https://codepen.io/Yakudoo/full/prYmKj/%20?height=500&default-tab=result&embed-version=2](https://codepen.io/Yakudoo/full/prYmKj/%20?height=500&default-tab=result&embed-version=2)
*   视差有意义的时候:

    > ![](../Images/b3496bfaee53bf0b2fa950b6af364bae.png)萨查格雷夫@萨查格雷夫![](../Images/4d9c44713c216584b3d48ff3455cbb68.png)在这家日本抱石健身房的网站上真正酷的使用滚动动画:[mushrooming.co](https://t.co/ZkdDvxh4Jx)03:30am-2018 年 2 月 10 日[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=962166832697241602)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=962166832697241602)2[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=962166832697241602)9T30】

## 战略情报局

*   [通过@NikkitaFTW](https://github.com/SaraVieira/styled) 预动作样式化模板
*   [感谢@feross](https://github.com/feross/thanks)
*   [脸书的 OSQuery](https://osquery.io/)
*   一个类似 JSON 的数据结构，可以被不同的用户同时修改，并自动合并。

## 公共服务公告

*   这是个好主意吗？

    > ![](../Images/63941b33e0cf4ab8f39791807c467713.png)![](../Images/9da0698fedd18ceea92c0514249f4915.png)Shawn swyx 王【T8@ swyx![](../Images/4d9c44713c216584b3d48ff3455cbb68.png)你大概不需要`开关'语句:00:36am-04 2018[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=959948647273259008)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=959948647273259008)1[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=959948647273259008)6

*   `aria-label` doesn't play nice with automatic translation services:

    > ![](../Images/8d5aca72b3aa3baea14e1671b8f19830.png)Stefan Judith@ Stefan Judith![](../Images/4d9c44713c216584b3d48ff3455cbb68.png)Hmm-never thought of these ... "aria-label" is not suitable for automatic translation service (because it will not be translated). "aria-labelledby" or "visual hiding" class is a better choice. "aria-label is xenophobia" by
    > [@ heydonworks](https://twitter.com/heydonworks) . [# A11Y](https://twitter.com/hashtag/a11y)February 05, 2018 at 16: 30 PM[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png) ](https://twitter.com/intent/tweet?in_reply_to=960551096333819906) [ ![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png) ](https://twitter.com/intent/retweet?tweet_id=960551096333819906) 5 [ ![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=960551096333819906)

*   I must admit that I had no idea!

    > ![](../Images/8706e6fb8ba89ac39a1d26bc37d8d75c.png)Wesbos@ Wesbos![](../Images/4d9c44713c216584b3d48ff3455cbb68.png)Til Nesting an input in a tag is better than just using an ID
    > 
    > Auxiliary equipment coverage rate is therefore:
    > 
    > < label for = "first" > name: < input id = "first"/> </ Label >
    > 
    > is more than
    > 
    > < label for = "first" >

*   > ![](../Images/6d9040bf97df42491d4fba0781e14e10.png)![](../Images/68f15a1f61f9fb22e8c6cb1e43e76c97.png)埃里克劳伦斯🎻@ ericlaw![](../Images/4d9c44713c216584b3d48ff3455cbb68.png)TIL:这种状态叫做 tablerone .2018 年一月 23 日 21 点 32 分[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=955916279155691523)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)T25】541](https://twitter.com/intent/retweet?tweet_id=955916279155691523)[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=955916279155691523)1667

*   新版本的 Vue CLI 看起来蛮 rad

    > ![](../Images/f6d35734e4c875b496578af990997358.png)![Play butt](../Images/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop=""><source src="https://video.twimg.com/ext_tw_video/961253457020600322/pu/vid/320x180/T1JEvxW0y--I8aX9.mp4" type="video/mp4"></video>![](../Images/637a339279abeeed271b711ec4511349.png)萨拉·德拉斯内尔@萨拉 _ 埃多![](../Images/4d9c44713c216584b3d48ff3455cbb68.png)杜乌德新版本(3)的 Vue CLI 令人惊叹✨
    > 快来看看吧,我可以使用默认,或者从一堆选项中选择,不限于:
    > -打字稿
    > 2018 年 2 月 07 日下午 15:01[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=961253706715942912)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=961253706715942912)299[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)T39】1029](https://twitter.com/intent/like?tweet_id=961253706715942912)

*   This!

    > ![](../Images/546e1876a48a0f5ac7bd96f79eb9be92.png)Whitney Williams@ Whitney Hacks![](../Images/4d9c44713c216584b3d48ff3455cbb68.png)What does the company do specifically?
    > Me: I know CSS specifications by heart, and I can code flexbox layout correctly in the first attempt.
    > Company: But do you know JavaScript? [T15】 ... 【T16] As an industry, it's time for us to start accepting that front-end is more than JS. [twitter.com/meyerweb/ Statu …](https://t.co/95LDwwqdoo)November 12th, 2017 at 06: 13 AMEric Meyer @ Meyer[[T30](https://twitter.com/intent/tweet?in_reply_to=929593000107196417)

*   I *need* to play this!

    > ![](../Images/c046bcb4a427e691f49e745a5b3b242b.png) ![Play butt](../Images/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop=""><source src="https://video.twimg.com/tweet_video/DVhOm_2XkAA0P36.mp4" type="video/mp4"></video> ![](../Images/b9d7fa2be017addddc39700139ae5491.png)Sebastian Close Kevic[T11 [# carrion game](https://twitter.com/hashtag/carriongame) [# indiedev](https://twitter.com/hashtag/indiedev) [# game dev](https://twitter.com/hashtag/gamedev) [# Monogamy](https://twitter.com/hashtag/monogame)February 08, 2018 at 14: 19 PM[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png) ](https://twitter.com/intent/tweet?in_reply_to=961605346518355971) [ ![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png) ](https://twitter.com/intent/retweet?tweet_id=961605346518355971) 45 [ ![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png) T43] 2008](https://twitter.com/intent/like?tweet_id=961605346518355971)

## 我在做什么？

在葡萄牙拿撒勒享受美丽的风景！

[![Sunset at Nazaré](../Images/c53510f898d56b99b3e38640b3a31896.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gn9ySEPh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/85173yciyzjam8d9ubul.jpg)

祝你一周愉快！
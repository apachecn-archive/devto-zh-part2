# 前端开发的下一件大事是什么？

> 原文：<https://dev.to/stereobooster/what-is-the-next-big-thing-in-frontend-development--4nm6>

> Joshua Earle 在 Unsplash 上拍摄的照片

我对这篇文章有过一些想法，但不确定如何构思。这条推文帮助了我

> ![Christoph Nakazawa profile image](../Images/0bc586c49e14365411b4b44add12c617.png)克里斯托夫·中泽友秀[@ CPO jer](https://dev.to/cpojer)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)我们能从前端开发中得到什么让你更开心？2018 年 9 月 13 日上午 08:56[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1040162175216693249)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1040162175216693249)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1040162175216693249)

## 亚秒构建时间

您见过 OCaml(您可能知道它是 ResonML 或 BuckleScript)编译器运行吗？它以二进制形式缓存所有内容，因此后续构建大约需要 20 毫秒。这完全令人吃惊。迫不及待地想看到 JS 这样的东西。

也许[https://pax.js.org/](https://pax.js.org/)？谁知道呢...

## 自动代码拆分

> ![Jamie K 🏳️‍🌈 profile image](../Images/5a1e084ac37788fbfef751283e242bb2.png)杰米·🏳️‍🌈[@ Jamie builds](https://dev.to/jamiebuilds)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)我们将在 package 2 中运行的大实验并没有赋予您手动定义代码拆分的权力。你只需要写你的代码。相反，捆绑器会通过一个插件系统为你做所有的决定。
> 
> 社区能否让电脑打败人类，我们拭目以待。2018 年 7 月 06 日下午 17:44[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1015290488012722177)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1015290488012722177)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1015290488012722177)

[相关包裹 RFC](https://github.com/parcel-bundler/parcel/issues/885)

这将很好地与反应悬念配对

> ![Dan Abramov profile image](../Images/8410abe414a98a3109390b1a1f7377ee.png)丹·阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)[@ wycats](https://twitter.com/wycats)[@ TheLarkInn](https://twitter.com/TheLarkInn)[@ slightly late](https://twitter.com/slightlylate)[@ AdamRackis](https://twitter.com/AdamRackis)[@真诚 _ 甘特](https://twitter.com/sincerely_tegan)这意味着你可以在任何时间点进行更多的代码拆分——比如加载 modal header——而无需您已经有一个占位符。在尝试呈现模式时，它将等待 ModalHeader 和 Autocomplete。2018 年 9 月 13 日上午 01:55[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1040056401484304385)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1040056401484304385)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1040056401484304385)

## 按浏览器构建

@sitnikcode 开始了这场名为 [browserslist](https://github.com/browserslist/browserslist) 的讨伐(如果我说错了请纠正我)。想象一下，您指定想要支持哪个浏览器，编译器(transpiler)将为列表中的每个浏览器提供构建，其中包含所有必需的 polyfills 以及对 CSS 的所有必需更改，您可以使用 Lambda@Edge 或 Cloudflare-Worker 中的小脚本在 CDN 边缘提供适当的资产。或者，如果您的代码对于给定的浏览器是不可编译的，编译器将会失败。

此外，如果能把它编译成 ES6 就更好了

> ![Rich Harris profile image](../Images/bb52b451f45377440ef4090bbb10ccda.png)Rich Harris@ Rich _ Harris![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)解释:ESM 让我们少了很多代码(从 webpack 切换到 Rollup 减少了**25%**)的 JS 有效载荷，70%的用户可以根据 caniuse 原生使用模块(包括动态导入())。剩下的 30%我们使用 Shimport—[github.com/Rich-Harris/sh…](https://t.co/Feu8YgoTOF)2018 年 9 月 08 日下午 15:58[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1038456514426687488)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1038456514426687488)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1038456514426687488)

作为参考，下面是每次浏览的构建看起来如何像 [preact-hn webpack 配置](https://github.com/kristoferbaxter/preact-hn/tree/master/config)

## 你梦到什么？

你最期待的事情是什么？
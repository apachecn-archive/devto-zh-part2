# 如何创建一个盖茨比启动器

> 原文：<https://dev.to/emasuriano/how-to-create-a-gatsby-starter-42m8>

<figure>[![](../Images/7b99918c39a6d2e8651cd75c106c85f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ILB0U3q---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9Mntlltb4s_NTKQNNMfz9Q.jpeg) 

<figcaption>照片由[杰德欧文](https://unsplash.com/photos/PpzBE1hWeGI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/suitcase?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

几周前，我发表了我的第一部《盖茨比》，我想分享一下我从这段旅程中学到的东西。

和《盖茨比》中的许多人一样，它是从个人投资组合开始的。在我发布并在 Tweeter 上分享之后，人们开始问我这是不是开源的，我说“嗯，是的🤔".

> ![unknown tweet media content](../Images/46b6f176a064bc5c05a8b48fa70abf5c.png)![Ema Suriano profile image](../Images/3187553067f3bd3c48742091f190d815.png)Ema 苏里亚诺[@ emasuriano](https://dev.to/emasuriano)![twitter logo](../Images/03bb33b910d947ffb5d9c56b0a509ab2.png)经过日复一日的编码和设计，我用 rebass(styled-components system)[@ gatsbyjs](https://twitter.com/gatsbyjs)v2 和 [@contentful](https://twitter.com/contentful) 完成了我的网站的第二版🎉这是对我现有产品的全面重构，但我对结果真的很满意😁【emasuriano.com】2018 年 8 月 26 日 22 点 03 分[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1033837348881137665)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1033837348881137665)11[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1033837348881137665)94

从技术上来说，所有代码对 Github 的每个人都是开放的，但它根本不可配置(很多很多关于我的硬编码信息)。因此，当你不得不猜测在哪里修改代码时，这不是帮助开发人员，而是一种可怕的体验。

就在这时，我开始考虑创建一个 Gatsby starter 来简化页面的设置。我的目标是用定义好的步骤完成 *2 分钟的设置*，所以我必须做很多工作来使*可配置*以及*可扩展。*

### #1。让它对开发者有吸引力💅

[https://medium . com/media/38563052 ed 7 a 5b 0 abb 5a 59135 ca 9 e 175/href](https://medium.com/media/38563052ed7a5b0abb5a59135ca9e175/href)

你可以把你的 starter 想象成你正在卖给开发者的一个页面(你不会得到任何钱，因为这不是开源的重点)。所以你必须让它成为有吸引力的✨，为此我遵循以下三条规则:

> 让它工作。
> 
> 动作快点。
> 
> 做得漂亮点。

***让它工作*** 是为了让你的应用程序实际运行，没有任何错误或异常。幸运的是，我有这个规则，因为我的页面已经正常工作，没有任何问题。

做到这一点后，一个非常好的技巧是列出你实现的所有特性。因此，开发人员无需阅读任何代码就可以快速浏览它的内部内容。👍

***让它更快*** 留下来构建一个整体性能良好的应用。在盖茨比身上实现这一点并不难，因为盖茨比已经跳出了框框🔥但是，您仍然可以编写性能非常糟糕的代码。

为了衡量我的应用程序的性能，我使用了 Google Lighthouse。它为您提供了一份报告，其中包含您的应用程序不同方面的统计数据，如性能、可访问性、PWA 等。👍

**，*这个规律可以用这句话来解释:*

 *> 如果某个应用程序看起来不好或者不支持我的屏幕，我就不会使用它。

对，就是这么简单！你必须提供一些看起来对其他人有吸引力的东西，也是有反应的，这是我的观点，在当前的网络发展状态下是必须的。

如果您正在使用 React，一个很好的提示是选择一个带有内置组件的设计系统。这将节省您大量的时间，因为有一个完整的团队花时间来考虑如何间距应该，调色板，字体，响应和许多许多更多！👍

### #2。不要硬编码个人信息🤷‍♂️

当我查看我的页面时，我可以很容易地发现我的组件和信息混淆了。一个简单的例子是“关于我”页面，看起来像这样:

<figure>[![](../Images/02c7b7fb9d71d91e0f4983abd1b63f6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mmdZcPEm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXCVR6iCsSAMOvP6Ypo-z_A.png) 

<figcaption>旧约我的片段章节</figcaption>

</figure>

解决方案是“*简单*”，所有的信息都必须来自其他资源，幸运的是，在盖茨比身上做到这一点非常简单！

我决定用一个 CMS 来存储我所有的数据，然后显示在启动器上，在这种情况下，我选择了*，我真的很满意😃。*

 *第一步是创建一个 ContentModel，在这里我可以声明关于投资组合所有者的数据的形状。

<figure>[![](../Images/230f61e5f82633a47ed5615389789607.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ioGzx-30--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AfO6SDQeQlxjZMHTnnPzkMg.png) 

<figcaption>内容模式为</figcaption>

</figure>

关于我

之后，我需要通过创建一个内容来用数据填充我的内容模型。我这样做是为了提供一个例子，当你填写所有的信息时，它应该是什么样子，而且如果没有数据，开始是完全空的😂

<figure>[![](../Images/f1069083ee19816361b89a51760a4a1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mw_FQRSb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABvmjDD5BaFoZbl6OdBgwwA.png) 

<figcaption>关于我的新片段</figcaption>

</figure>

通过这样做，想要改变关于他自己的信息的人不必查看组件结构来找到图片被安装的位置。只需简单地进入*内容丰富的*并改变图片的资产，这就是✨

### #3。提供快速设置🚀

如果你想鼓励人们开始使用它，这是至关重要的一步。作为维护人员，您必须提供简单的步骤来启动和运行启动器。

此外，设置可能会根据您选择构建的启动器而改变。在我的例子中，当我使用 *Contentful* 作为数据提供者时，我必须解释如何轻松地将我所有的内容模型和内容迁移到另一个人的空间。

**快速提示:**记住添加 gatsby new 命令作为设置的第一步。这是文档建议的方法，也比克隆回购要好。

所以我为我的初学者定义的步骤是:

*   盖茨比新伴侣-作品集
*   纱线设置
*   纱线显影

在介绍一个通常不存在的新步骤时，尽量具体，在我的例子中是纱线设置。此外，我粘贴了如果一切顺利，控制台的结果应该是什么样子。

### #4。把它加入盖茨比的官方首发名单📦

这可能是最简单和最值得感激的一步，基本上就是正式的了！

对于任何不知道的人来说，盖茨比在其网站上有一个所有首发的[列表，人们(包括我在内)去那里寻找首发。这一步就像为你的网站注册一个域名，这样它就可以被谷歌的用户发现。](https://www.gatsbyjs.org/starters/)

添加启动程序的方法是打开一个对 Gatsby 存储库的 pull 请求，然后编辑 starters.yml 文件，您可以在其中编写启动程序的简要描述和 URL。

```
- url: Link to a demo of your starter
 repo: Link to GitHub repo
 description: Your starter description

# These correspond to the category filters in the library
 # Make an effort to use the existing tags, and add more if needed!
 tags:
 - Redux

# Add your site features
 # These will be included on your starter's detail page.
 features:
 - Blog post listing with previews (image + summary) for each blog post 
```

合并您的拉取请求后，您将能够看到 showcase 的一个新条目。在这里你可以看到我的！

盖茨比的首发队友

最后一件事，当维护者接受你的请求时，你将成为 Gatsby 社区的一员！盖茨比向任何贡献者提供“免费赠品”(他们有一个商店，你可以免费挑选一些东西并送到你家)，这是向开发者表示感谢的最好方式，非常感谢盖茨比🎉

我强烈建议你去看看 [Spectrum 社区](https://spectrum.chat/gatsby-js)，这是一个有着优秀开发者的论坛，所以你可以问任何问题，也可以回答。

就是这样！这是我构建 Gatsby starter 的经验，希望对您有所帮助。如果你也建立了一个，请在评论中分享你的经验😀**
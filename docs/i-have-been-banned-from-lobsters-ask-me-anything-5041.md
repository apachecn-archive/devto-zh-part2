# 我被禁止上龙虾了，问我什么都行。

> 原文：<https://dev.to/shamar/i-have-been-banned-from-lobsters-ask-me-anything-5041>

首先，我要说的是 [Lobste.rs](https://lobste.rs) 是一个**伟大的社区**，我参与了一年多。几个非常聪明的家伙在那里等着，我和他们就操作系统设计、编程语言、人工智能和机器学习、安全、隐私等等进行了很好的交谈。

我也试着成为这个社区中有建设性的一员，在那里张贴我遇到的有趣的文件。

**注意**在上面的网址中，两个标有“**【故事被原投稿人删除】**”的投稿，在我的禁止下，已经被管理员删除。

我没有移除它们。我没什么好隐瞒的。

一个是[我最近的一篇文章，记录了一个漏洞](https://dev.to/shamar/the-meltdown-of-the-web-4p1m)，它可以让你访问的任何网站进入你的私人网络(绕过许多公司的防火墙和代理)。

另一个是[我写给 Mozilla](https://bugzilla.mozilla.org/show_bug.cgi?id=1487081) 的相关 bug 报告(然后向 Chromium too 报告了[),之后才披露了这样的概念验证漏洞。](https://shamar.github.io/documents/Mozilla-Bug1487081-Attachments/ChromiumBug879381.html)

在这些提交之后，出现了一些问题，因为尽管 Lobste.rs 被 Mozilla 安全开发者建议作为继续讨论我报告的 HTTP/JavaScript 漏洞的地方，但没有人回答我的问题“[火狐用户容易受到这种广泛的攻击吗？](https://shamar.github.io/documents/Mozilla-Bug1487081-Attachments/Undetectable_Remote_Arbitrary_Code_Execution_Attacks_through_JavaScript_and_HTTP_headers_trickery__Lobsters.html#c_i5j37u)”。

然而，我遭到了如此多的否决，以至于一位管理人员(在 8 月 30 日第一次写信给我之后)认为我不适合这个社区的文化。

禁令的官方理由是:“持续的对抗行为和没有改善的希望”。

现在让我们说清楚，我对彼得的决定没意见，即使我不同意。**你的服务器，你的规则**。

但是我认为我的禁令是统计数据滥用的一个很好的例子。

事实上，从我收到彼得的第一条私信开始，他就让我解释为什么我被否决的几率比平均水平高出 18 个(后来是 22 个)标准差。

请注意，我也获得了足够多的赞成票，在我的大多数评论和帖子中获得了积极的排名，但他只是孤立地**关注着反对票。**

作为一个知道如何在统计数据上撒谎的人来说这有点味道，但由于我的私人解释不够充分[，我仔细解释了](https://lobste.rs/s/pnfmzr/google_certbot_letsencrypt#c_o2zvx2)这些否决是如何不符合股东自己关于否决的指导方针的(抱歉，由于否决，你必须展开[这个评论](https://lobste.rs/s/pnfmzr/google_certbot_letsencrypt#c_s8oksi)来查看[解释](https://lobste.rs/s/pnfmzr/google_certbot_letsencrypt#c_o2zvx2))。

要了解我的不良行为，你可以看看我最近在 Lobste.rs 上的评论(有些评论已经被审查，但 Peter 已经友好地发给我一个 CSV，其中包含数据库的完整导出)。

这里有一些缺失内容的例子(注意，只有 18 岁以上！:-D):

> > 我对安全浏览的事情感到很不安。
> 
> 对于大多数人(那些使用 WHATWG 浏览器如 [Firefox](https://bugzilla.mozilla.org/show_bug.cgi?id=1487081) 、 [Chromium](https://shamar.github.io/documents/Mozilla-Bug1487081-Attachments/ChromiumBug879381.html) 、IE/Edge 以及衍生的如 [Tor 浏览器](https://www.torproject.org/docs/faq.html.en#TBBJavaScriptEnabled)、Safari 或 Google Chrome) [的人来说，并不存在“安全浏览”](https://dev.to/shamar/the-meltdown-of-the-web-4p1m)。
> 
> 我的意思是:如果你访问的任何网站可以进入你的私人网络或检查你的缓存，如果你访问了某个页面...或将非法内容上传到您的硬盘...称之为安全是相当误导人的！
> 
> HTTPS 通过减少 CA 的潜在攻击者和那些能够访问证书的人的数量来保护用户免受某些威胁(无论如何，这是一个变化的和大量的人，如果你考虑 CDN 或定制 CA，你可能必须在你的工作 pc 上安装)。
> 
> 至于这是反竞争的...也许吧。
> 
> 但是这里的一些问题根源于版权保护，所以...这可能只是在信息技术出现之前设计的法律体系的众多问题之一。

[T2`see in context here`](https://lobste.rs/threads/Shamar#c_s8oksi)

> 注意:**每个执行 JavaScript 和支持 HTTP 缓存控件头的浏览器**都同样容易受到攻击。

[T2`see in context here`](https://lobste.rs/threads/Shamar#c_qtjegw)

> 我非常担心 IT 人员的这种态度。
> 
> 我的问题很简单，有一个布尔答案。
> 
> bug 报告中描述的**攻击**是否可能？

[T2`see in context here`](https://lobste.rs/threads/Shamar#c_22ksxd)

> > 好吧，我会咬。
> 
> +1!我是意大利人！我很好吃！；-)
> 
> > > > Bugzilla 不是一个讨论论坛。
> > > 
> > > 事实上，这是一份错误报告。
> > 
> > 啊，这就是我们不同意的地方。我知道 bug 是一个模糊的概念。这就是为什么我们有 Bugzilla 礼仪，它也包含了一个指向 Mozilla bug 编写指南的链接。
> 
> 我对网络礼仪很认真，在写报告前我检查了你的。
> 
> 如果我违反了你的一条礼仪规则，我非常抱歉，但是老实说，[我看不出是哪一条。](https://bugzilla.mozilla.org/page.cgi?id=etiquette.html)
> 
> 甚至关于 [Bug 写](https://developer.mozilla.org/en-US/docs/Mozilla/QA/Bug_writing_guidelines)我都尽力了，我到底错在哪里？
> 
> 请注意，这不是一个单一的 RCE，而是一个完整的类别。
> 
> 问题不仅仅是 JavaScript 攻击本身，而是他们可以删除所有证据的事实。
> 
> > > > 此外，你寻求讨论的内容并不特定于 Mozilla 或 Firefox。
> > > 
> > > 没错。其他几个浏览器也受到影响，但是:
> > > 
> > > *   这并不意味着这不是火狐的一个错误
> > > *   作为一个“为人而建，不以盈利为目的”的浏览器，我想你对这个话题更感兴趣。
> > 
> > 请详细说明，我不确定你想暗示什么。
> 
> 作为 0.8 版本的火狐用户(和“传道者”)，我知道 Mozilla 是一个关心人的品牌。
> 
> 就连你用的这个词，“人”而不是“用户”，对我来说也一直是励志的。
> 
> 现在，这个问题特别危险，因为不是所有的人都生活在同样的法律下。
> 
> 因此，我认为(也希望)Mozilla 比其他受利润驱使的浏览器厂商更关心这些人的安全。
> 
> > 我同意@[calla had @ wandering . shop](mailto:callahad@wandering.shop)马上说的话:如果你浏览一个网站。它给你 JavaScript。浏览器执行它。那是设计好的！现在 web 被 W3C 和 WHATWG 指定为应用平台。你必须接受网络不再是超文本的事实。
> 
> 我在这样的应用平台上工作了 20 年，我想我已经非常了解了。
> 
> 重点是这样的应用平台是否在设计层面被**攻破。**
> 
> > > > 这不是火狐的 bug。
> > > 
> > > 你是说这些袭击是不可能的吗？
> > 
> > 我是说这不是火狐特有的，而是浏览器固有的概念。
> 
> 抱歉，我再问一次，但我很笨。
> 
> 在 Firefox 中， [bug 报告](https://bugzilla.mozilla.org/show_bug.cgi?id=1487081)中描述的攻击可能发生吗？

[T2`see in context here`](https://lobste.rs/threads/Shamar#c_hd61dm)

这只是一个示例，但是如果你发现其他你感兴趣的被审查的内容，请随时提问。

* * *

现在，我仍然认为 Lobste.rs 是一个很棒的技术社区，你真的应该加入他们。甚至彼得也是一个好的管理者:他只是犯了一个错误。

但是我自己也是一个数据科学爱好者，所以请随意问我一个真正的巨魔如何通过否决其他人来欺骗这样的指标。或者为什么如果你不关心网络积分(也不试图最大化它们)，你显然会失去很多积分。

或者很好...问我别的！:-D

我不是 Mozilla 安全部门的。

我来回答。我是黑客。

[![](img/809d36e9481dc228fe32b68eaca7f719.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8lohbV5V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.mastodon.social/media_attachments/files/006/053/256/original/0e2a898b01052765.jpeg)
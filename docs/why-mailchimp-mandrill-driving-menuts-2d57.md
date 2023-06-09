# 为什么 mailchimp-mandrill 让我发疯？

> 原文：<https://dev.to/amrelgarhy/why-mailchimp-mandrill-driving-menuts-2d57>

我最近在几个项目中使用了 mailchimp 和它的 API mandrill。

这两个项目都需要发送相对复杂的电子邮件，例如给客户的确认、详细的发票和账户总数，这需要强大的电子邮件模板引擎来允许发送对象列表，并让模板本身遍历对象并显示它们。

我找到了 Mandrill 和他们的模板引擎，你可以设置它来使用手柄。js 非常强大，非常适合我的需求。

因为 mailchimp 模板设计编辑器是健壮且易于使用的，我们开始在 Mailchimp 中构建模板，然后推送到 mandrill 以从我的项目中调用。

直到此刻，一切看起来都很完美，没有任何问题。

但是，接下来发生的事情是这样的:

我想删除一些默认情况下 Mailchimp 模板设计者添加到模板中的代码:[https://stackoverflow.com/a/46185879/20126](https://stackoverflow.com/a/46185879/20126)

所以我去了 Mandrill 模板编辑器，(它没有 UI，你将需要更新纯 HTML)并在那里更新了代码。一切都很好，但现在我不能将模板推回 Mailchimp，它们之间再也没有同步的方法了，因此我现在有了同一个模板的两个不同版本，Mailchimp 上的原始版本(我不再推给 Mandrill)，Mandrill 新的更新版本。

这背后真正的痛苦是，与设计丰富的 Mailchimp 模板编辑器相比，Mandrill 电子邮件模板编辑器非常糟糕。

但是现在我没有办法回到 Mailchimp，我被困在山魈 [![🙁](img/88488570268374aac0b940e1bc857958.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N109eHMv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f641.png)

Mailchimp:请你将需要更多的集成函数与 Mandrill 看起来像一个应用程序。
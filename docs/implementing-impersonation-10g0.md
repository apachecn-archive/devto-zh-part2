# 实现模拟

> 原文：<https://dev.to/ideasasylum/implementing-impersonation-10g0>

作为您的用户之一登录的能力是您可以开发来支持客户的最有价值的功能之一。

作为您的用户之一登录的能力是您可以开发来支持您的客户的最*危险的*功能之一。

随着简洁的介绍的结束…

不，实际上，让我们倒回去一分钟，因为我不确定你是否已经完全意识到你将要做的事情:**你正在你的应用**中制造一个安全漏洞。

如果你的 app 是圣盔谷(Helm's Deep)，那么冒充用户就像是加了一个小的不设防的涵洞，绕过了主要防御工事。你应该期待 works…并添加适当的防御。

[![Helmsdeep](img/e49631180294025d4844d870677cbf56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fv4omErx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jamie.ideasasylum.cimg/helmsdeep.gif)

还不怕？哦，也许你听说过脸书？是的，你即将愉快地实现的这个功能[昨天导致了 9000 万个受损账户](https://www.theregister.co.uk/2018/09/28/facebook_accounts_hacked_bug/)。

所以，你现在害怕*吗*？很好。你可以继续。

* * *

实现这样一个特性需要考虑很多事情，技术细节可能是最没意思的。它们在应用程序、框架和语言之间也有很大的不同。

从技术上讲，以另一个用户的身份登录可能就像`session[:current_user] = user.id`或类似的事情一样简单。随便啦。你可能知道这是怎么回事。

作为另一个用户登录并不难。

这里有一些更重要的事情需要考虑:

*   用户需要给支持人员显式的权限来模拟他们吗？
*   谁有权模拟用户？
*   您是如何认证支持人员的？
*   模仿会持续多久？
*   模仿者如何知道他们在模仿另一个用户？
*   需要避免哪些无意的影响？

## 获得许可

这可能不是每个应用程序都需要的，但是如果你正在处理敏感或财务数据，你可能需要在查看他们的帐户之前征得用户的同意。我见过 FreeAgent 将此实现为在用户设置中可见的特殊代码，必须直接提供给支持人员

[![FreeAgent support access code](img/8ee1456b0ee73133d372e34585f9b92a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DJdUjSml--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jamie.ideasasylum.cimg/freeagent-support-access.png)

用户还可以选择加入/退出，允许支持人员访问他们的帐户。

## 谁可以冒充用户？

这实际上是一个公司内部流程，但是您应该清楚谁可以和不可以模拟用户，在什么情况下，出于什么目的。您可能希望您的支持人员模拟用户，以便他们可以修复/调试正在发生的问题。你可能不希望你的销售人员出于无聊的好奇心冒充用户。

我之前建立的一个特性是某种形式的责任。您可以在数据库中构建一个审计日志，记录支持人员每次模拟用户的情况。就我个人而言，我认为审计日志对于分析发生后的滥用行为非常有用，但却没有起到威慑作用。相反，我认为良好的行为可以通过公开宣布冒名顶替来强制执行——每次有人被冒名顶替时都在一个空闲频道上发帖是确保问责的简单方法。

## 认证

既然您的管理员帐户是每个用户帐户的后门，那么是时候再次检查它们的安全性了。

首先，我认为拥有独立的`Admin`和`User`模型很重要，这是避免特权提升攻击的最简单方法

[![You can't escalate a privilege if there's nothing to escalate](img/c3e70a76b2753bad805005a0af92ecd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6smXkhbi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jamie.ideasasylum.cimg/you-can-t-escalate-privilege.jpg)

接下来，我们应该确保它真的是一个模拟用户的管理员。但是我们不是应该检查他们是以管理员身份登录的吗？

哈！呃…不。如果您的支持人员笔记本电脑被盗，会发生什么情况？或者他们重复使用了一个密码？您需要另一种方法来验证它是否真的是管理员用户。一种第二密码…也可以说是双重认证。2FA。提示:只需使用[https://www.twilio.com/authy](https://www.twilio.com/authy)生成并确认一个确认码。这非常简单，最多需要几个小时。

这可以确保登录的管理员帐户是由您认为的员工操作的。

## 冒名顶替持续多久？

一个相当常见的问题是，当你在周五模拟一个用户，然后在周一打开应用程序，却忘记你已经登录到该用户的帐户。希望你在做任何事情之前能及时意识到…永久的，比如用错误的账号发简讯。

一个简单的解决方案是比普通的会话 cookies 更快地终止模拟。如果您的用户会话通常持续 30 天，我会将模拟的会话超时减少到 1 小时左右。

## 管理员如何知道他们在冒充另一个用户？

即使您限制了持续时间，您仍然希望显示一些他们正在冒充另一个用户的迹象。

在一个应用程序中，我添加了一个大/突出的幽灵👻修正了点击时会结束会话的左上角问题。这是一个有趣但重要的功能。顶部的横幅也同样有效

[![Impersonating a user in Podia](img/b4430e9a3e18fc183302872cd12aa719.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AJi6XzSU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jamie.ideasasylum.cimg/podia-impersonation.png)

## 需要避免哪些无意的影响？

只有在构建了模拟功能之后，您才会发现所有意想不到的副作用。尝试通过考虑将您的用户信息发送到其他地方来简化这一过程。

我的一些来之不易的经验包括:

*   模拟用户时关闭对讲机！否则，您将发送一条消息，并最终在模拟会话中自己阅读它…而用户将永远不会收到通知！
*   禁用所有分析，否则您将在您的支持人员的位置周围开发一个非常可疑的用户活动热点！
*   如果可能，在模拟帐户时禁用用户通知/电子邮件，或提醒员工模拟用户仍会生成电子邮件、通知和仪表板事件。

## 多租户应用

当用户位于不同的子域或自定义域时，模拟用户会稍微复杂一些。不过，基本过程并不太难:

1.  生成附加到目标用户帐户的安全令牌
2.  使用令牌作为参数，将管理员用户重定向到正确域中的特殊端点([https://mycustomdomain.com/users/impersonate?)token = AC 8 feb 1 b 48 fcddfe 902814 ff 342 de 0d 41 e 80d 8d 67 e 56d 8182d 634 dbea 1220 e 92 f 9 DD a4 b 0 dbbe 902 EC 460 f 119 a 435 a 684793 e 844 b 738529 b42d 6d 60 f 12736 B2 f 2](https://mycustomdomain.com/users/impersonate?token=ac8feb1b48fcddfe902814ff342de0d41e80d8d67e56d8182d634dbea1220e92f9dda4b0dbbe902ec460f119a435a684793e844b738529b42d6d60f12736b2f2)
3.  使用令牌查找目标用户帐户
4.  使用您的应用程序要求的任何版本的`session[:current_user] = user.id`登录他们
5.  从用户帐户中删除令牌，以便模拟无法重播

## 重述

下面是模拟用户的大致过程:

1.  在您的管理仪表板中，让员工选择要模拟的用户帐户
2.  请求 2FA 验证码以确认管理员用户的身份
3.  确认他们的身份后，创建用户会话。在一个简单的 web 应用程序中，这可能只是`session[:current_user] = user.id`。或者您可以使用令牌和重定向来完成更复杂的多租户舞蹈。
4.  在审核日志中记录模拟会话
5.  将会话的详细信息通知团队空闲通道
6.  添加一个会话变量，指示帐户正在被模拟`session[:impersonating] = user.id`
7.  显示带有警告消息、当前用户名称和结束会话方式的横幅
8.  禁用所有用户分析，包括 javascript 和服务器端
9.  如果有必要和可能，禁用用户通知，如帐户活动电子邮件
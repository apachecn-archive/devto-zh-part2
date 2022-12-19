# 根据您的说法，使用 cookies 实现身份验证的正确方式。

> 原文：<https://dev.to/jochemstoel/the-right-way-to-implement-authentication-using-cookies-according-to-you-2e1f>

谈到实现认证和使用会话/cookie，互联网上众说纷纭。我们都同意将密码存储在 cookies 中或者设置一个像 *admin = true* 这样的值是一个非常糟糕的主意。我们还必须防止 XSS 饼干被偷。(将 *document.cookie* 附加到 *< IMG >* 元素 *src* 属性或其他东西)
在许多情况下，我们还希望防止“保存的”cookie 在复制时成功认证不同的客户端或设备。

根据您的经验，处理身份验证的正确方法是什么？用 PHP，JavaScript 或者任何你喜欢的语言/框架？
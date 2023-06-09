# 如何防止您的用户使用被破解的密码

> 原文：<https://dev.to/oktadev/how-to-prevent-your-users-from-using-breached-passwords-5cne>

不久前，美国国家标准与技术研究院(NIST) [正式建议](https://www.nist.gov/itl/tig/projects/special-publication-800-63)检查用户提供的密码是否存在数据泄露。今天我将向你展示如何使用 [PassProtect](https://github.com/OktaSecurityLabs/passprotect-js) 将这一功能轻松添加到你运行的任何网站上，这是我专门为此创建的开源开发者库。

## 为什么要检查用户密码？

新的 [NIST 建议](https://www.nist.gov/itl/tig/projects/special-publication-800-63)意味着每次用户给你一个密码，作为一个开发者，你有责任对照一个被破坏的密码列表检查他们的密码，并防止用户使用以前被破坏的密码。

这在安全社区中是一件大事，因为多年来，随着越来越多的网站被攻破，攻击者已经开始下载被攻破的用户凭据，并使用它们试图在其他地方危及帐户。

例如，假设您的密码“fdsah35245！~!3”，早在 2014 年就在众所周知的[索尼数据泄露](https://www.forbes.com/sites/josephsteinberg/2014/12/11/massive-security-breach-at-sony-heres-what-you-need-to-know/)中被攻破。一旦这些密码泄露，攻击者就会下载泄露的密码，并使用它们来尝试登录其他用户的帐户。

例如，攻击者可能会尝试使用您泄露的密码登录用户帐户，因为他们知道这是某人正在使用的真实密码，其他人(包括您)使用它的可能性很高。

为了解决这一问题，官方推荐的 NIST 解决方案是检查每个用户提供的密码，以确保它不是这些泄露的凭据之一，从而减少攻击者能够在您的网站上轻松猜出用户凭据的几率。

## 如何获取被破解的密码

NIST 建议的唯一问题是很难实现。为了对照一系列被破解的密码来检查用户的密码，你需要有一个包含每组被泄露的凭证的大规模数据库。这不仅不切实际，而且在许多层面上都存在风险(安全、法律、合规)。

为了帮助开发人员采纳这一新的 NIST 建议， [Troy Hunt](https://www.troyhunt.com/) 创建了免费服务[when I be Pwned](https://haveibeenpwned.com/)它将所有数据泄露聚集到一个巨大的数据库中。

我是否被 Pwned 允许您通过以下方式访问被破坏的数据:

*   直接下载被攻破的数据哈希:[https://haveibeenpwned.com/Passwords](https://haveibeenpwned.com/Passwords)(向下滚动页面找到下载链接)，或者
*   使用免费的匿名 API:[https://haveibeenpwned.com/API/v2](https://haveibeenpwned.com/API/v2)

我是否被 Pwned API 允许您发出任意多的请求，这对于检查您的用户的密码是否被破解特别有用。

## 如何轻松检查用户密码

为了让你更容易地根据我是否被 Pwned 数据库检查你的用户的密码，我最近创建了 [passprotect-js](https://github.com/OktaSecurityLabs/passprotect-js) 开发者库。

它被设计为一个简单的 JavaScript 库，可以放入任何网页(页面上的任何地方)，它将根据我是否被 Pwned API 服务检查您用户的密码，并通知用户他们使用的密码是否涉及违规:

[![PassProtect Demo](img/47d29833f88eccf336c8ab659dc50af7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZptOqYML--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/shqq9xf3vgminovtawj1.gif)

PassProtect 是:

*   **快**:整个库 16k (gzipped)。
*   **移动友好型**:在各种尺寸的设备上都能很好地呈现。
*   信息性的:它会向用户解释他们试图使用的密码已经被破解。
*   **不烦**:不会在当前会话中一遍又一遍的反复烦用户关于同一个密码。
*   **安全**:网络上不会存储或共享任何密码。PassProtect 使用 k-匿名，这意味着通过网络发送的唯一内容是密码哈希的前 5 个字符。

要使用 PassProtect，你需要做的就是将下面的`script`标签放到你站点的页面中:

```
<script src="https://cdn.passprotect.io/passprotect.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

我希望通过提供一些简单的工具，我们可以帮助开发人员采用新的 NIST 建议，并促进更好的整体 web 安全性。

如果您有任何问题或意见，请打电话给我。

**PS** :如果你想在你使用的每一个网站上启用 PassProtect 的功能，你可以随时去安装 [PassProtect Chrome 扩展](https://chrome.google.com/webstore/detail/passprotect/cpimldclklpfifolmdnicjnfbjdepjnf)

和...如果你喜欢 PassProtect，你可能也会喜欢 Okta [API 服务](https://developer.okta.com/)。Okta API 存储您正在构建的网站、移动应用和 API 服务的用户帐户，并使其易于处理身份验证、授权等事情。它为开发者(像你一样)提供了一个令人敬畏的免费计划，你可以在这里创建一个新的 Okta 帐户并尝试一下:【https://developer.okta.com/signup/。
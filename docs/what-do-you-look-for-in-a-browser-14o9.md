# 你在浏览器中寻找什么？

> 原文：<https://dev.to/tunaxor/what-do-you-look-for-in-a-browser-14o9>

那么，你在网络浏览器中寻找的是什么？你想要/需要什么功能？

我有点想探索 UWP/电子来创建一个 web 浏览器的原型，利用 chromium 和 chakra 是两个非常有能力的引擎的事实

我想看看~~我是否会感到无聊~~我可以做一些事情，
如果你想知道这是怎么回事，你可以查看下一个 UWP javascript 示例

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [微软](https://github.com/MicrosoftEdge) / [ JSBrowser](https://github.com/MicrosoftEdge/JSBrowser)

### 🌲作为 Windows 应用程序使用 JavaScript 构建的 web 浏览器

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![Logo](img/f28c111a58dbcc05ddac2f4dcb360f57.png) ](https://cloud.githubusercontent.com/assets/7266075/9254929/15448684-419b-11e5-8110-41757c572fe8.png) JavaScript 浏览器

作为 Windows 应用程序使用 JavaScript 构建的 web 浏览器。
[http://microsoftedge.github.io/JSBrowser/](http://microsoftedge.github.io/JSBrowser/)

[![badge_windowsstore](img/016c0281d83fc08750cecc01085b8d1c.png)T2】](https://www.microsoft.com/store/apps/9NBLGGH1Z7VX)

[![JavaScript Browser](img/1bfb66a7ee4f95f3657f68cad73f835c.png)T2】](https://cloud.githubusercontent.com/assets/3200580/10122615/99850d4a-651f-11e5-8357-e83576384010.png)

该项目是一个演示 Windows 10 网络平台功能的教程。浏览器是一个围绕 HTML [WebView 控件](https://msdn.microsoft.com/en-us/library/windows/apps/dn301831.aspx)构建的示例应用程序，主要使用 JavaScript 来照亮用户界面。使用 [Visual Studio 2015](https://www.visualstudio.com/) 构建，这是一个 JavaScript [通用 Windows 平台](https://msdn.microsoft.com/library/windows/apps/dn894631.aspx) (UWP)应用。

除了 JavaScript，HTML 和 CSS 是使用的其他核心编程语言。还包含了一些 C++代码来启用补充功能，但这并不是创建简单浏览器所必需的。

此外，我们正在利用 Chakra 中新的 [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) (ES2015)支持，这是 Microsoft Edge 和 WebView 控件背后的 JavaScript 引擎。ES2015 允许我们删除许多脚手架和样板代码，大大简化了我们的实施。以下 ES2015 功能用于创建此应用: [Array.from()](http://www.ecma-international.org/ecma-262/6.0/#sec-array.from) …

</article>

[View on GitHub](https://github.com/MicrosoftEdge/JSBrowser)

在电子的例子中，你可以在这里找到很多

但这也是一个很好的例子

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ wexond ](https://github.com/wexond) / [基于浏览器的](https://github.com/wexond/browser-base)

### 基于电子的现代和功能丰富的网络浏览器

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/d7a878fd82069dfada51145cc2c37bd9.png)T2】](https://wexond.net)

# Wexond 浏览器库

[![Actions Status](img/acae922bdb073051744a479dbef059fd.png)](https://github.com/wexond/desktop/actions)[![Downloads](img/cf868effb830a64eed7281137b8ef518.png)](https://wexond.net)[![FOSSA Status](img/3f3f03879d5291c276a1217a1d5d60a3.png)](https://app.fossa.io/projects/git%2Bgithub.com%2Fwexond%2Fwexond?ref=badge_shield)[![PayPal](img/8811b32f4f42c6533b7cdd768bd1730a.png)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=VCPPFUAL4R6M6&source=url)[![Discord](img/36adb1110c14606820a0ba5577363077.png)](https://discord.gg/P7Vn4VX)

Wexond Base 是一个现代网络浏览器，构建在现代网络技术之上，如`Electron`和`React`，它也可以用作创建定制网络浏览器的框架(参见[许可](https://github.com/wexond/browser-base#license)部分)。

# 目录:

*   [动机](https://github.com/wexond/browser-base#motivation)
*   [特性](https://github.com/wexond/browser-base#features)
*   [截图](https://github.com/wexond/browser-base#screenshots)
*   [下载量](https://github.com/wexond/browser-base#downloads)
*   [投稿](https://github.com/wexond/browser-base#contributing)
*   [开发](https://github.com/wexond/browser-base#development)
    *   [运行中](https://github.com/wexond/browser-base#running)
*   [文档](https://github.com/wexond/browser-base#documentation)
*   [执照](https://github.com/wexond/browser-base#license)

# 动机

直接编译和编辑 Chromium 可能既有挑战性又耗时，所以我们决定用现代网络技术来构建 Wexond。因此，开发工作和时间大大减少。无论哪种方式，Firefox 都是基于 Web 组件的，Chrome 在 WebUI 中实现了新的对话框(本质上是托管在 WebContents 中)。

# 特征

*   **Wexond Shield**——浏览网页时不带任何广告，不要让网站跟踪你。多亏了由 [Cliqz](https://github.com/cliqz-oss/adblocker) 提供动力的 Wexond Shield，网站加载速度甚至快了 8 倍！
*   没有谷歌服务和低资源使用率的 Chromium

</article>

[View on GitHub](https://github.com/wexond/browser-base)

有了 UWP，你可以用 JavaScript(如上所示)或 C#，VB，C++，或者用电子 JavaScript(或者[rust，cpp]原生模块，如果你想更有趣的话)

请分享你的特色，问题和评论！在任何情况下，我都会汇报我在这些平台上的工作经验！
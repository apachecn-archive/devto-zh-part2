# [Web]您的下载将在几秒钟后开始...

> 原文：<https://dev.to/jochemstoel/web-your-download-will-start-in-a-few-seconds-2lgp>

[![Download VS Code in 5 seconds](../Images/7f796b3a6907f17e030f2d0ba7e2ac03.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hgCm7tkm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hx28xt3j5ubrsh1kuaxi.png)

我们都见过这个，它可以追溯到 90 年代。你点击一个下载链接，你会看到一个页面，告诉你在下载开始前等待几秒钟。

这很容易复制。JavaScript 中的某个 *setTimeout* 内部的函数创建一个 HTMLAnchorElement，将 *href* 属性设置为文件的直接 URL，一个 *void download* 属性告诉浏览器这是一个下载，然后触发一个点击事件。

我从未质疑过这一点，但这到底是为了什么？我唯一能想到的是，这是某种形式的针对爬虫的保护(尽管我不知道为什么这是必要的)，或者可能是一种迫使用户访问网站并阻止外部网页直接链接的方法。

如果这确实是原因，那么它是无效的，因为直接下载 URL 仍然在页面源中，所以它仍然可以被“热链接”。

# 有人吗？

编辑:显然很多人说这是为了显示广告，但许多网站实际上并不显示广告，但仍然使用这一功能。[堆栈溢出](https://stackoverflow.com/questions/2142613/what-are-the-benefits-of-showing-your-download-will-begin-in-x-seconds)

[![Download VLC in 5 seconds](../Images/1f0f0a960b0540dfa93a926dfb28a34b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TVQya-5v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nf28yuai83l9kk597b7y.png)
[![Download TeamSpeak in 5 seconds](../Images/469301d244a8654f2f76329c918e2adb.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--WPuaMwrN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rolabknp62ya9jaln0nq.png)
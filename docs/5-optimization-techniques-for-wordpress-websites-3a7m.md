# WordPress 网站的 5 种优化技术

> 原文：<https://dev.to/alexxpaull/5-optimization-techniques-for-wordpress-websites-3a7m>

你遇到过速度慢的网站吗？你喜欢他们吗？不，毫无疑问！

花时间加载的网站让我们很烦。他们极大地贬低了用户体验。此外，统计数据证明了这一点。如果你的网站太慢，访问者可能没有耐心等待，有时更喜欢去你的竞争对手那里！

此外，搜索引擎，尤其是谷歌，对网站性能非常敏感。事实上，它使用这个参数来引用它的结果。如果你想增加在谷歌首页的机会，你会有兴趣拥有一个快捷的网站！当然，速度不是搜索引擎使用的唯一 SEO 标准，但它仍然是其中的一部分。

你觉得你的网站很慢吗？网上有各种工具可以让你测试它的速度。这里有三个:

1.  Pingdom 工具
2.  GTmetrix
3.  谷歌页面速度洞察

但是，不要把他们的结果当成绝对真理。这些工具使用不同的权重来测试你的网站的性能。这就是为什么这些平台之间的结果有时会有巨大的差异。现在让我们来看看你的 WordPress 的 5 种优化技术。

**1。WordPress 主机**

[![](../Images/ae0502f9d207acb20b854a36571fcc05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1HWFzWv6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5dqoor8284gnsatvi3q5.png)

没有一个高质量的虚拟主机，获得一个快速的网站几乎是不可能的。对于 WordPress 主机，我认为有两种情况:

场景#1
要么你知道一点服务器，你把你的 WordPress 网站安装在一个非托管的主机上(包括共享或 VPS 主机)。这是你能得到的最物有所值的东西，也是你如何配置你的服务器(PHP 版本，服务器端缓存等)的最大灵活性。).但是，您需要技能或系统管理员来配置您的服务器。

场景#2
否则，使用一个高质量的网络主机服务作为 Cloudways——[WordPress 托管主机](https://www.cloudways.com/en/wordpress-cloud-hosting.php)——专门托管 WordPress 网站。他们所有的 WordPress 都提供了 PHP 7+，HTTPS/2，一个预先配置的缓存系统(被称为 ThunderStack)和开发环境，这样你就不必在你网站的实时版本上进行修改。对我来说，Cloudways 拥有市场上托管 WordPress 网站的最佳质量/价格比，我建议你看看他们的报价。

**2。WordPress 主题**

[![](../Images/5e6db520d2362ebc61908855d439b900.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sz5NA-23--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qxczu5wg33rx1opfh5mh.png)

你的主题是你 WordPress 网站的基础。如果它对美观很重要，那么它对网站的加载速度和代码质量也很重要。WordPress 是世界上使用最多的内容管理系统，拥有数量惊人的主题(这是它的优势)。但是这种多样性是一把双刃剑！也有很多主题在纸上看起来很漂亮，但编码很差，只会减慢你的网站。

由于这个原因(以及其他原因)，我只使用 StudioPress 的 Genesis 框架来开发所有的主题(更多关于我为什么选择 Genesis 框架的细节)。我建议你看看所有的创世纪兼容的主题，因为它们都被开发成高质量的代码。

**3。WordPress 缓存插件**

[![](../Images/9f3222a67d2ec6b5e9e8e497b27c6e81.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KLHMTryn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vmspyl808jyswdpb0z4p.png)

WordPress 也有一整套缓存插件(或多或少质量不错)，允许你激活我在指南中告诉你的不同东西，比如:

*   缓存页面
*   Gzip 压缩
*   浏览器缓存
*   文件的缩小
*   CDN 整合
*   可能还有其他选项，取决于插件

就我个人而言，我在自己开发的所有网站上都使用 Breeze，这是一个免费的 WordPress 缓存插件。这个插件是免费的，兼容 Apache 和 Nginx web 服务器，允许你以一种极简的方式配置我上面提到的所有元素。

**4。WordPress CDN**

[![](../Images/eb824798743c2b7f5889e6894e0d9f1a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oomgToNs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8yqzs516h1lukuoyukju.png)

内容交付网络(CDN)主要用于有效地传播网站的内容，无论访问者在世界的任何地方。网络的服务器分布在世界各地，访问者总会有一个离他很近的点，可以更快地提供内容，减少下载时间(延迟)。

**5。WordPress 插件**

[![](../Images/602a9e09407b7c734cc44d67ac7f34ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DagAgx_W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vip7f6w0uectqdtcosx7.png)

插件很棒，但是不要过度使用。每个插件不仅会降低你的 WordPress 网站的安全性，更重要的是会降低它的性能。这就是为什么我只能建议你只保留那些对你来说真正必要的东西。

安装插件时，不要忘记查看最后一次更新的日期。如果已经有 2-3 年了，小心！它不应该是最新的，因此，它很有可能不再为 WordPress 的最新版本进行优化。

为了帮助您检测降低网站速度的插件，您可以安装查询监视器。“按组件查询”列出了加载的插件及其速度。

通过应用这些优化技术，与普通安装相比，你已经优化了大部分 WordPress 网站。正如你将在 Pingdom 工具、GTmetrix、
和 Google PageSpeed Insights 报告中看到的，还有其他方法可以优化 WordPress 网站。
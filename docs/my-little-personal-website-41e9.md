# 我的小个人网站

> 原文：<https://dev.to/crease29/my-little-personal-website-41e9>

嗨 devs，

这是我在 dev.to 上的第一篇帖子，我不知道这是否是分享我的个人网站的合适地方，但我想给你一些我在创建它时发现的有趣的见解。

所以在一个空闲的周日，我想创建一个小小的个人网站，有点创意，分享我的社交网络账户和 SSH & OpenPGP 密钥。

我完成了“设计”并写下了 HTML、CSS 和 JS。我用 [GTmetrix](https://gtmetrix.com/) 测试了 PageSpeed 的分数，得到了一个相当糟糕的分数。幸运的是，GTmetrix 提供了一些关于如何改进某些标准的非常好的指南。这里激活了一些缓存，那里启用了一些 web 服务器模块，那里优化了一些资产:**整洁**！

之后，我在加载性能方面做了更深入的研究。有一段内容让我很恼火。也许你会看到，后台有一个视频循环(也许在 Safari 中没有，因为苹果阻止自动播放视频🤦).所以我必须在背景视频加载之前把这段时间补上。简单的解决方案就是将背景视频的第一帧作为背景图像。太棒了！🙂

我还添加了一些`dns-prefetch`标签来改善外部资产的加载:

```
<link rel="dns-prefetch" href="//fonts.googleapis.com">
<link rel="dns-prefetch" href="//www.googletagmanager.com"> 
```

Enter fullscreen mode Exit fullscreen mode

总的来说，有趣的是，即使是最小的网站也能被优化。

如果你有任何问题，批评，改进或类似的经验，我会很高兴，如果你只是在这里回答！

我网站的代码也是，可以肯定:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[crease 29](https://github.com/Crease29)/[kai . neuwerth . me](https://github.com/Crease29/kai.neuwerth.me)

### 🎨我的个人网站

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Teaser image](img/36f1455f09a682a5e140440fec3d9e86.png)T2】](https://raw.githubusercontent.com/Crease29/kai.neuwerth.me/master/assets/img/opengraph.png)

# <g-emoji class="g-emoji" alias="art" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3a8.png">🎨</g-emoji>我的个人网站:kai.neuwerth.me

这个库包含了我自己的静态网站。您可以在这里看到生产状态[。](https://kai.neuwerth.me/)

很抱歉，因为许可证的原因，我不能发布在那里运行的背景视频，但我相信你会选择自己的背景视频。<g-emoji class="g-emoji" alias="blush" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f60a.png">😊</g-emoji>放在`assets/webm/`目录下，链接到`index.html`就可以了。

## 为发展而设立

```
git clone git@github.com:Crease29/kai.neuwerth.me.git
cd kai.neuwerth.me
npm install
grunt build
```

Enter fullscreen mode Exit fullscreen mode

## 请随意使用这个代码库

我会很高兴，如果你只是喜欢这个设置和“主题”，并将其用于自己的网站。如果你有问题，或者只是想分享你做了什么，请随时留下问题。<g-emoji class="g-emoji" alias="blush" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f60a.png">😊</g-emoji> <g-emoji class="g-emoji" alias="v" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/270c.png">✌️</g-emoji>

</article>

[View on GitHub](https://github.com/Crease29/kai.neuwerth.me)

感谢你阅读这篇文章！

卡伊
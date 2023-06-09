# 我构建 Google Chrome 扩展的经历

> 原文：<https://dev.to/crease29/what-ive-learned-from-building-a-google-chrome-extension-4do>

4 年前，我发布了我的第一个谷歌 Chrome 扩展。我说的是[页面线](https://chrome.google.com/webstore/detail/pageliner/nepakmljodobhlbbkpobblnifmhclemh)。稍后更多关于差评！😉请不要因为那些可怕的 jQuery 和匈牙利符号而责怪我。4 年前，jQuery 仍然是我喜欢的东西，而且很好用。但我知道事实是，它可以做得更干净、更优雅。

**那么，这是为了什么？** PageLiner 允许您在网站上放置参考线，就像您从 Adobe Photoshop 中了解的那样。这有助于查看网站上的所有元素是否正确对齐，或者测量参考线之间的距离。所以它非常适合前端开发人员。

我开始向我的同事和一些软件社区大声喊出来，所以我的用户群开始慢慢增长。

我遇到的第一个问题是一些页面上使用的 CSS 造成的视觉错误，影响了引导线的渲染。很容易修好。

随着用户群的增长，我注意到其他国家的一些用户也在使用 PageLiner，因为当时只有德语版本，所以我决定用 Javascript 实现一个非常简单的国际化方法，只需读取元素的`data-i18n`属性并替换其节点文本。

```
<span data-i18n="POPUP_TOGGLE_HELPLINES"></span> 
```

Enter fullscreen mode Exit fullscreen mode

```
$('[data-i18n]').each(function () {
    var sIdent = this.getAttribute('data-i18n'),
        sTranslation = '';

    if (sIdent === 'VERSION') {
        sTranslation = chrome.runtime.getManifest().version;
    }
    else {
        sTranslation = chrome.i18n.getMessage(sIdent)
    }

    if (!sTranslation.length) {
        console.error('Could not find message string by ident "' + sIdent + '"!');
        return;
    }

    $(this).text(sTranslation);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这使得用户群增长稍快。但是在某一天，用户群爆炸了！💥下图显示了用户、评论和差评的大幅增长:

[![PageLiner statistics](img/44cccac3dca26a0ca966bc0b5413827e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vDCehWmR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ajar8kepemhgdfyoewam.png)

该扩展得到了许多俄罗斯人的评论。类似“删除此病毒”“我无法卸载此扩展”的评论。我不知道发生了什么，于是联系了谷歌。他们对此无能为力。谷歌刚刚删除了那些带有脏话的评论。
我试图通过 Google+或 Hangouts 联系评论者，有技术背景的人回复了我。我们分析了一下，发现有人拿走了我的扩展，修改了它，用恶意软件扩展了它，并作为 OEM 扩展安装在其他客户端上，这样它就不能在谷歌浏览器中删除了。几个月后，用户群回来了，一切都很好，除了差评。

一年后，同样的事情发生了，但更加严重:

[![PageLiner statistics](img/c7876153bf9a22f0b7283cbe5052a778.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DkPl6200--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s1ami1ltj1kfer3b4k2t.png)

在俄罗斯反病毒论坛上有很多帖子询问如何卸载这个扩展。几个月后，用户群又回到了 6000 人的稳定水平。我猜反病毒供应商已经实现了对恶意软件扩展的检查，并可以删除它。

目前资料片没有太多变化，但对我来说没问题。我真的很自豪，我创造了一个扩展，每天都有这么多真实的人在使用。

如果有问题，我很乐意在下面的评论中回答！你曾经创建过浏览器扩展吗？分享一下！

希望这在某种程度上是有趣的，你喜欢我的小思考。🙂

如果你想看看这个扩展背后的代码，你可以在这里找到:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ Crease29 ](https://github.com/Crease29) / [页面线](https://github.com/Crease29/pageliner)

### 📐Google Chrome 扩展在网站上创建引导线。

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![](img/f2c9a677a240a68cf97c0309d4f99f82.png)](https://github.com/Crease29/pageliner/blob/master/icons/icon_pl_48x48.png)[![Join the chat at https://gitter.im/WebChimp-DE/pageliner](img/80064cbae208bbac11d2f49a393a38ef.png)](https://gitter.im/WebChimp-DE/pageliner)[![](img/0929a1e1b4c705d8e21d06428e25eb3c.png)](https://github.com/Crease29/pageliner/workflows/.github/workflows/main.yml/badge.svg)

[![Chrome extension version](img/10ec1ba393ba1e909d0623e084b6f228.png)](https://camo.githubusercontent.com/0e7aca2957f2f685631b511ed473481d7501eb819afc72cfbbfa3400f5622027/68747470733a2f2f62616467656e2e6e65742f6368726f6d652d7765622d73746f72652f762f6e6570616b6d6c6a6f646f62686c62626b706f62626c6e69666d68636c656d68)[![Chrome extension user count](img/e2467fba2de545a00bc92c51e47ed9be.png)](https://camo.githubusercontent.com/e266622213971ac1db870ecb39e4d3b11e3f38fc0b6c7db0794538c09580e026/68747470733a2f2f62616467656e2e6e65742f6368726f6d652d7765622d73746f72652f75736572732f6e6570616b6d6c6a6f646f62686c62626b706f62626c6e69666d68636c656d68)[![Chrome extension price](img/674926f1299491a84371e577b1a35bc2.png)](https://camo.githubusercontent.com/195cb47c6045b88500f1f6049391d8970bdbde1a137bd6f25859319b567007c0/68747470733a2f2f62616467656e2e6e65742f6368726f6d652d7765622d73746f72652f70726963652f6e6570616b6d6c6a6f646f62686c62626b706f62626c6e69666d68636c656d68)[![Chrome extension rating count](img/0a107d51b91e3e6581e170f2cab23878.png)T11】](https://camo.githubusercontent.com/387d2b44545c0292e8dcab761100c3b6b3558f766a314a071dbf545ba0b86962/68747470733a2f2f62616467656e2e6e65742f6368726f6d652d7765622d73746f72652f726174696e672d636f756e742f6e6570616b6d6c6a6f646f62686c62626b706f62626c6e69666d68636c656d68)

如果你喜欢这次延期，我会很高兴你请我喝啤酒🍺 <g-emoji class="g-emoji" alias="grin" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f601.png">😁</g-emoji>[![Flattr this git repo](img/04f92fe62149c34fde504f8520e6f14d.png)](https://flattr.com/submit/auto?user_id=Crease29&url=https%3A%2F%2Fgithub.com%2FCrease29%2Fpageliner%2F&title=PageLiner&language=en_GB&tags=github&category=software)[![Donate via PayPal](img/b4aa262c001053d347c26fecc867717b.png)T9】](https://www.paypal.me/koi)

Google Chrome 扩展在网站上创建引导线。

[在 Chrome 网上商店查看](https://chrome.google.com/webstore/detail/pageliner/nepakmljodobhlbbkpobblnifmhclemh)

如果您有任何建议或错误，请创建一个问题或使用 Chrome 网络商店中的支持表单。谢谢大家！

## 特征

### 从标尺添加参考线

将鼠标悬停在其中一个标尺上，并从那里开始拖动新的参考线，就像你在 Photoshop 中知道的那样。

### 添加带快捷方式的参考线

*   `Alt` + `H`在光标位置添加一条*水平*引导线，选择它进行键盘移动。
*   `Alt` + `V`在光标位置添加一条*垂直*引导线，选择它进行键盘移动。
*   `Alt` + `A`在光标位置添加一条*水平*和一条*垂直*引导线。

### 用键盘移动引导线

1.  添加一条参考线。
2.  点击…

</article>

[View on GitHub](https://github.com/Crease29/pageliner)
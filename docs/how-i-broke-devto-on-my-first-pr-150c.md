# 我是如何在我的第一次公关中击败 Dev.to 的

> 原文：<https://dev.to/bennypowers/how-i-broke-devto-on-my-first-pr-150c>

今天我做了我的第一个 PR to dev.to，它让你[给 glitch 嵌入](https://github.com/thepracticaldev/dev.to/pull/680)添加选项。我努力安装需求和构建应用程序，为新功能编写单元测试，学习我的第一点 ruby，在几个小时的游戏时间后，提交 PR。很快就被接受了，✨THANKS，[@本](https://dev.to/ben)！🌟

这时，我注意到整个网站上嵌入的每一个小故障都被破坏了

[![🤦‍♂️](img/f0e2df31f9bca18476989534ea7cb8b8.png)T2】](https://i.giphy.com/media/3og0INyCmHlNylks9O/giphy.gif)

感谢[@韵](https://dev.to/rhymes)，指出问题:

[![rhymes profile image](img/557aa2636ae2993dfd13f29772ceb666.png) ](/rhymes) [ rhymes ](/rhymes) • [<time datetime="2018-09-17T14:52:32Z" class="date-short-year"> Sep 17 '18 </time>](https://dev.to/rhymes/comment/5hgh) 

我认为某处有一个错误，因为现在如果我试图嵌入这个:

[glitch.com/edit/#!/familiar-violet](https://glitch.com/edit/#!/familiar-violet)

用`{% glitch familiar-violet %}`或`{% glitch familiar-violet app %}`我得到 404 页

[![glitches](img/a4af3559a7807903097c3dc2a074bcc2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dez814qa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hw1n0m8k88wvrolldal5.png)

一个小故障:P

解决方案和 bug 本身一样简单:我省略了在 ruby 字符串中指定插值的`#`:

```
-    src={@uri} +    src=#{@uri} 
```

Enter fullscreen mode Exit fullscreen mode

对我来说是一个有趣的学习练习。我真诚地希望我的疏忽没有造成任何不便。

新的公关在飞行中，一旦修复是活的，我会写一个回归测试。

[https://github.com/thepracticaldev/dev.to/pull/683](https://github.com/thepracticaldev/dev.to/pull/683)
# 在移动设备上使用“悬停”css 选择器非常有趣

> 原文：<https://dev.to/mario/lots-of-fun-with-hover-css-selectors-on-mobile-devices-3kh6>

[![](img/b9b1431c6065b6dd1179f115822c2c74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3mWd-XBB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q3e47r86fg442793yqb8.png)

欢迎来到马里奥的“我不知道我在做什么”

我绝不是一个设计师或深入的 CSS 专家，但最近有理由为一个基于网络的聊天机器人组件做一些 CSS 工作。它必须在桌面和移动浏览器上都能工作，虽然大多数都很简单，但我在移动浏览器上遇到了一些有趣的行为，围绕着按钮上的“悬停”选择器。

***简而言之:手机浏览器似乎对有焦点的按钮应用了“悬停”风格。*T3】**

这让我很困惑——大多数移动浏览器(Android 和 iOS)对有焦点的按钮应用了“悬停”样式。这并不是我真正想要的，所以我想在媒体查询中包装悬停样式，以阻止不能真正悬停的浏览器(即移动浏览器)使用它:

```
@media (hover: hover) { 
```

Enter fullscreen mode Exit fullscreen mode

然而，这不起作用，因为似乎大多数移动浏览器认为自己能够悬停(通过长按)。该死的。好吧，我们来试试相反的:

```
@media not all and (hover: none) { 
```

Enter fullscreen mode Exit fullscreen mode

仍然没有运气，原因和上面一样，我猜。

因此，我接下来关注的是“指针”媒体查询，嘿，很快，使用“指针:好”似乎工作得更好，因为移动浏览器显然不认为自己有一个好的指针。这个只有一个问题:Firefox 不支持(还不支持？).为了解决这个问题，我添加了“-moz-touch-enabled: 0”，所以我最后使用了这个媒体查询:

```
@media (-moz-touch-enabled: 0), (pointer: fine) { 
```

Enter fullscreen mode Exit fullscreen mode

这最终非常有效，除了一个例外:“悬停”效果现在在带触摸屏的笔记本电脑上的 Firefox 上不起作用。我想我(或者更确切地说是我的客户)可以接受这一点，因为至少这意味着按钮现在可以在所有移动浏览器和所有桌面浏览器中按预期呈现，除了“Firefox + touch screen”组合。

分享这篇文章，希望它能帮助一些人，但是同样的，我不是 CSS 专家，正如我所说的,“解决方案”在任何情况下都不是完美的解决方案——如果有人看到这篇文章，对我的无能摇头，请让我在评论中知道任何更好的解决方案！
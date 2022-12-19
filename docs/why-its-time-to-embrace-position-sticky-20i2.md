# 为什么是时候拥抱粘性位置了

> 原文：<https://dev.to/mattstuddert/why-its-time-to-embrace-position-sticky-20i2>

每当我给人们讲授 CSS 位置时，我总是警告不要使用`position: sticky;`。

浏览器对所有位置值的支持都很好，除了 sticky。那么，使用一个大多数时候都不会起作用的东西有什么意义呢？

但是，我必须说，我现在改变了我的论调！在构建 [Frontend Mentor](https://www.frontendmentor.io) 的过程中，我一直在寻找一个简单的解决方案来在资源页面上创建一个粘性菜单。菜单允许你在页面上的类别之间跳转，它不是主标题的一部分。它所要做的就是当元素滚动到视窗的顶部，当容器滚动过去时离开。

当然，安装一个 npm 包来达到这个效果是非常容易的，但是我想用最简单的方法。

因为我知道这正是由`position: sticky;`提供的行为，所以我想我应该看看当前的浏览器支持:

[![caniuse.com browser compatibility stats with CSS position sticky, showing over 86% global browser compatibility](../Images/7b5b5ef6253b0c5bda9c6580c716100c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A65apUZY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/c3G49nb.png)

不算太差！全球超过 86%的浏览器支持`position: sticky;`，几乎所有主流的现代浏览器都支持它。

但是，如果有人通过不支持它的浏览器访问该网站，会发生什么呢？简单！元素就是不粘在视口上。没有大规模混乱或互联网中断。只是没有粘性元素。

该网站的主要用户是网络开发人员(包括现在的和有抱负的)。因此，假设绝大多数人会通过现代浏览器访问网站是完全合理的。

我已经开始使用它了，我必须说它让我喜笑颜开🙂这是当您向下滚动页面时显示在视窗顶部的菜单栏:

[![Animated GIF of CSS position sticky property sticking navigational menu to the top of the screen](../Images/c7fbf99596a32cbd6586a7894fb1d90d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZQHxVmbI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/z5OuuvZ.gif)

这里，当您滚动过父元素时，它会自动释放:

[![Animated GIF of CSS position sticky property releasing navigational menu from the top of the screen when the user scrolls passed the container](../Images/a88a2d693a13632417f651945d7ed529.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8H6iGPiA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/EoA0n4z.gif)

轻松点。

如果你想正确地查看它，[前往站点](https://www.frontendmentor.io/resources)上的资源页面。

想在你的博客文章页面上有一个粘性边栏吗？基本标记如下:

```
<!-- parent element -->
<section> 
    <article>
        ...
    </article>

    <!-- sticky element -->
    <aside>
        ...
    </aside>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

一旦`section`元素移出视口，`aside`将会自动脱离并移出屏幕。

所以，下次你像我一样需要一个粘性工具条或菜单时，考虑使用`position: sticky;`。

## 保持联系

如果你想保持联系，你可以[关注前端导师](https://twitter.com/frontendmentor)或者[在 Twitter 上关注我的个人账户](https://twitter.com/_mattstuddert)。随便打个招呼吧！🙂
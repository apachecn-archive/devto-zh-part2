# 带有一行 CSS 的全屏部分

> 原文：<https://dev.to/devhammed/full-screen-section-with-one-line-of-css-4jm>

你好，我最近分享了我如何成为一名 Web 开发人员的故事，如果你还没看过的话，这里是[链接](https://dev.to/devhammed/story-of-a-17-years-old-web-developer-3584)。

所以今天，我想分享一个你可能不知道的 CSS 一行程序解决方案，这个解决方案利用 CSS 单元 vh 来创建一个页面的全屏部分(例如标题)。

*   所以 WTF 就是 vh？？？

[![](../Images/d3f1a25a6007489072f751e7a4ef6d29.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rjsxbt0E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AWXGxBTgA7igKKyd3Pq9aWQ.jpeg)

根据 MDN 文档:

— **视口高度**(VH)——全视口高度的百分比。10vh 将解析为当前视口高度的 10%。

这样，我们知道 1vh 等于用户浏览器可见部分(视口)的 1%。我们将把它与 min-height 一起使用，以确保无论内容大小，该部分都是满的。

> 。全屏{
> 
> 最小高度:100vh
> 
> }

这是给像我一样好奇的人的笔😃😃😃

[https://codepen.io/devHammed/embed/EEoogx?height=600&default-tab=result&embed-version=2](https://codepen.io/devHammed/embed/EEoogx?height=600&default-tab=result&embed-version=2)

感谢你阅读❤️️❤️️，我想接下来应该是……

[![](../Images/c27dcc0ae5f267a0359664620847f0e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uFBrkx5Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/220/1%2AGK6G_LKVxtMAKN7fepRpcA.gif)

* * *
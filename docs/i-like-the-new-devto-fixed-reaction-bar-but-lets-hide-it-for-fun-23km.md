# 我喜欢新开发的固定式反应杆。不过还是藏起来玩玩吧！

> 原文：<https://dev.to/ahmedmusallam/i-like-the-new-devto-fixed-reaction-bar-but-lets-hide-it-for-fun-23km>

我觉得这很酷:

[![ben image](../Images/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## Changelog:帖子操作 UI 更改

### 本哈尔彭 9 月 13 日 181 分钟阅读

#changelog #meta](/ben/changelog-sticky-post-actions-ui-change-3i00)

但后来我想知道，如果当你向下滚动时，底部的滚动条向下滑动，而当你向上滚动时，底部的滚动条又向上显示，那会是什么样的体验。所以我想知道如何实现这一点。很容易，事实证明:

[https://www.w3schools.com/howto/howto_js_navbar_slide.asp](https://www.w3schools.com/howto/howto_js_navbar_slide.asp)

现在让我们利用这一点来实现这一点:

[![](../Images/111bb60176f0110690ca96db42dedfdc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CI1CpmcJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yfwyss8z96a9im79s69g.gif)

*与[https://getkap.co/](https://getkap.co/)T3】成为可能*

代码真的很简单:

```
var initialScrollPos = window.pageYOffset;
var bar = document.getElementById("article-reaction-actions");
bar.style.transition = "bottom 0.25s"
window.onscroll = function() {
  var currentScrollPos = window.pageYOffset;
  if (initialScrollPos > currentScrollPos) { // user scrolled down
    bar.style.bottom = "0";
  } else { // user scrolled up
    bar.style.bottom = `-${bar.offsetHeight}px`; // 80 is a number larger than the bar's height
  }
  initialScrollPos = currentScrollPos;
} 
```

Enter fullscreen mode Exit fullscreen mode

> 我知道这是可以优化的，但这只是为了举例说明:)

我不知道我最喜欢哪个...过渡很好，但是固定的横条可能更好，让读者专注于阅读，而不是看着横条移动。我很想听听你们的想法！
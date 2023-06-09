# TIL: CSS 网格将很快成为居中元素的最短方式

> 原文：<https://dev.to/stefanjudis/til-css-grid-will-soon-be-the-shortest-way-to-center-elements-1p9l>

今天我在推特上发布了我发现的一个新的 CSS 属性:`place-items`。~~这是`align-items`和`justify-content`T6 的简单简写——至少我是这么认为的。原来它是`align-items`和`justify-items`的简写([感谢本杰明指出这一点](https://twitter.com/bdc/status/1013882355197644801))。~~

这个发现最初让我很难过，因为这意味着我不能在基于 Flexbox 的类中去掉一个 CSS 声明来集中处理事情。

```
/* this class is there to stay */
.center {
  display: flex;
  align-items: center;
  justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

在分享了我的失望之后，本杰明提到了一些我之前没有想到的事情。`place-items`与`display: grid`完美配合。

我不得不承认，即使当 CSS `grid`是如今支持的[跨浏览器时，我也没怎么用它，但事实证明，你可以通过在只有一行和一列的元素上定义它来轻松地将事情居中(参见](https://caniuse.com/#feat=css-grid) [this CodePen](https://codepen.io/stefanjudis/pen/mKoBQM) 作为例子)。

```
.center-using-grid {
  display: grid;
  align-items: center;
  justify-items: center;
}

.center-using-grid-even-shorter {
  display: grid;
  place-items: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 听起来好得难以置信？有陷阱吗？

良好的...算是吧。`place-items`是不是[还不支持跨浏览器](https://developer.mozilla.org/en-US/docs/Web/CSS/place-items#Browser_compatibility)(基本上今天是 Chrome 和 Firefox)，不过还好， [PostCSS 已经把你覆盖了，这样你今天就可以用了](https://twitter.com/PostCSS/status/1013859184725643264)。🎉

你可以在关于的详细的 [MDN 文章中读到更多关于`place-items`的内容。](https://developer.mozilla.org/en-US/docs/Web/CSS/place-items)

开心定心！
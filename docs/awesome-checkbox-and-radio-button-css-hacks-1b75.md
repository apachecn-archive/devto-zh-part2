# 真棒复选框和单选按钮 CSS 黑客

> 原文：<https://dev.to/devhammed/awesome-checkbox-and-radio-button-css-hacks-1b75>

[![](img/ecb5ef752efa2ae3cd56022b8edc928d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2QUlqT9q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/355/1%2AL7rQX_nQSB0K59x_bvEAUw.png)

你好，今天我们将探讨一些无需 JavaScript 就能使用的 CSS 技巧。我会仔细阅读 3 篇精彩的文章，我知道你会对这篇文章的结尾感到惊讶。让我们继续前进

*   **CSS 菜单**

首先，我们将隐藏复选框和菜单容器。

```
.menu--toggle,
.menu--body {
 display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个菜单的主要技巧是~ CSS 通用同级选择器。下面 CSS 是用来改的。要显示的菜单体:复选框处于选中状态时阻止。

根据 MDN 文档，~是:

> **通用兄弟组合符** (~)将两个选择器分开，只有当第二个元素跟在第一个元素之后(虽然不一定是立即)才匹配第二个元素，并且两者都是同一个父[元素](https://developer.mozilla.org/en-US/docs/Web/API/Element)的子元素。

下面是我们将使用这个救生器的技巧:

```
.menu--toggle:checked ~ .menu--body {
 display: block;
} 
```

Enter fullscreen mode Exit fullscreen mode

检查下面的笔为这个黑客的全部标记

[https://codepen.io/devHammed/embed/vRbqzB?height=600&default-tab=result&embed-version=2](https://codepen.io/devHammed/embed/vRbqzB?height=600&default-tab=result&embed-version=2)

*   CSS Modal

这个技巧和 CSS 菜单是一样的，所以我不打算解释这个，因为唯一的区别是如何设计模态容器，但下面是笔:

[https://codepen.io/devHammed/embed/BrMOpd?height=600&default-tab=result&embed-version=2](https://codepen.io/devHammed/embed/BrMOpd?height=600&default-tab=result&embed-version=2)
我们要讲的最后一个黑客是:

*   CSS Accordion

是的，你们都知道手风琴可以用纯 CSS 和单选按钮创建🙄🙄。注意:只能在一个命名组中选择一个单选按钮。

我们将利用这一点来创建纯粹的 CSS 手风琴，它还利用:像 CSS 菜单检查状态，下面是笔，供你玩。

[https://codepen.io/devHammed/embed/WzPgbX?height=600&default-tab=result&embed-version=2](https://codepen.io/devHammed/embed/WzPgbX?height=600&default-tab=result&embed-version=2)
感谢您抽出时间阅读此文，😍😍😍你能做到吗:

<figure>[![](img/c27dcc0ae5f267a0359664620847f0e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uFBrkx5Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/220/1%2AGK6G_LKVxtMAKN7fepRpcA.gif) 

<figcaption>拍手……拍手……拍手……拍手</figcaption>

</figure>

下次见！！！**</>T1】**

* * *
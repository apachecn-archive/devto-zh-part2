# 焦点捕获-在元素内部捕获焦点

> 原文：<https://dev.to/r3l_k3r/focus-trappinglooping-for-keyboard-navigation-l6m>

在本文中，我将介绍使网页可访问的一个重要方面——DOM 元素内部的*捕获/循环*(从现在开始称之为*循环*)焦点、需要它的场景以及实现它的 JavaScript 实用程序。
在最后一部分，我将讨论在启用屏幕阅读器的移动设备上建议的解决方案的细微差别。

## 关于我们要修复的问题的一些文字

键盘导航是任何产品可访问性的基本要求。在 web 页面中，通过使用语义 HTML、`tabIndex`、`aria`属性和一些 JS 的组合，很容易实现。

如果你知道这个问题，你可以跳到下一节。详情请继续阅读。

我们举个例子。想象一个同时具有登录和注册按钮的登录页面。单击 any 将打开一个模式，其中包含相应操作所需的字段。为了正确的键盘导航-

1.  当模态打开时，第一个输入元素(*哪个元素应该获得焦点取决于页面的交互设计*)应该获得焦点。
2.  用户应该能够切换到模式中的其他可交互元素。
3.  在跳转时，焦点不应该离开模态，而应该在模态内部循环

尽管上面的交互听起来很自然，但默认情况下并不是这样。那是因为模态或类似的 UI 元素只是虚拟边界。Tabbing 对 DOM 的自然秩序起作用。

因此，在打开模态时，模态元素甚至不会获得焦点(*，如果模态元素没有绝对定位到触发它的元素*)，更不用说焦点在内部循环了。

## 这个实用程序解决了这个问题

我将不再浪费文字，并展示为我们解决问题的代码:

**TL；博士**

下面是 JS 代码片段，它完成了捕获选项卡式导航的主要任务。这个片段可以在第 47 到 62 行的要点中找到。

```
function keyboardHandler(e) {
  if (e.keyCode === 9) {
    //Rotate Focus
    if (e.shiftKey && document.activeElement === firstFocusableEl) {
      e.preventDefault();
      lastFocusableEl.focus();
    } else if (!e.shiftKey && document.activeElement === lastFocusableEl) {
      e.preventDefault();
      firstFocusableEl.focus();
    }
  }
};
el.addEventListener('keydown', keyboardHandler); 
```

在上面的代码中，`firstFocusableEl` & `lastFocusableEl`是容器元素中第一个和最后一个可聚焦的元素(*顾名思义*)。

对于一个详细的和生产就绪的代码，请看看下面的要点。
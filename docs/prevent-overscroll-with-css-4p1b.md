# 使用 CSS 防止过度滚动

> 原文：<https://dev.to/ziga/prevent-overscroll-with-css-4p1b>

## 有什么问题吗

你有一个可滚动的对话框。但是当你滚动到它的结尾，整个页面开始滚动:(如何防止这种情况？

## 还有一个鲜为人知的 CSS 属性

```
.element {
  overscroll-behavior: contain;
} 
```

Enter fullscreen mode Exit fullscreen mode

(在 [Safari](https://caniuse.com/#search=overscroll-behavior) 中尚不支持)

这摘自 evilmartians.com 的一篇令人惊叹的[未来卷轴](https://evilmartians.com/chronicles/scroll-to-the-future-modern-javascript-css-scrolling-implementations)文章。
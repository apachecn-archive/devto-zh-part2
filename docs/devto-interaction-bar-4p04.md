# 开发到交互栏

> 原文：<https://dev.to/zcdunn/devto-interaction-bar-4p04>

我不喜欢在应用程序中使用持久的 chrome，尤其是在移动设备上。我不喜欢占用屏幕空间，chrome 会分散我阅读实际内容的注意力。虽然我可以在桌面上使用扩展来摆脱工具栏，但 Android 的 Chrome 不支持扩展，所以我被困在了工具栏中。

我的建议是 Dev .引入 [CSS 变量](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)来控制交互栏的位置。CSS 变量或自定义属性在`::root`规则中设置，并且总是以`--`开始。

```
::root {
    --interaction-bar-position: fixed;
} 
```

Enter fullscreen mode Exit fullscreen mode

它们的值可以是任何有效的 CSS 值。要使用该变量，请使用`var`功能。

```
.container .article-actions {
    position: var(--interaction-bar-position);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后仅在特定规则中覆盖该变量。

```
.container .article-actions.relative {
    --interaction-bar-position: relative;
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。默认情况下，交互栏将固定在底部。设置中一个简单的复选框可以控制`article-actions`元素上`relative`类的存在。

这是完整的 CSS。

```
::root {
    --interaction-bar-position: fixed;
}

.container .article-actions.relative {
    --interaction-bar-position: relative;
}

.container .article-actions {
    position: var(--interaction-bar-position);
} 
```

Enter fullscreen mode Exit fullscreen mode
# 快速和肮脏的网页可访问性

> 原文：<https://dev.to/antonfrattaroli/quick-and-dirty-web-accessibility-29f>

# 为什么

*   法律理由:“平等机会”、“合理便利”
*   政府合同
*   搜索引擎优化...算是吧。

# 多久

不会太久的。

# 如何

## 使用语义 HTML

使用语义 HTML5 元素- `header`、`footer`、`nav`、`main`、
、`dialog`(带填充)、`article`、`section`、`aside`。它们同时也是 ARIA
的地标，所以你不需要处理它们。也使用语义 HTML4 元素，比如在`input`字段上使用`label`。

## 跳过导航链接

您需要:

a.一个只有屏幕阅读器的类:

```
.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    margin: -1px;
    padding: 0;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    border: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

b.您的`body`元素后的链接:

```
<body>
    <a class="sr-only" href="#main" tabindex="0">Skip to Main Content</a> 
```

Enter fullscreen mode Exit fullscreen mode

c.给你的`main`元素一个 ID:

```
<main id="main"> 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
WAVE 无障碍检查器浏览器扩展

为什么是这个？因为 Firefox 和 Chrome 都有 WAVE Accessibility Checker 扩展，这涵盖了我认识的 99%的人。

没有一个可访问性检查器做得特别好。清除错误，忽略警告和手动检查。常见的错误是图像上缺少`alt`属性和空标题。

## 键盘导航- tabindex

在这一步中，您将把`tabindex`添加到所有“可交互的”内容中——用户可能点击的任何内容，比如链接或文本输入字段。不要担心文本部分，真正知道如何使用屏幕阅读器的人将能够管理它。

`tabindex`只有两个值:

*   `0`:可以跳转到该元素。
*   `-1`:无法跳转到此元素。

跳转应该遵循 DOM 顺序，这也是我们只使用`0`的原因。像菜单项这样的隐藏东西应该有`-1`。安装 ChromeVox 屏幕阅读器浏览器扩展来测试这一点——这是免费的，它做的工作。

## 其他东西

你可能会有一些一次性的键盘导航，比如“按 enter 打开菜单，按箭头键导航菜单项”。用`aria-label`属性告诉用户。

如果您有不需要用户交互就能更新的内容，比如通知，那么就对它使用`aria-live="polite"`属性和值。这将告诉用户发生了一些事情。

如果你的链接只写着“点击这里”，那你就错了。

如果您有一个`hover`事件处理程序或 CSS 伪类，那么也让它在`focus`上做同样的事情。

如果你有一个`click`事件处理程序，你会想要为`keypress`和`event.key === 'Enter'`添加相同的处理程序。

# 够好了吧？

好到可以在法庭上说你尽力了。

# 不够好？

*   这个想法是所有用户对网站都有相同的体验。
*   联系顾问，他们有昂贵的屏幕阅读器。
*   招募真正的残疾人来协助测试。

# 备注

从谷歌图片搜索的“标记为重复使用”工具 Ganked 图像。原载于[https://yoast.com/http-503-site-maintenance-seo/](https://yoast.com/http-503-site-maintenance-seo/)
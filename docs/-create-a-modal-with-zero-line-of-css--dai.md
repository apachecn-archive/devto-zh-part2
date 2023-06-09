# 🤯用 CSS 的零线创建一个模态🤯

> 原文：<https://dev.to/mornir/-create-a-modal-with-zero-line-of-css--dai>

## 简介

HTML 5.2 给我们带来了`dialog`元素！🙌

目前，只有 Chrome 实现了它，但如果其他浏览器供应商想完全兼容 HTML，他们肯定会效仿。(在 Firefox 中，它已经在一面旗帜后面了。)它还有一个 [polyfill](https://github.com/GoogleChrome/dialog-polyfill) 。

现在让我们来研究一下这个相对较新且闪亮的 HTML 元素。🔬

## 演示

在下面的 codepen 中，我没有写一行 CSS 就创建了一个全功能的 modal。🤓

[https://codepen.io/mornir0/embed/xaNGma?height=600&default-tab=result&embed-version=2](https://codepen.io/mornir0/embed/xaNGma?height=600&default-tab=result&embed-version=2)

那么，这一切是如何运作的呢？

嗯，其实很简单。我们只需要使用`dialog`元素并添加`open`属性来显示它(默认情况下它是隐藏的)。我们可以通过使用 HTMLDialogElement 上提供的`showModal`方法来打开该属性。我们调用`close`方法来关闭它。

```
const modal = document.querySelector('#modal')
modal.showModal() // opens the modal
modal.close() // closes the modal 
```

Enter fullscreen mode Exit fullscreen mode

您可以注意到`dialog`元素自动为我们做了一些简单的事情:

*   对话框位于屏幕中央。
*   背景变暗，模式后面的元素无法交互。
*   这个对话框已经有了基本的样式(很难看，但是稍后会有更多的介绍)。
*   `escape`键关闭模态。

通过向表单标签添加`method="dialog"`:

*   第一个交互元素(按 DOM 顺序)是焦点。
*   表单提交后，对话框关闭，触发提交事件的元素的值保存在 HTMLDialogElement 的`returnValue`属性中。

在上面的代码中，两个按钮都触发了模态的关闭。当模态关闭时，它发出一个`close`事件。在我们的演示中，modal.returnValue 将包含' yes '或' no '(如果用户按下[escape]，则为空字符串)。

```
modal.addEventListener('close', () => {
    console.log(modal.returnValue) // In our case: 'yes', 'no' or ''
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 定制

对话框元素带有默认的用户代理样式表，但是可以完全定制。你甚至可以这样改变模式背景:

```
#modal::backdrop {
    background-color: rgba(0, 0, 0, 0.4)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 使用[顺风 CSS](https://tailwindcss.com/) 定制的例子

[https://codepen.io/mornir0/embed/GXLGQK?height=600&default-tab=result&embed-version=2](https://codepen.io/mornir0/embed/GXLGQK?height=600&default-tab=result&embed-version=2)

## 资源

在这篇文章中，我只讨论对话框元素的一些方面。关于这个话题的详细信息，我推荐以下链接:
[https://alligator.io/html/dialog-element/](https://alligator.io/html/dialog-element/)
[https://developer . Mozilla . org/en-US/docs/Web/HTML/Element/dialog](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog)
[https://demo.agektmr.com/dialog/](https://demo.agektmr.com/dialog/)
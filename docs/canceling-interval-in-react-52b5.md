# Setinterval React 取消 React 中的 Setinterval

> 原文：<https://dev.to/dance2die/canceling-interval-in-react-52b5>

*图片由 [Icons8 团队](https://unsplash.com/photos/dhZtNlvNE8M?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

有时你需要使用 [window.setInterval](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval) 在一个间隔内运行一个命令。
但是，如果您在使用`setInterval`的组件未停止时不停止间隔，它将继续使用不必要的资源运行。

我将谈论如何在 React 中处理`setInterval`

## 😅重现问题

让我们创建一个非常“做作”的例子，在控制台中每秒打印“hello world”。
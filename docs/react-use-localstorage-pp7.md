# 反应-使用-本地存储

> 原文：<https://dev.to/dance2die/react-use-localstorage-pp7>

*照片由[克里斯·斯科特](https://unsplash.com/photos/NEc3YEN1FFw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/hooks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

[React Hooks](https://reactjs.org/docs/hooks-intro.html) 今天刚刚在 [React Conf 2018](https://conf.reactjs.org/) 公布。

你可以查看[官方文档](https://reactjs.org/docs/hooks-intro.html) &也有如下文章，

[![vcarl image](img/455174a90bce4c193c773c0e82aa5125.png)](/vcarl) [## 关于 React Hooks 你需要知道的一切

### 卡尔·维图洛 10 月 25 日 18 时 10 分阅读

#react #javascript](/vcarl/everything-you-need-to-know-about-react-hooks-doh)

所以我就不深究了。

我创建了一个名为 [react-use-localstorage](https://www.npmjs.com/package/react-use-localstorage) 的简单钩子，它允许您在[本地存储](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)中存储状态。

## 🚀先决条件

您需要使用以下 React 版本。

*   **反应**:16 . 7 . 0-α0
*   **react-DOM**:16 . 7 . 0-α0

这是一种前沿技术，不建议在生产中使用。

你可以继续关注 [CodeSandbox](https://codesandbox.io/s/09xj95vxl) 。

## ⚒如何使用它

首先，安装软件包 react-use-localstorage。
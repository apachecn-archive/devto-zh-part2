# 需要设置 tabIndex=0 来启用选项卡导航

> 原文：<https://dev.to/dance2die/need-to-set-tabindex0-to-enable-tab-navigation-4i0a>

*照片由[阿比盖尔·林恩](https://unsplash.com/photos/MNXIZgzKw4U?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

***对自我的注释+问题***

我最近回答了一个堆栈溢出问题，[React focus items list in child component after action from a different child component](https://stackoverflow.com/questions/53327522/)并了解到要启用使用制表符的 HTML 元素导航，需要将 tabindex 值设置为 0。

我对这个问题进行了研究，并回复了。

我仍然不明白的是，为什么不建议使用大于 0 的 tabindex 值。

以下文章敦促您不要使用 0 & -1 以外的任何值。

*   [使用 tabindex](https://developers.google.com/web/fundamentals/accessibility/focus/using-tabindex)
*   [不要使用大于 0 的 Tabindex】](http://adrianroselli.com/2014/11/dont-use-tabindex-greater-than-0.html)

我已经谷歌了一遍又一遍，但仍然找不到任何满意的答案。

至少我能够回答问题，并提供了[沙盒](https://codesandbox.io/s/002pz9kp20)。

第一次使用 [React.forwardRef](https://reactjs.org/docs/forwarding-refs.html) ，效果非常好。

## 问题

有人知道为什么不推荐使用大于 0 的`tabindex`值吗？
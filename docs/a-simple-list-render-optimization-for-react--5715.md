# React 的简单列表渲染优化🏎

> 原文：<https://dev.to/pureooze/a-simple-list-render-optimization-for-react--5715>

乔治·布林赞在 [Unsplash](https://unsplash.com/search/photos/speed?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的封面照片

昨天我看了 Ben Ilegbodu 在 React Alicante 的一个名为“救命”的演讲。我的 React App 慢了 www！其中 Ben 讨论了开发人员可以进行的一些优化，以帮助提高 React 应用程序的性能。他讨论了许多可能出现的潜在瓶颈，比如不必要的 DOM 更新、协调和不必要的对象创建。这是一个非常有趣的演讲，我鼓励你观看(下面的链接)，但我发现最有趣的是他关于不必要的 DOM 更新的第一点。

[https://www.youtube.com/embed/LBzJFcZsPBQ](https://www.youtube.com/embed/LBzJFcZsPBQ)

当试图优化性能时，我们会寻找比大多数其他操作慢的操作，并尽量减少我们执行这些操作的次数。事实证明，更新 DOM 是一项非常耗时的操作。事实上，这非常耗时，因此 React 有一个名为“协调”的过程来尝试和避免不必要的更新。

不幸的是，正如本在他的演讲中所展示的——也是我将在这篇文章中展示的——仍然存在和解无法帮助我们的情况。然而，我们不需要失去希望，因为我们可以做一些简单的调整来解决这个问题。

## 这个🔑至列表

这是一个非常方便的技巧，可以用来优化 React 中列表项的呈现。假设您有一个显示项目列表的页面，其定义如下(单击按钮查看 codesandbox 上的代码):

[![Edit r4y5lj0qxn](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/r4y5lj0qxn)

单击该按钮时，它会向列表中添加一个项目。这将触发对 DOM 的更新，以显示我们的新项目和所有旧项目。如果我们在单击按钮时查看 DOM 检查器，我们会看到以下内容(橙色表示节点正在更新):

[https://www.youtube.com/embed/02oJsIemKwQ](https://www.youtube.com/embed/02oJsIemKwQ)

看到所有列表项是如何更新的了吗？如果我们考虑一下，这实际上似乎不是一个理想的更新。为什么我们不能只插入新节点而不更新所有其他节点呢？其原因与我们如何在列表组件中使用 map 函数有关。
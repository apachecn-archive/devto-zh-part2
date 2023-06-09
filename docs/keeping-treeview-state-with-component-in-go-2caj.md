# 使用 Go 中的组件保持 TreeView 状态

> 原文：<https://dev.to/progrium/keeping-treeview-state-with-component-in-go-2caj>

我回到了在我的 Go UI 框架中重新实现的树形视图原型。它的行为真的没有什么不同，但是我花了整个晚上重新实现它，以处理一个更像 React 的数据模型，其中我们将树的权威状态存储在 Go 和组件中，在那里加载和保存，然后将该状态推送到 jQuery 组件。

我浪费了大量时间试图找到一种方法来重置 [jsTree](https://www.jstree.com/) 中的数据。我不断尝试重新创建元素，然后重新运行加载数据的初始化 JavaScript。它确实不喜欢这样，但直到后来我将 jsTree 元素放在组件外部，然后移除模板，并直接开始从 Go 组件设置和使用 jsTree API 时，我才想到另一种方法。

然后，我必须挂钩 jsTree 中发生的任何更改，以在 Go 组件中复制数据模型中的更改，保存并重新呈现该组件，该组件现在只调用一些 JS 来设置和刷新 jsTree。

最后，我让 localStorage 持久性工作起来，所以它大致在以前的位置，但是现在我可以控制 Go 中的节点，并可以在其上存储任何额外的数据，而不是攻击 jsTree 来在节点上保存额外的数据。

[https://www.youtube.com/embed/fxIebLEQGgk](https://www.youtube.com/embed/fxIebLEQGgk)
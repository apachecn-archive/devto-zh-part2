# 将最近的原型移植到 UI 框架

> 原文：<https://dev.to/progrium/ported-recent-prototype-to-ui-framework-1a75>

我在我的创新项目上做了一些周末工作，其中大部分我没有流，但我想我还是要重述一下。

我所做的是为 go-reflected 编写文档和测试。我的覆盖率达到了 90%,并在此过程中修复了一些 bug。然而，我还没有准备好发布它，因为这是一个完全关于 API 的项目，我仍然不确定 API 不会改变。当它准备好发布时，它只需要一个自述文件。

我做的一些更有趣的离线工作是开始使用我的 UI 框架来移植我最近开始的 UI 原型。这是用普通的旧 JavaScript 和一些 jQuery 库编写的。我的计划是将 jQuery 库隔离到我的 UI 框架中的组件中，这样它们仍然是 jQuery，但是我可以像组件一样与它们交互。

到目前为止，我有树的看法大部分在那里。按钮是组件，所有的粘合和页面布局都是用组件完成的。我原本想我可能需要使用 Vue 来给这个原型添加一些结构，但是既然这个已经可以工作了，我就继续推进这个。它帮助我解决了构建真正的应用程序所必需的框架中的问题，并为进一步的开发清理了原型。

[https://www.youtube.com/embed/q9J-jI_pmPs](https://www.youtube.com/embed/q9J-jI_pmPs)
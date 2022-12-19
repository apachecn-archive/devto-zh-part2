# React 中的表单完全指南

> 原文：<https://dev.to/abelagoi/the-complete-guide-to-forms-inreact-2c4o>

## 一封关于 react forms 未来的信给我

> 表单在任何 web 应用程序中都非常有用。与 angular 和 angularjs 不同，这提供了开箱即用的形式验证。在 React 中，您必须自己处理表单。这带来了很多复杂性，比如如何获取表单值，如何管理表单状态，如何动态验证表单并显示验证消息。有不同的方法和库来帮助解决这个问题，但是如果你像我一样讨厌依赖太多的库，欢迎加入，我们将从头开始引导我们自己的表单。

在`react`中有两种类型的表单输入。我们有`uncontrolled input`和`controlled input`。`uncontrolled input`就像传统的 HTML 表单输入，它们会记住你输入的内容。我们将使用`ref`来获取表单值。
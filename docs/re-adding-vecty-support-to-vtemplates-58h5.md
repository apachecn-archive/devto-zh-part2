# 向虚拟模板重新添加 Vecty 支持

> 原文：<https://dev.to/progrium/re-adding-vecty-support-to-vtemplates-58h5>

我做了一个快速的非计划流来将 Vecty 添加回 vtemplate，因为我将它重构为一个真正的库。不出所料，我必须修复一些东西，但大部分都是小问题。新建筑很棒。我的大部分麻烦来自于确保我的反射库正确地进行反射。就像我之前提到的，反射可能很棘手。

我重写了我的旧 Markdown 演示原型，以使用 vtemplate，它的工作！我希望在组件中加入插槽，这样组件就可以使用它们内部的 HTML，但是我没有时间了。不过，这是下一步，应该不会太难。我想之后我可以开始把它包装成一个用户友好的网络框架，这是它自己的任务。我想让加载组件的 Go 文件旁边的模板文件变得容易，并把我的开发工具改造成一个合适的开发服务器。

[https://www.youtube.com/embed/YbGa38k2OnM](https://www.youtube.com/embed/YbGa38k2OnM)
# 带有 Vue.js 样式模板的 Vecty

> 原文：<https://dev.to/progrium/vecty-with-vuejs-style-templating-f50>

你可能还记得，我最近用 WebAssembly 做了一个 Go 前端框架的快速原型。然而，接下来的步骤涉及编写相当多的代码来实际生成和识别 DOM 更改。在研究了 Vecty 的做法后，我决定自己尝试使用 Vecty，看看效果如何。今天，我没有使用为 GopherJS 设计的 Vecty，而是将它编译成 WebAssembly。

幸运的是，看来老好人布莱恩·科特森已经开始行动了。他开创了一个 Vecty 的分支，做了我理论上你能做的事情。我们在 Twitter 和 stream 上聊了一会儿，他向我展示了他基于 Vecty fork 的 markdown 演示项目。果不其然，我运行了它，它成功了。这就是我今天的全部计划！

所以我决定偶然发现我想用 Vecty 做的下一件事:重新实现我的 Vue.js 样式模板，但是它会吐出 Vecty 对象。这将让您用所有这些函数助手来代替构建 HTML 和呈现组件。

我缓慢但肯定地完成了这个 markdown 演示示例中的所有案例:字符串插值、属性绑定、事件处理程序和组件元素。不幸的是，我没有达到用单个模板来呈现整个页面视图组件的地步，但我已经没有时间了。

这已经很酷了，感觉真好。我将继续研究这个模板系统，顺便说一下，它是一个可选的插入式库。不需要分叉或修改 Vecty。我会尽我所能继续建设城市。

[https://www.youtube.com/embed/7svQTQklPds](https://www.youtube.com/embed/7svQTQklPds)
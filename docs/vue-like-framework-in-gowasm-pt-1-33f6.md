# Go+wasm 中类似 Vue 的框架

> 原文：<https://dev.to/progrium/vue-like-framework-in-gowasm-pt-1-33f6>

我计划做这个已经有一段时间了。这是 Go 中一个
前端 web 框架的概念验证，感觉像是 [Vue.js](https://vuejs.org/) ，使用 [WebAssembly](https://webassembly.org/) 。我在这里的意图并不是说我必须在 Go 中编写代码，而是因为我只想跳过现代 JavaScript 应用程序堆栈中的所有废话。我想要热重装，我想要组件，但是我不想要一个有数百个依赖项的项目，Webpack rat nest，等等。另外，需要简单前端的 Go 项目可以像项目的其他部分一样用 Go 编写。

现在，在考虑一个类似 React 的框架之前，我已经研究过这个问题。事实证明，Gopherjs 是存在的，而且 Gopherjs 是围绕一个 API 构建的，这个 API 几乎可以被 syscall/js API 所替代，当您编译到 WebAssembly 时，就会得到这个 API。然而，换出那个 API*还不能工作*，结果我真的不喜欢 React 在 Go 中的样子。你没有 JSX，所以他们做这些嵌套的函数调用来生成 HTML。如果我们不能内联 JSX，我宁愿它只是一个模板。

Vue.js 是另一个现代框架，它提供了相同的 React 风格组件，但是构建在强大的 Vue 实例原语之上。你创建 Vue 实例的方式很像你在 Go 中创建新结构的方式，所以看起来感觉会更好。

上周，我整理了一个简单的概念证明，它在 Go 中实现了 Vue.js 模板语法。与 Vue 不同，它呈现 HTML 而不是构建 DOM，但是如果我们需要这样做的话，以后可以替换掉。我一直是在 HTML 元素中嵌入指令的模板引擎的粉丝，所以，如果没有别的，在 Go 中有这个 Vue.js 模板引擎可能会很好。

我还做了一些 WebAssembly 原型，确保我可以为 WebAssembly 编译一个简单的 Go 程序，并尝试使用 syscall/js API。服务这个的服务器我变成了一个观察者，它会重新编译 WASM 代码，然后通过 WebSocket 告诉页面刷新。webassembly 二进制文件的加载需要一段时间，但除此之外还是很快的。

然后，我将这些片段组织成一个 Vue 实例，这样我就可以在 Go 中创建一个 struct 并在其上调用 Mount。数据不是反应性的，方法是一项正在进行的工作，但我确实让它们被用作事件处理程序；更改 Vue 实例中的状态并重新渲染。

这里有很多捷径，我只是想看看我能走多远，看看感觉如何。在某个时候，我会重新开始，把事情做好，如果真的做这件事有意义的话。我觉得有可能。

就这样吧，让我知道你的想法或者你有什么问题。如果你想继续关注，请订阅，或者在 Twitch 上关注我。感谢观看

[https://www.youtube.com/embed/3dsmXHNWyQA](https://www.youtube.com/embed/3dsmXHNWyQA)
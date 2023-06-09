# 无状态组件

> 原文：<https://dev.to/independnt/stateless-components-4e3a>

当我第一次使用 React 甚至更高版本时，我大部分时候都让我的所有组件都是有状态的，尽管组件不需要维护状态。但这是一件坏事吗？嗯，简单的回答是肯定的，而且原因不止一个。

无状态组件可以被声明为一个函数，并且可以作为一个完整的有状态组件，只要它有必要的属性。除了不保留自己的状态这一明显的问题之外，无状态组件也不符合组件生命周期方法。它也没有后台实例(内存中存储状态的对象)。所以本质上，无状态组件只是一个返回 JSX 表达式的函数。

能够创建无状态组件不仅可以节省内存，这总是一件好事，还可以让我们保持代码的整洁和简单。最近我在开发一个需要导航条的 React 应用程序，在将导航条导出到它自己的无状态组件之前，它看起来像这样:

[![App_Before](img/64d4652761a330e8037770688aaac11a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ySH-IL-m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jue2b251rb16s3nxr5dq.png)

然而，现在我们所做的，只是简单地创建一个新文件，并删除其中的 html，将其移动到一个无状态组件，并像这样调用它:

[![App_Before](img/30fe6626d9099658311c56075fdc10f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BXPX8T0z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vq0fdy5cseb3x7dzt8zu.png)

注意，当我把它作为一个函数调用时，我使用了 function 关键字，而不是 arrow 函数，原因是对于 arrow 函数，我们绑定了它，这在这个特定的实例中是不需要的。现在，我们可以简单地替换 App.js 中的所有代码，并插入新的无状态组件。

[![App_Before](img/67cc6e216343db69acc5c66020e896a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x4v3t4S2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vebxmo3z8varxfn1yywi.png)

我之所以用。call(this)是将 App.js 中 this 的上下文绑定到子节点，这样我们就可以从 App.js 中访问 this.state 这样的东西。

无状态组件确实改变了我在 React 中的编写方式，我计划尽可能多地使用它们来为我的应用程序节省内存。完美！
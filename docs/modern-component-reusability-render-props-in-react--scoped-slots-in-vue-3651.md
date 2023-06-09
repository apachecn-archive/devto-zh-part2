# 现代组件可重用性:在 Vue 的 React 和 scoped 插槽中呈现道具

> 原文：<https://dev.to/bnevilleoneill/modern-component-reusability-render-props-in-react--scoped-slots-in-vue-3651>

[![](img/9a20685c85d07e98e8e33cec304e41f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bmfW-A28--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AwOPY1wHGN0wc6EESA9Nw7g.jpeg)

所有前端开发人员面临的一个问题是如何使 UI 组件可重用。我们如何以这样一种方式来制作组件，以满足我们现在清楚的狭窄用例，同时还使它们足够可重用以在各种环境中工作？

假设我们正在构建一个自动完成组件:

[![](img/6d1616b9b5d095a08ec6ba93eb23c2b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xdDUbKQJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Av3_4oPZRiulzwLVmCcD2Xg.png)

看看最初的 React 组件代码:

[https://medium . com/media/680362 e 8187 c03d 2477406 e 8 EAC 49 aa 1/href](https://medium.com/media/680362e8187c03d2477406e8eac49aa1/href)

在这个组件中，我们有一些控制核心搜索行为的逻辑，但是我们也指定如何呈现输入和搜索结果。在这个实例中，我们呈现了一个 div，作为一个下拉容器和一个无序列表，其中包含每个结果的列表项。

想想你将如何重用这个组件。当然，如果您想要重现完全相同的行为和视觉效果，您可以使用这个完全相同的组件。但是，如果您想要重用相同的行为，但是组件的可视化略有不同，该怎么办呢？如果您想要重用核心搜索行为，但是为稍微不同的用例添加一些修改，该怎么办？

假设您想要一个类似标签的搜索结果列表，而不是包含搜索结果的下拉列表，该列表总是显示:

[![](img/819996df1810e365c326c0bf7f204633.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tyWWPjHu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXaTfw2O0izYAKGancHTItg.png)

这两个组件的核心功能非常相似:在输入中输入内容来过滤列表。

对于现代 JavaScript 框架现在提供的一些相对较新的工具来说，这是一个完美的用例。这些是 React 中的*渲染道具*和 Vue 中的*作用域插槽*。它们的工作方式非常相似，并且提供了一种将组件的**行为**与其**表现**分离的方法。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 渲染道具在反应过来

首先，让我们看看如何在 React 中使用 render props 来重构我们的 autocomplete 组件。我们现在将有两个组件——一个用于我们的自动完成组件，另一个用于核心的 SearchSelect 组件。

让我们先来看看 SearchSelect 组件:

[https://medium . com/media/1c 4d 06376 a 44 B1 c0b 86514 F3 c 45 f 0528/href](https://medium.com/media/1c4d06376a44b1c0b86514f3c45f0528/href)

这是一个*无呈现的*组件(它不呈现自己的任何标记)。相反，它返回一个叫做*渲染道具*的特殊道具的结果。这个渲染属性接受一个对象，您可以将希望父组件能够访问的任何数据传递到该对象中。

把这个当成正常道具的对立面。通常道具是父母传给孩子的。在渲染道具的情况下，这些是从子对象返回的，因此父对象可以访问它们。

我们的 SearchSelect 组件处理最底层的功能——根据查询字符串过滤选项列表。然后它使用特殊的渲染属性来渲染一个元素。

在父组件中，我们将一个函数传递给 SearchSelect 组件的 render prop。该函数返回一个 React 元素，我们可以将它与 SearchSelect 组件本身的状态和行为结合起来。基本上，这意味着我们能够从父组件的子组件中访问数据。

[https://medium . com/media/30330 a 7 c 6 ff 4 BD 97 EC 85 bbf 37d 83 b5 EB/href](https://medium.com/media/30330a7c6ff4bd97ec85bbf37d83b5eb/href)

实现这一点的关键是我们传递给 render prop 函数的参数。看到我们是如何析构单个对象并在我们的标记中使用这些析构的属性了吗？在子组件中调用 this.props.render()时，该对象应作为参数传递。

所有这一切意味着，我们可以编写任何我们想要的标记，只要我们用 SearchSelect 组件公开的数据和行为适当地混合它。

另外，请注意我们是如何将过滤列表的方法作为一个属性传递进来的。这将允许我们更改选项列表的过滤方式，同时仍然使用 SearchSelect 组件。

让我们看看如何实现类似标签的列表组件。我们使用相同的 SearchSelect 核心组件，只是更改了由 render prop 呈现的标记:

[https://medium . com/media/d 904 B3 f1 befe 858 a 83d 50 c 77 f 74 c 22d 2/href](https://medium.com/media/d904b3f1befe858a83d50c77f74c22d2/href)

查看工作示例:

[https://medium . com/media/a2 AEF 8 f 38 deddc 2 FB 66113218 EB 3 aed 7/href](https://medium.com/media/a2aef8f38deddc2fb66113218eb3aed7/href)

### Vue 中的作用域插槽

现在让我们看看如何使用作用域插槽在 Vue 中实现这一点。首先，这是我们的 SearchSelect 组件(在这个例子中，我使用了全局注册的组件，但是在实际项目中，您可能应该使用单个文件组件):

[https://medium . com/media/bec 185 b8 e 045 eee 1a 93 D3 b5 71 C5 d0s 0/href](https://medium.com/media/bec185b8e045eeee1a93d3b5de71c5d0/href)

如您所见，这看起来非常类似于 React 组件中的渲染道具。这里，我们返回一个默认的*作用域槽*，它传递一个我们想要的对象。在这里，我们给它的结果和我们的搜索方法。

在我们的 Autocomplete 组件中，我们使用 slot-scope 属性来访问子组件中的数据。我们可以析构通过的属性以便于访问，就像 React render prop 参数一样:

[https://medium . com/media/3ea 8235036674 CD 748 a 6 b 0 a 42 b 94170 c/href](https://medium.com/media/3ea8235036674cd748a6b0a42b94170c/href)

查看工作示例:

[https://medium . com/media/0857 cf 6 F6 e 04 ECD 7474 aa 4a E3 BCA 497 c/href](https://medium.com/media/0857cf6f6e04ecd7474aa4ae3bca497c/href)

### 渲染道具的其他用途&作用域槽

创建可重用的接口组件并不是渲染道具和作用域插槽的唯一用途。下面是一些其他的想法，告诉你如何使用它们将可重用的行为封装到一个组件中，然后将该组件暴露给它的父组件。

**数据提供者组件**

您可以使用 render props/scoped slot 来创建一个组件，该组件处理异步获取数据并向其父级公开该数据。这允许您隐藏访问端点、获取结果和处理可能的错误的逻辑，以及在数据获取过程中向用户显示加载状态。

基本组件可能是这样的:

[https://medium . com/media/07e 6800392836 f1 EB 45 a 9334 b 8281252/href](https://medium.com/media/07e6800392836f1eb45a9334b8281252/href)

它接受一个 URL 作为道具，并处理实际的获取逻辑。然后，我们在父组件中使用它:

[https://medium . com/media/6085 caf 2 EB 8 FB 05 EB 62 e 9 CD 522 cf 94 e 9/href](https://medium.com/media/6085caf2eb8fb05eb62e9cd522cf94e9/href)

**观察者(调整大小、相交等。)**

您还可以使用渲染道具/作用域插槽来创建一个组件，作为调整大小或相交观察点的包装器。该组件可以简单地向父组件公开元素的当前大小或交叉点。然后，您可以根据父类中的数据执行任何您需要的逻辑，保持关注点的良好分离。

下面是一个观察自身大小并向其父级公开其高度和宽度的基本组件:

[https://medium . com/media/d0ce 776 a7 ba 80d 718 ad 4035651 CD 6 EC 8/href](https://medium.com/media/d0ce776a7ba80d718ad4035651cd6ec8/href)

我们使用[元素大小调整检测器](https://github.com/wnr/element-resize-detector)库来监听元素大小的变化，并使用 React ref 来获取对实际 DOM 节点的引用。

然后，我们可以在我们的应用程序中轻松使用该组件:

[https://medium . com/media/a 4b 31 BD 49 f 3c daac 44 E1 f 4 AE 5 FBA 0977/href](https://medium.com/media/a4b31bd49f3cdaac44e1f4ae5fba0977/href)

### 结论

使用渲染道具和作用域插槽成功创建可重用组件的关键是能够正确地将**行为**与**表示**分开。每当你创建一个新的 UI 组件时，思考“这个组件的核心行为是什么？我能在其他地方使用这个吗？”

拥有一组使用渲染道具或作用域插槽的核心无渲染组件可以帮助您减少应用程序中的代码重复，并更仔细地考虑您的核心接口行为。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *
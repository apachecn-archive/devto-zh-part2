# 我的祈祷者杂志之旅:前端

> 原文：<https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-front-end-4589>

*备注:*

*   这是系列文章中的第二篇；请参见[介绍](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-introduction-jl5)中的所有内容，以及构建该软件的要求。
*   *以文本“mpj:”开头的链接是指向 myPrayerJournal 的 1.0.0 标签(1.0 版本)的链接，除非另有说明。*

[Vue](https://vuejs.org) 是一个前端 JavaScript 框架，旨在拥有很少的样板文件和仪式，同时仍然呈现一个组件化的抽象，如果需要的话可以扩展到企业级 [<sup>1</sup>](#note-1) 。Vue 组件可以使用内嵌模板或多个文件进行编码(拆分代码和模板)。Vue 还提供单个文件组件(sfc，使用`.vue`扩展名)，允许你将模板、代码和样式放在同一个地方；它们封装了组件，但允许所有三个部分都像在单独的文件中一样表达(而不是，例如，在 JavaScript 文件中将 HTML 片段作为字符串)。Visual Studio 代码的 Vetur 插件为文件的三个部分提供了语法着色支持。

## 布局

使用默认模板，`main.js`是切入点；它创建一个 Vue 实例，并将其附加到一个名为`app`的元素上。该文件还支持注册通用组件，因此不必在希望使用它们的组件中专门导入和引用它们。对于 myPrayerJournal，我们在那里注册了我们的公共组件( [mpj:main.js](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/main.js) )。我们还注册了一些第三方 Vue 组件来支持进度条(在 API 活动期间激活)和吐司(弹出通知)。

`App.vue`也是默认模板的一部分，是`main.js`附加到`app`元素( [mpj:App.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/App.vue) )的组件。它是我们应用程序的主要模板；如果你做过很多模板工作，你可能会认出熟悉的页眉/内容/页脚模式。

这也是我们第一次看到 SFC，所以让我们深入了解一下。最上面的部分是模板；我们使用 [Pug(以前的 Jade)](https://pugjs.org) 作为我们的模板。下一部分包含在`script`标签中，是页面的脚本。对于这个组件，我们导入一个额外的组件(`Navigation.vue`)和来自`package.json`的版本，然后导出一个符合 Vue 预期组件结构的对象。最后，组件的样式包含在`style`标签中。如果样式标签上有`scoped`属性，Vue 将为每个元素生成数据属性，并将声明的样式呈现为只影响具有该属性的元素。myPrayerJournal 不怎么使用作用域样式；如果可行的话，Vue 推荐使用类来降低编译后应用的复杂性。

在`App.js`中还需要注意的是围绕`toast`组件使用的代码。在模板中，它被声明为`toast(ref='toast')`。虽然我们在`main.js`中注册了它，并且可以在任何地方使用它，但是如果我们把它放在其他组件中，他们会创建自己的实例。属性`ref`导致 Vue 在组件的`$refs`集合中生成一个对该元素的引用。这使我们能够从路由器加载的任何组件(我们稍后将讨论)通过使用`this.$parent.$refs.toast`访问 toast 实例，这允许我们在任何时候发送 toast，并让一个实例句柄显示和淡出它们。(如果没有这个，祝酒词会出现在彼此之上，因为独立的实例不知道其他实例当前显示的是什么。)

## 路由

正如 URL 在常规应用中很重要一样，它们在 Vue 应用中也很重要。Vue 路由器是一个独立的组件，但是可以通过 Vue CLI 包含在新的项目模板中。在`App.vue`中，`router-view`项渲染路由器的输出；我们在`main.js`接上路由器。配置路由器非常简单:

*   导入应该显示为页面的所有组件(即，不是模态或公共组件)
*   为每条路线指定路径和名称，并指定组件
*   对于包含数据的 URL(以`:`开头的段)，确保`props: true`是路由配置的一部分

源代码中出现的`scrollBehavior`函数使 Vue 应用程序模仿传统 web 应用程序处理滚动的方式。如果用户按“后退”按钮，或者您以编程方式返回到历史记录中的 1 页，该页将返回到之前的位置，而不是页面的顶部。

为了指定到路由的链接，我们使用`router-link`标签，而不是普通的`a`标签。这个标签接受一个`:to`参数，这个参数是一个具有`name`属性的对象；如果它需要参数/属性，则包含一个`params`属性。 [mpj:Navigation.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/common/Navigation.vue) 散落着前者；后者的结构见 [mpj:RequestCard.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/request/RequestCard.vue#L45) 中的`showEdit`方法(也是编程导航 vs. `router-link`的一个例子)。

## 组件

当软件开发人员听到“组件”时，他们通常会想到可重用的软件，这些软件可以组合在一起构成一个系统。虽然这没有错，但是理解“可重用”并不一定意味着“重用”是很重要的例如，隐私策略( [mpj:PrivacyPolicy.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/legal/PrivacyPolicy.vue) )是一个组件，但是在整个应用程序中重用它将是……好吧，我们只能说是“次优的”用户体验。

然而，这并不意味着我们的组件都不会被重用。我们上面引用的`RequestCard`，在`Journal`组件中的一个循环中使用([mpj:journal . vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/Journal.vue))；日志中的每个请求都会重用它。事实上，即使对于不应该显示的请求，它也被重用；与`shouldDisplay`属性相关的行为使得组件在请求被暂停或处于重复周期时不显示任何内容。代替负责回答问题“我应该显示这个请求吗？”，请求显示回答问题“我应该渲染什么吗？”。这可能看起来不同于典型的服务器端页面生成逻辑，但是一旦我们讨论状态管理(下一篇文章)，这将更有意义。

看看其他一些可重用(和被重用)的组件，页面标题组件( [mpj:PageTitle.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/common/PageTitle.vue) )改变了 HTML 文档的标题，并且可选地在页面顶部显示一个标题。“从现在开始的日期”组件( [mpj:DateFromNow.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/common/DateFromNow.vue) )是最常被重用的组件。每次调用它都会生成一个相对日期，以实际日期/时间作为工具提示；它还设置了一个超时，每 10 秒更新一次。这将保持相对时间同步，即使路由器目的地长时间处于活动状态。

最后，还值得一提的是，sfc 不必定义所有三个部分。根据惯例，并根据您的预期用途，这些部分都不是必需的。“从现在开始的日期”组件只有一个`script`部分，而隐私策略组件只有一个`template`部分。

## 组件交互

在我们深入事件的细节之前，让我们再看一下`Journal`和`RequestCard`。在当前结构中，`RequestCard`将始终有`Journal`作为父级，`Journal`将始终有`App`作为父级。这意味着从技术上来说，`RequestCard`可以通过`this.$parent.$parent.toast`获得它的 toast 实现；然而，这种类型的联轴器非常脆弱 [<sup>2</sup>](#note-2) 。要求将`toast`作为`RequestCard`的参数意味着，无论在哪里实现`RequestCard`，如果给它一个`toast`参数，它都可以显示针对该请求的动作。`Journal`作为`App`的直接后代，可以从其父组件获得对 toast 实例的引用，然后将其传递给子组件；这只是给了我们一层依赖。

在 Vue 中，一般来说，父组件通过 props 与子组件通信(我们看到的是将 toast 实例传递给`RequestCard`)；子组件通过事件与父组件通信。事件的名称没有规定；开发人员提出了它们，它们可以根据需要尽量简洁或描述性。事件可以选择性地包含附加数据。Vue 实例支持订阅事件通知，以及发出事件。如果愿意，我们还可以创建一个单独的 Vue 实例来用作事件总线。myPrayerJournal 在不同的地方使用了这两种技术。

作为第一个的例子，我们来看看`ActiveRequests`([mpj:active requests . vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/request/ActiveRequests.vue))和`RequestListItem`([mpj:request listitem . vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/request/RequestListItem.vue))之间的交互。在`ActiveRequests`(父节点)的第 41 和 42 行，它订阅了`requestUnsnoozed`和`requestNowShown`事件。这两个事件都会触发页面刷新日志中的基础数据。`RequestListItem`，第 67 行和第 79 行，都使用`this.$parent.$emit`来触发这些事件。这个模型允许子节点随意发出事件，如果父节点没有订阅，就不会有错误。例如，`AnswerdRequests`([mpj:answered requests . vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/request/AnsweredRequests.vue))没有订阅这两个事件。(`RequestListItem`不会显示导致这些事件被发出的按钮，但是即使显示了，发出事件也不会导致错误。)

第二种技术的一个例子是专用的父/子事件总线，可以在`Journal`和`RequestCard`中看到。添加笔记和打盹请求是模态窗口 [<sup>3</sup>](#note-3) 。`Journal`没有为每个请求指定一个实例，这可能会增长得相当快，而是只实例化每个模态的一个实例(第 19-22 行)。它还创建专用的 Vue 实例(第 46 行)，并将其传递给模态窗口和每个`RequestCard`实例(第 15、20 和 22 行)。通过这个事件总线，任何`RequestCard`实例都可以触发显示笔记或打盹模式。查看`NotesEdit` ( [mpj:NotesEdit.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/request/NotesEdit.vue) )以了解子进程如何监听事件，以及它如何重置其状态(`closeDialog()`方法)，以便为下一个请求提供新的状态。

这结束了我们的 Vue 路线和组件之旅；下一次，我们将看看 Vuex，以及它如何帮助我们在浏览器中维护[状态。](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-state-in-the-browser-95o)

* * *

 *这是我的总结；我肯定他们有更好的方式来描述它。*

*……而且有点丑，但也许这只是我。*

 *直到开发接近尾声，编辑请求还是一个模态。添加循环使它太忙，所以它必须是自己的页面。*
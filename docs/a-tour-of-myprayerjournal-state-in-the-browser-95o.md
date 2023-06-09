# myPrayerJournal 之旅:浏览器中的状态

> 原文：<https://dev.to/danieljsummers/a-tour-of-myprayerjournal-state-in-the-browser-95o>

*备注:*

*   这是系列文章中的第三篇；请参见[介绍](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-introduction-jl5)中的所有内容，以及构建该软件的要求。
*   *以文本“mpj:”开头的链接是指向 myPrayerJournal 的 1.0.0 标签(1.0 版本)的链接，除非另有说明。*

通量(起源于脸书的一种模式)定义了状态，以及可以改变状态的行为。Redux 是该模式最流行的实现，自然可以很好地与 React 配合使用。然而，其他 JavaScript 框架使用这种模式，因为它确保状态得到合理的管理。(嗯，国家是理智的，但开发商也是！)

作为 Vue 的一部分， [Vuex](https://vuex.vuejs.org) 组件是 Vue 的一个 flux 实现，它提供了一种管理状态的标准方式。他们解释得更详细，所以如果这个概念是新的，你可能想看看他们的“什么是 Vuex？”页面，然后继续。一旦你准备好了，让我们继续，看看它是如何在 myPrayerJournal 中使用的。

## 定义状态

商店( [mpj:store/index.js](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/store/index.js) )导出一个新的`Vuex.Store`实例，它的`state`属性定义了它将跟踪的项目，以及这些项目的初始值。这代表应用程序的初始状态，每当浏览器刷新时都会运行。

在查看我们的商店时，有 4 个项目被跟踪；两项与身份验证相关，两项与日志相关。作为身份验证的一部分(将在以后的文章中进一步探讨)，我们将用户的配置文件和身份令牌存储在本地存储中；这些项目的初始值试图访问这些值。这两个日志相关的项目被简单地初始化为空状态。

## 变异状态

Vuex 突变有一些指导原则。首先，它们必须被定义为商店中`mutations`属性的一部分；外部代码不能简单地将一个状态值更改为另一个状态值而不经过变异。第二，它们必须是同步的；突变必须是快速操作，并且必须按顺序完成，以防止竞争条件和其他不一致。第三，突变不能直接调用；变异是针对当前存储“提交”的。突变接收当前状态作为它们的第一个参数，并且可以根据需要接收许多其他参数。

*(旁注:虽然这些函数被称为“突变”，但 Vuex 实际上是在每次调用时替换状态。这使得一些真正酷的时间旅行调试成为可能，因为工具可以重放状态及其转换。)*

那么，当您需要运行一个异步流程时，比如说，调用一个 API 来获取对日志的请求，您会怎么做呢？这些过程被称为动作，并在商店的`actions`属性中定义。动作接收一个具有状态的对象，但它也有一个可用于提交突变的`commit`属性。

如果你查看 store/index.js 的第[行，你会看到当一个用户的日志被加载时，上述概念被付诸行动](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/store/index.js#L87) [<sup>1</sup>](#note1) 。这一个动作可以提交多达 4 个状态突变。第一个清除之前日志中的内容，用一个空对象提交`LOADED_JOURNAL`变异。第二个通过`LOADING_JOURNAL`突变将`isLoadingJournal`属性设置为`true`。第三个函数在 API 调用成功解析时调用，提交`LOADED_JOURNAL`变异和结果。第四个名为不管它是否工作，再次提交`LOADING_JOURNAL`，这次使用`false`作为参数。

关于我们的突变和动作的名称的一个注意事项 Vuex 团队建议为突变和动作定义常量，以确保它们在存储和调用它的代码中以相同的方式定义。这段代码遵循了他们的建议，这些建议在`store`目录的`action-types.js`和`mutation-types.js`中定义。

## 使用商店

所以，我们有这个很好的数据仓库，它有有限的变异方式，但是我们还没有使用它。既然我们看到了加载日志，让我们以它为例( [mpj:Journal.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/Journal.vue) )。在第 56 行，我们用`...mapState`包装计算出的属性，它将来自存储的数据项作为组件的属性公开。就在那下面，`created`函数调用商店，在组件实例上公开为`$store`，以执行`LOAD_JOURNAL`动作。

模板使用映射的状态属性来控制显示。在第 4 行和第 5 行，如果`isLoadingJournal`属性为真，我们显示一件事，如果不为真，则显示另一件事(实际上是模板的其余部分)。第 12 行使用`journal`属性为日志中的每个请求显示一个`RequestCard` ( [mpj:RequestCard.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/request/RequestCard.vue) )。

我在上面提到了开发人员的理智；在[的最后一篇文章](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-front-end-4589)中，在“组件”标题下，我说过让`RequestCard`决定是否应该显示，而不是由`Journal`决定应该显示哪些组件的逻辑是有意义的。这是我们把这些碎片放在一起的地方。Vuex 悬挂物是反应式的；当来自 it 的数据被渲染到应用程序中时，如果商店发生变化，Vue 将更新渲染。因此，`Journal`只是在日志加载时显示一条“稍等”的提示，并在日志加载后显示“所有请求”。`RequestCard`仅在需要显示请求时显示。而且，这背后的整个“大脑”是启动整个过程的东西，调用`LOAD_JOURNAL`动作。我们没有移动东西，我们只是简单地展示事物的本来面目！

`Navigation` ( [mpj:Navigation.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/common/Navigation.vue) )是另一个基于其显示关闭状态的组件，并利用该状态的反应性。通过映射`isAuthenticated`，可以根据用户是否登录来显示或隐藏许多菜单项。通过映射`journal`和计算属性`hasSnoozed`,“暂停”菜单链接不显示是否有暂停的请求；然而，第一次来自日志的请求被暂停时，这个请求出现 ***只是因为状态改变了*** 。

这是决定使用 Vue 作为前端 [<sup>2</sup>](#note-2) 的原因之一，也是整个应用程序中我最喜欢的特性之一。*(不过，你可能已经知道了。)*

我们现在已经参观了我们的有状态前端；下一次，我们将看看[我们用来将数据放入其中的 API](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-api-56l5)。

* * *

 *一语双关不是原意，而是现在！*

 *其他的则是缺乏仪式感和单一的文件构件结构；这两者看起来都很直观。*
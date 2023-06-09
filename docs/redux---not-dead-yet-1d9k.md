# Redux -还没死！

> 原文：<https://dev.to/markerikson/redux---not-dead-yet-1d9k>

我是 Redux 维护者。最近有很多关于 Redux 的困惑、声明和错误信息，我想帮助澄清这些事情。

> 最初发表在我在 blog.isquaredsoftware.com 的博客上，作为我在 T2 的博客回答系列的一部分。

## TL；速度三角形定位法(dead reckoning)

### Redux 是死了，奄奄一息，被弃用，还是即将被取代？

号码

### 有没有不需要 Redux 的情况？

当然，但这一直都是事实。

## 较长的解释

最近有一大堆评论和文章，归结起来就是有人问“Redux 死了吗？”，或者断言“工具 X 代替 Redux”。我将回顾几个混淆的来源，并解释实际发生了什么。

### “Redux 使用过度”

#### 这是哪里来的？

Redux 已经快三年了。在《JS 图书馆岁月》里，那就像...它很快变得非常流行，结果，很多人被告知他们*不得不*使用 Redux，而没有真正理解其中的权衡以及何时使用 Redux 是有意义的。因此，不可避免地出现了一些反弹，人们已经在寻找替代方案，包括采用其他国家管理库或创建自己的库。

作为其中的一部分，大约一个月前有一波关于 Redux 如何被过度使用的推文。其中一封来自 Cory House，他是 React 社区中一位著名的作家兼教师。这条推文和其他各种推文被大量转发，随后的讨论在推特上传播了一段时间。

#### 清理混乱

Redux 的维护者(先是丹·阿布拉莫夫和安德鲁·克拉克，现在是蒂姆·多尔和我)总是说[你可能不需要 Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367) 。有*极好的*理由使用 Redux，但它可能不是最适合你的情况。**像任何工具一样，在决定使用某样东西之前，理解其利弊很重要**。

我在推特上看到很多评论，说人们已经选择从 Redux 转向其他东西。但是，与此同时，**我自己的估计是，所有 React 应用程序中有 50-60%使用 Redux** ，加上它在 Angular、Ember 和 Vue 等其他 JS 框架中的使用，这是一个不会在一夜之间消失的用户群。(在社交媒体上以闪电般速度谈论的内容和人们在“真实世界”中实际做的事情之间也有很大差距。)

同样值得注意的是 **Redux 并不归脸书所有——它是一个独立的开源项目**。它的两位创始人(丹·阿布拉莫夫和安德鲁·克拉克)现在都在脸书工作，但蒂姆·多尔和我与脸书没有任何关系。我们与 React 团队交谈，以帮助协调未来的计划，但 Redux 不属于他们。

### “新的上下文 API 可以替代 Redux”

#### 这是哪里来的？

React 16.3 引入了一个新的稳定版本的`context` API，旨在取代旧的不稳定 API。Context 专门用于将数据传递给深度嵌套的 React 组件的用例。这是一些人选择使用 Redux 的原因之一，因此有人声称新的上下文 API 将取代 Redux。

#### 清理混乱

是的，新的上下文 API 对于将数据传递给深层嵌套的组件来说将会很棒——这正是它的设计目的。**如果你只是用 Redux 来避免传递道具，那么上下文*可以用*代替 Redux——但是你可能一开始就不需要*用*Redux**。Context 也没有提供像 Redux DevTools 这样的东西，没有提供跟踪状态更新的能力，没有提供添加集中式应用程序逻辑的中间件，也没有提供 Redux 支持的其他强大功能。

### “graph QL 可以代替 Redux”

#### 这是哪里来的？

有些类似的是，GraphQL 和 Apollo 客户机周围有很多噪音。曾经有文章专门宣称“GraphQL 会让你取代 Redux”。此外，Apollo 有一个新的`apollo-link-state`插件，可以处理客户端状态，有讨论说这也可以帮助取代 Redux。

#### 清理混乱

我同意通过 GraphQL 获取数据，尤其是使用 Apollo，可能会减少或消除与数据获取相关的冗余代码。同样，如果这就是你使用 Redux 的全部目的*的话，在把所有的数据获取处理都转移到 Apollo 之后，你可能就不需要 Redux 了。我甚至可以说`apollo-link-state`可能可以处理大多数其他客户端状态逻辑，我认为 Apollo 自带了一个 DevTools 设置。Apollo 团队一直在做一些非常漂亮的工作，虽然我不喜欢*看到人们放弃 Redux，但最终我们都希望构建伟大的应用来帮助我们的用户。但是，和上下文一样，我要说肯定有 Redux 比 GraphQL + Apollo 更好的用例，并且可能不需要在整个架构中进行太多的投入。如果您需要做的不仅仅是获取数据或更新几个本地状态值，比如通过页面重载保存用户数据或实现复杂的工作流逻辑，这一点尤其正确。

### “Redux 正在被 React 的某个东西取代”

#### 这是哪里来的？

最后， [Dan Abramov 最近在 JS Conf 冰岛](https://reactjs.org/blog/2018/03/01/sneak-peek-beyond-react-16.html)上做了一个很棒的演讲，他演示了 React 的“异步渲染”的两个即将到来的方面:时间切片将允许 React 拆分更新计算以实现更平滑的更新，而“React 悬念”将允许深度嵌套的组件延迟其渲染，直到提取的数据可用。不幸的是，在演讲结束后不久，一个以撰写误导性和糟糕的 React 文章而闻名的网站发布了一个帖子，声称“丹·阿布拉莫夫宣布了一个新的‘未来提取器’库来取代 Redux”，并链接了肯特·C·多兹的一条推文作为证据。

#### 清理混乱

社交媒体的一个问题是错误信息很容易迅速传播。尤其是在这种情况下，因为**那篇广泛传播关于 Dan 宣布一个“未来掠夺者”库的文章是完全错误的！**丹的声明纯粹是关于异步反应能力，和【Redux 没有任何关系。此外，Kent 关于 Redux 被替换的推文实际上是一条关于该谈话的笑话 Twitter“现场评论”帖子中的一条笑话推文。这篇文章要么是对 React 生态系统的完全误解，要么是故意试图传播混乱和 FUD。

## Redux 的未来

作为 Redux 的维护者，我可以向你保证 **Redux 不会去任何地方**。Redux 核心库是稳定的，我们实际上已经有了 4.0 测试版。尽管有重大的版本变动，但这实际上只是清理一些边缘情况和改进打字稿的打字。除此之外，[Redux 生态系统欣欣向荣](https://dev.to/markerikson/react-boston-2017-presentation-you-might-need-redux-and-its-ecosystem-315c-temp-slug-5525438)。

***真正*有趣的近期工作是围绕 React-Redux 库**展开的。我们将对其进行更新，以更好地与 React 的异步功能配合使用。我们有[一个公开的问题来讨论 Redux 将如何与 async React](https://github.com/reactjs/react-redux/issues/890) 一起工作，我创建了[一个概念证明 PR，它更新`connect`以使用新的上下文 API](https://github.com/reactjs/react-redux/pull/898) 。在接下来的几个月里还需要做更多的工作，但是我们致力于确保 React 和 Redux 继续成为构建应用程序的最佳选择。

## 准备学习 Redux？

所以记住所有这些，现在是学习如何使用 Redux 的好时机。和往常一样，我将以一些更多资源的链接结束:

*   [官方还原文档](https://redux.js.org/)。我刚刚更新了自述文件，加入了学习资源的快速列表，还有一个更广泛的 [Redux 学习资源](https://redux.js.org/introduction/learning-resources)列表，涵盖了使用 Redux 的许多方面。
*   我有一篇博文，里面有我建议的学习 Redux 的资源清单
*   我的 [React/Redux 链接列表](https://github.com/markerikson/react-redux-links)有一个很大的部分[充满了 Redux 教程](https://github.com/markerikson/react-redux-links/blob/master/redux-tutorials.md)
*   我的[“实用 Redux”博客教程系列](http://blog.isquaredsoftware.com/series/practical-redux/)通过构建一个示例应用程序演示了各种真实世界的 Redux 和 React 技术，我还在 Educative.io 上创建了[一个交互式“实用 Redux”课程。](https://www.educative.io/collection/5687753853370368/5707702298738688)

我也鼓励人们真正理解何时以及为什么应该使用 Redux。一些建议的文章:

*   关于“我应该何时使用 Redux？”的 Redux 常见问题条目
*   丹·阿布拉莫夫的文章[你可能不需要 Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367) ，它讨论了 Redux 要求你做出的权衡以及你得到的一些回报
*   我的两部分帖子[Redux 之道，第一部分——实现和意图](https://dev.to/markerikson/idiomatic-redux-the-tao-of-redux-part-1---implementation-and-intent-1c1i-temp-slug-5403642)和[Redux 之道，第二部分——实践和哲学](https://dev.to/markerikson/idiomatic-redux-the-tao-of-redux-part-2---practice-and-philosophy-hml-temp-slug-2020452)，探究 Redux 如何设计背后的历史和意图，它的*意味着如何使用*，以及为什么存在常见的使用模式。

### 亲自跟我学 Redux！

除了所有的文章、帖子和链接，**我将通过 Workshop.me 教授一系列“Redux 基础知识”研讨会！**我很高兴有机会花几天时间帮助人们了解 Redux 的工作原理和使用方法。

**[我的第一个“Redux Fundamentals”工作坊是 4 月 19-20 日，在纽约市](https://workshop.me/2018-04-react-redux/?a=mark)** ，其他工作坊地点和日期在 TBD，今年晚些时候。**纽约研讨会的门票仍然有售！**如果你不在纽约，或者已经习惯了 Redux，把这个消息告诉你认识的可能感兴趣的人吧！:)
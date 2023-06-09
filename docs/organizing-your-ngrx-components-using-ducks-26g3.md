# 使用 ducks 组织 ngrx 组件

> 原文：<https://dev.to/michaeljota/organizing-your-ngrx-components-using-ducks-26g3>

我最近又开始使用 ngrx，但是我找不到按照团队建议组织代码的方法。我注意到大多数风格指南使用的是一种按类型分类的结构，流行的 amount React 项目不鼓励使用有棱角的风格指南，它强烈建议使用按特征分类的结构。他们甚至将应用程序的功能分为核心、共享和其他功能，在大多数情况下，核心模块应该导入应用程序的所有核心组件和服务，并由主模块加载，共享模块应该对所有共享组件使用桶模块，并应由功能模块导入，他们建议所有其他功能模块延迟加载。

社区似乎对何时使用每种结构有强烈的意见，但是大多数时候，推荐使用基于特性的文件夹，因为它比基于类型的文件夹更容易扩展。

当我使用 Angular 和 ngrx 时，我使用的是按类型的文件夹，而我使用的 React 项目中，我们的团队使用的是按类型的文件夹，我们经常尝试实现 [ducks](https://github.com/erikras/ducks-modular-redux) 提议。这个提议有一个扩展，可以用在一个按断裂的文件夹结构中，名为[重鸭](https://github.com/alexnm/re-ducks)。因此，基于重新鸭子的建议，并使用有棱角的风格指南，我开发了一个英雄之旅应用程序。

我在 [ngrx-ducks](https://github.com/michaeljota/ngrx-ducks) 存储库中记录了结果，我将在这里尝试解释它。

### 商店

在`@ngrx`中，商店必须被配置为一个模块，使用与路由模块相似的方法。因此，我采纳了大多数关于 ASG 路线的建议，并申请了项目中的商店。

### 鸭子

Reducers、Actions、Effects 和 Selectors 在提案中被称为同名的 ducks。在那个提议中，它假设您使用的是一个按类型的文件夹结构，并建议使用一个 ducks 文件导出您所有的 ducks。然而，在 re-ducks 建议中，它假设您使用的是一个按特征的文件夹结构，并建议将 ducks 的每个部分分离到它自己的文件中，并在 ducks 文件夹中的一个索引文件中重新导出它们。

然而，在本提案中，我假设您的大多数减速器将与一个组件相关。假设一个 reducer 将处理一个组件的状态，允许您将您的鸭子放在一起。

为了假设这一点，我还假设您正在使用一个按特性的文件夹结构，并且您正在为您的每个特性声明一个模块。这个模块应该有自己的存储，应该导入减速器的状态并初始化该特性的状态。此外，它应该导出一个特性选择器，供每个特性组件选择器使用。

# 一言以蔽之

显然，这种方法并不适合所有人。如果你觉得你可以使用来自`@ngrx`的当前风格指南，那完全没问题。我只是需要在更大的项目中使用的替代方案。如果你有任何建议，请在评论中留下，或者更好的是，在回购中打开一个问题。

完整的提案很大，无法包含在这里，但您可以在 [ngrx-ducks](https://github.com/michaeljota/ngrx-ducks) 中阅读。

我知道我这么做是考虑到我自己，但我真的希望这能帮助其他人。

非常感谢你阅读这篇文章，我真的很感激你对此的想法。我不喜欢在大型项目中有这么多的经验，但我确实认为横向扩展是一个很好的方法。
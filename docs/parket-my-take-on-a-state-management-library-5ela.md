# 帕克特:我对国家管理图书馆的看法

> 原文：<https://dev.to/hrmny/parket-my-take-on-a-state-management-library-5ela>

[![](img/bc45001eca1a74f69bc79f6ab9bfa79e.png)T2】](https://github.com/ForsakenHarmony/parket)

[^点击这里进入 github 回购](https://github.com/ForsakenHarmony/parket)

## 你会如何向一个从未听说过 Parket 的人描述它？

Parket 是一个状态管理库，一个著名的例子是 Redux。它很大程度上受 mobx-state-tree 的启发，因为文件相当大，所以我没有使用它。

Parket 允许您创建带有状态、动作和视图的模型，稍后可以通过实例化它们来使用这些模型，并且可以相互嵌套。

## Parket 是如何工作的？

Parket 内部使用代理。代理是一个新特性，它允许你包装对象并管理获取和设置对它的访问，这就像给每个属性添加一个 getter 和 setter，但也适用于新添加的属性。

## Parket 与其他解决方案有何不同？

许多状态管理库似乎非常关注不可变性，每次状态更新都必须返回一个不可变的对象。我只是通过代理来管理可变性，所以你不能在动作之外设置任何东西，你也不必返回任何东西或调用 setState 之类的，因为它在监听状态变化并基于这些变化发送事件。

### 一个基本例子

```
import model from 'parket';

const Person = model('Person', {
  initial: () => ({
    firstname: null,
    lastname: null,
  }),
  actions: state => ({
    setFirstName (first) {
      state.firstname = first;
    },
    setLastName (last) {
      state.lastname = last;
    },
  }),
  views: state => ({
    fullname: () => `${state.firstname}  ${state.lastname}`,
  }),
});

const instance = Person({ firstname: 'Tom' }); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，状态被传递给动作，动作只是修改它而不做任何特殊的事情。同样的事情也发生在视图上，但是这些视图只能从状态中读取，与从外部访问它有相同的限制。视图在每次状态改变时都会得到更新。

任何熟悉 mobx-state-tree 的人可能都会看到相似之处。您为状态定义了一个模型，并可以重用它，这对于嵌套模型非常有用，例如待办事项列表中的待办事项。我还将 mst todomvc 示例改编为 parket，您可以在回购中找到它。

当实例化模型时，可以传递一个对象来合并到状态中。(我可能会将其更改为只传递给初始函数，因为它目前可以覆盖嵌套对象)

### 异步

```
const Async = model('Async', {
  initial: () => ({
    loading: false,
    result: null,
  }),
  actions: self => ({
    async doSomethingAsync() {
      self.loading = true;
      // be aware that you should handle errors ( /rejections )
      self.result = await somethingAsync();
      self.loading = false;
    },
  })
}); 
```

Enter fullscreen mode Exit fullscreen mode

正如您在这里看到的，parket 真的不关心您的动作做了什么，或者说它是什么，它只是监听状态变化。

## 你为什么开发 Parket？

我不久前发现了 mobx-state-tree，并立即喜欢上了它，但它和对 mobx 的依赖在文件大小方面相当大。在 preact 核心团队中，我显然必须做得更小，所以在失败 2 次后，Parket 诞生了(约 1.5kB)。
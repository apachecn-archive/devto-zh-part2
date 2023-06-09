# 查看 Redux 的第一次提交

> 原文：<https://dev.to/captainsafia/looking-at-the-first-commit-of-redux-21m8>

我的朋友詹姆斯·鲍威尔最近给我发短信，建议我做一些“代码考古”式的代码阅读。本质上，我会检查一个代码库，进入初始提交，然后通过浏览项目的提交来构建项目的故事。

我起初对走那条路犹豫不决。我不认为自己是什么历史学家。此外，我进行这些代码阅读的主要原因是为了回答我对特定项目的疑问(例如，Git 中的 commit 是如何工作的？或者 Node.js 如何加载模块？).但是，我想我可能会尝试一些新的和不舒服的东西，所以这里去。

我试图找出解决这个问题的最佳方法。我可以深入一个项目的提交日志，并尝试围绕发生的变化建立一个叙述，但老实说，这不是我有时间做的那种工作。我可以查看与项目的每个标记版本相关联的代码，但这并不比前一种情况容易。最后，我决定只查看项目中的第一次提交。最初的承诺有一种怀旧和浪漫的感觉。我觉得回到过去，看看业界一些流行的开源项目是从哪里开始的，会很有趣。

我想做一个相对较新的项目，并且我有在生产环境中使用的丰富经验。我决定在 [redux](https://github.com/reactjs/redux) 项目上做一次代码考古挖掘。对于那些不熟悉 JavaScript 状态管理的人，我将在下面给出一个快速入门，但是了解更多的最好地方是 [Redux](https://redux.js.org) 主页。

Redux 被称为“可预测的状态容器”它允许您为您的 web 应用程序创建一个中央存储，您可以在其中定义应用程序的状态以及操作该状态可以采取的操作。如果这听起来很奇怪，我会在后面的文章中澄清。此外，上面提供的 Redux 主页的链接有一些有用的资源，这些资源是由比我更擅长解释事物的人编写的。

好吧！我们开始挖吧。我首先将 Redux 代码库克隆到我的本地机器上，并检查项目中最早的提交。

```
captainsafia@eniac ~/dev> git clone https://github.com/reactjs/redux.git && cd redux/
Cloning into 'redux'...
remote: Counting objects: 13825, done.
remote: Compressing objects: 100% (34/34), done.
remote: Total 13825 (delta 11), reused 9 (delta 5), pack-reused 13786
Receiving objects: 100% (13825/13825), 5.87 MiB | 4.36 MiB/s, done.
Resolving deltas: 100% (8743/8743), done.
captainsafia@eniac ~/dev/redux> git rev-list HEAD | tail -n 1
8bc14659780c044baac1432845fe1e4ca5123a8d
captainsafia@eniac ~/dev/redux> git checkout 8bc14659780c044baac1432845fe1e4ca5123a8d
Note: checking out '8bc14659780c044baac1432845fe1e4ca5123a8d'.

...

HEAD is now at 8bc1465... Initial commit 
```

Enter fullscreen mode Exit fullscreen mode

哇！Redux 代码库中的初始提交。Git 让时光倒流变得如此容易，让我们看到事物是如何演变的，这真是太酷了。真的给了你视角，你知道吗？

我首先查看了该提交下存放的文件。

```
captainsafia@eniac ~/dev/redux> ls -1a
.
..
.babelrc
.eslintrc
.git
.gitignore
.jshintrc
LICENSE
README.md
index.html
package.json
server.js
src
webpack.config.js 
```

Enter fullscreen mode Exit fullscreen mode

这比现在代码库中的文件和文件夹要少得多。这无疑将有助于理解 Redux 的核心概念，而不会陷入随着项目的发展而添加到项目中的架构中。

我想看的第一个文件是 [`src/redux/connect.js`](https://github.com/reactjs/redux/blob/8bc14659780c044baac1432845fe1e4ca5123a8d/src/redux/connect.js) 。这里定义的`connect` React 组件实际上并不是 Redux 中现有代码库的一部分。相反，它是`react-redux`库的一部分，提供了连接 Redux 以做出反应的组件。在最初的提交中情况并非如此，因为在那一点上，Redux 代码库在与 React 结合使用时，在很大程度上是 Redux 状态容器的一个未完成的证明。同样地，`connect`组件装饰器管理将状态的观察者附加到组件和从组件分离，处理状态的变化，以及绑定与组件相关的动作。

我想看的第二个文件是 [`src/redux/createDispatcher.js`](https://github.com/reactjs/redux/blob/8bc14659780c044baac1432845fe1e4ca5123a8d/src/redux/createDispatcher.js) 。在我看来，这是代码库中最有趣的部分。首先，dispatcher 负责调度操作(因此得名)和提供状态订阅。这个文件中定义的主函数`createDispatcher`有如下函数声明。

```
export default function createDispatcher(stores, actionCreators, initialState) 
```

Enter fullscreen mode Exit fullscreen mode

`initialState`是我们希望用来初始化状态的默认数据树。初始状态通常是一个 JavaScript 对象，如下所示。

```
{
  value: 10
} 
```

Enter fullscreen mode Exit fullscreen mode

`actionCreators`是返回普通 JavaScript 对象的函数，代表 Redux 中的动作。动作创建者应该是这样的。

```
function decrement() {
  return { type: DECREMENT };
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，`stores`将上述两个实体链接在一起。它们描述了一个特定的动作，比如减量动作，应该如何影响状态中的信息。

`createDispatcher`函数返回以下函数定义。

```
return {
  bindActions,
  observeStores,
  getState
}; 
```

Enter fullscreen mode Exit fullscreen mode

函数返回应用程序的当前状态。那里没什么真正有趣的事。

[`observeStores`函数](https://github.com/reactjs/redux/blob/8bc14659780c044baac1432845fe1e4ca5123a8d/src/redux/createDispatcher.js#L53)将它应该附加观察者的树的部分(`pickStores`)以及当在树的该部分上检测到变化时它应该做什么(`onChange`)作为参数。

最后， [`bindActions`函数](https://github.com/reactjs/redux/blob/8bc14659780c044baac1432845fe1e4ca5123a8d/src/redux/createDispatcher.js#L83)获取一组动作，并将它们与一个`dispatch`函数相关联，该函数实际上可以计算当一个特定动作被调用时状态应该如何变化。

据我所知，`createDispatcher`文件实际上是初始提交的核心。而且只有 99 行代码(带空格)！它建立了 Redux 生态系统中的许多核心概念(存储、动作和状态)，并概述了它们之间的关系(当动作被分派时，它们影响状态，存储是动作和状态的持有者，等等)。

Redux 代码库的最初提交与这样一个事实密切相关，即它最初是作为 React 应用程序的状态容器的概念验证而开始的(但肯定已经过了这个阶段)。从我个人的角度来看，最初的提交看起来不像是一个流行的 JavaScript 库的代码，更像是我为了向朋友展示一个概念或想法而编写的代码。这一切都说明了大事从小事做起！
# 在 React 中缓存组件数据

> 原文：<https://dev.to/washingtonsteven/caching-component-data-in-react--4m85>

## 问题

因此，我最近在我正在开发的一个 [React 应用](https://github.com/washingtonsteven/react-vn)中遇到了一个问题(为了好玩):

我有一个对象数组，可能会变得很大。这些对象中的每一个都有一个`id`，所以实现`Array.find`来获得我想要的项目应该可以。

```
 const nodes = [
  { id:"abc", content:"Lorem ipsum"},
  { id:"def", content:"Dolor sit" },
  // ...
]

const getNode = id => nodes.find(n => n.id === id);

console.log(getNode('abc'));
// => { id:"abc", content:"Lorem ipsum" } 
```

Enter fullscreen mode Exit fullscreen mode

然而，当`nodes`变大时，`Array.find`将迭代*的每一项*，这可能会变得很昂贵。所以我们可以实现某种“缓存”来帮助解决这个问题。

```
const nodes = [
  { id:"abc", content:"Lorem ipsum"},
  { id:"def", content:"Dolor sit" },
  // ...
];

const keyedNodes = {}

const getNode = id => {
  if (!keyedNodes[id]) {
    keyedNodes[id] = nodes.find(n => n.id === id);
  }

  return keyedNodes[id];
}

console.log(getNode('abc'));
// => { id:"abc", content:"Lorem ipsum" }

console.log(getNode('abc'));
// This time we are coming from keyedNodes!
// => { id:"abc", content:"Lorem ipsum" } 
```

Enter fullscreen mode Exit fullscreen mode

似乎很简单！

## 反应过来的数据

作为一个相对的 React 新手，我把它灌输到了我的头脑中，应用程序中的数据来源应该是:`prop`或`state`。`props`保存组件接收的数据(它不应该自我更新)，而`state`保存组件的当前状态，这个组件完全可以控制它(当然是通过`setState`)。).

有了这些信息，我开始使用组件的状态来实现这种记忆策略，由于`setState`的异步特性，这变得非常混乱。

### [查看 CodeSandbox 上的演示](https://codesandbox.io/s/jvpz0qxr55)

[![Edit Memoize Component Data in state](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/jvpz0qxr55)

看那个讨厌的`getNode`函数！在实际更改节点之前，我们必须等待状态得到解决，否则我们可能会在错误的时间覆盖状态。(`changeNodes`中的状态没有`getNode`中的`keyedNodes`更新，会将`keyedNodes`对象覆盖为空白！一点帮助都没有！).

我忍受了一段时间，然后我看了看[肯特·多德关于使用类字段](https://egghead.io/lessons/egghead-use-class-components-with-react)的视频(这对避开那些讨厌的`bind`调用很有用)。这提醒了我阶级领域的存在([的排序)...](https://github.com/tc39/proposal-class-fields)， [Create React App](https://github.com/facebook/create-react-app) 的默认 babel 配置允许使用它们)。因此，我不仅可以将`state`作为类字段放入(与箭头函数一起为组件回调创建正确绑定的函数)，而且任何其他东西也可以放在这里！

*注意:你实际上也不需要为此使用类字段！`constructor`中的`this.keyedNodes`也会做同样的事情。*

因此，将`keyedNodes`放在一个类变量上会产生类似的东西，但是更容易阅读:

### [查看 CodeSandbox 上的演示](https://codesandbox.io/s/44m6073m0)

[![Edit Memoize Component Data in a class field!](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/44m6073m0)

## 缺点？

这样做的主要缺点是 React 不会查看除了`state`和`props`之外的类字段来控制更新的呈现。因此，无论出于什么原因，如果您需要将这个缓存绑定到渲染循环，那么您只能使用第一种方法:保持缓存处于状态。

然而，我相信在大多数情况下，缓存不需要由 React 本身触发或更新。缓存应该跟随组件的任何更新，而不是抢占它们。

为此，如果`this.state.nodes`刚刚经历了一次更新，也许我们可以在`componentDidUpdate`中添加一个来清除缓存，这样我们就不会潜在地处理旧数据。但是这表明需要小心对待类字段中的数据。

另一个副作用是这些类字段被绑定到*实例*，而不是*原型*。这意味着页面上使用相同数据集的另一个组件必须构建自己的缓存，并且不能借用它。这可以通过将缓存置于状态，将缓存提升到父组件，或使用带有`Cache`组件(或`withCache` HOC)的渲染道具(或 HOC)来解决。

## 结论——慎用！

在类或对象字段中保存特定于组件的(甚至是特定于实例的)数据对于一些快速优化非常有用，或者只是保存一些不一定需要在 React 渲染循环中捕获的数据，在 React 渲染循环中,`setState`的异步特性会导致奇怪的问题和竞争情况，从而导致不可读的代码。**然而**，因为类字段在渲染循环之外，所以对数据的更新不会被 React 管理，如果使用不当，可能会导致问题。存储需要随时访问的数据的简单缓存对此非常有用，因为对于未命中，缓存自然会退回到 React 状态，并且应该“跟随领导者”从状态中获取事实的来源。
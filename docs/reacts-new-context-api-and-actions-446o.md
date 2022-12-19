# React 的新上下文 API 和动作

> 原文：<https://dev.to/washingtonsteven/reacts-new-context-api-and-actions-446o>

*图片:* [丹尼尔·沃森](https://unsplash.com/@danielwatsondesign?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Daniel Watson")

*编辑:2018 年 4 月 2 日- [有人向我指出](https://dev.to/dan_abramov/comment/2nga)这篇文章中的例子存在性能问题，其中`render`被不必要地调用到消费者身上。我已经更新了文章、示例和代码沙箱来纠正这个问题。*

新的 React 上下文 API ( ~~即将推出~~现已发布！在 React 16.3 中)是对 React 中旧的上下文概念的大规模更新，它允许组件在父>子关系之外共享数据。有许多例子和教程展示了如何从上下文提供的状态中读取，但是您也可以传递修改该状态的函数，以便消费者可以用状态更新来响应用户交互！

## 为什么要语境？

上下文 API 是一种解决方案，可以帮助解决复杂状态带来的许多问题，这种复杂状态意味着要与应用程序中的许多组件共享:

1.  它为感兴趣的组件可以直接访问的数据提供了单一的真实来源，这意味着:
2.  它避免了“prop-drilling”问题，即组件接收数据只是为了将数据传递给它们的子组件，这使得很难推断状态的变化在哪里发生(或没有发生)。

### B-but Redux！

Redux 也是一个解决这些问题的神奇工具。然而，Redux 也带来了许多其他特性(主要是关于状态和归约器的纯度的实施)以及所需的样板文件，这些文件可能会很麻烦，这取决于需要什么。为了透视，Redux 使用(旧的)上下文 API。

看看丹自己写的这篇文章:[你可能不需要 Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)

## 语境是做什么的？

有很多关于这方面的文章([我特别喜欢这个](https://medium.com/dailyjs/reacts-%EF%B8%8F-new-context-api-70c9fe01596b))，所以我不想详细介绍它是如何工作的。到目前为止，您已经看到了这些示例，它们大多缺少一些东西:*如何在提供者中更新状态。那种状态就在那里，每个人都可以读取它，但是我们如何向它写入呢？*

## 简单上下文示例

在许多这样的例子中，我们定制了一个提供者来包装 React，它有自己的状态作为`value`传入。像这样:

### `context.js`

```
import React from "react";

const Context = React.createContext();

export class DuckifyProvider extends React.Component {
  state = { isADuck: false };
  render() {
    const { children } = this.props;
    return ( 
      <Context.Provider value={this.state}>
        {children}
      </Context.Provider>
    );
  }
}

export const DuckifyConsumer = Context.Consumer; 
```

Enter fullscreen mode Exit fullscreen mode

看似简单，足够了。现在我们可以使用`DuckifyConsumer`来读取该状态:

### `DuckDeterminer.js`

```
import React from "react";
import { DuckifyConsumer } from "./context";

class DuckDeterminer extends React.Component {
  render() {
    return (
      <DuckifyConsumer>
        {({ isADuck }) => (
          <div>
            <div>{isADuck ? "quack" : "...silence..."}</div>
          </div>
        )}
      </DuckifyConsumer>
    );
  }
}

export default DuckDeterminer; 
```

Enter fullscreen mode Exit fullscreen mode

## 传递函数

现在，如果我们想模仿一个女巫把东西变成一只鸭子会怎么样呢？我们需要将`isADuck`设置为`true`，但是如何设置呢？

我们传递一个函数。

在 Javascript 中，函数被称为“一级”,这意味着我们可以将它们视为对象，并传递它们，甚至在状态和提供者的`value` prop 中。如果维护者选择`value`而不是`state`作为道具的原因是为了允许这种概念分离，我不会感到惊讶。`value`可以是任何东西，尽管可能基于`state`。

在这种情况下，我们可以向`DuckifyProvider`状态添加一个`dispatch`函数。`dispatch`会接受一个动作(定义为一个简单的对象)，调用一个 reducer 函数(见下文)来更新提供者的状态(*我在某个地方看到过这种实现一个类似 redux 的 redux 的 reducer 的方法，但不确定是在哪里。如果你知道在哪里，让我知道，所以我可以正确地信贷来源！*)。

我们为提供者将`state`传递到`value`中，因此消费者也可以访问那个`dispatch`函数。

这看起来是这样的:

### `context.js`

```
import React from "react";

const Context = React.createContext();

const reducer = (state, action) => {
  if (action.type === "TOGGLE") {
    return { ...state, isADuck: !state.isADuck };
  }
};

export class DuckifyProvider extends React.Component {
  state = {
    isADuck: false,
    dispatch: action => {
      this.setState(state => reducer(state, action));
    }
  };
  render() {
    const { state, props: { children } } = this;
    return <Context.Provider value={state}>{children}</Context.Provider>;
  }
}

export const DuckifyConsumer = Context.Consumer; 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们的状态中有`dispatch`，我们将它传递给`value`。这是由于[关于如何确定重新呈现消费者的需求的警告](https://reactjs.org/docs/context.html#caveats) ( [谢谢 Dan 指出这一点！](https://dev.to/dan_abramov/comment/2nga))。只要对`this.state`的引用仍然指向同一个对象，任何使提供者重新呈现的更新，但实际上并没有改变提供者的状态，就不会在消费者中触发重新呈现。

现在，在`DuckDeterminer`中，我们可以创建一个在`button`的`onClick`中分派的动作(`{type:"TOGGLE"}`)。

(我们还可以使用为`DuckifyContext`文件导出的 enum 对象来强制执行某些动作类型。当你检查这个[代码沙箱](https://codesandbox.io/s/kw25vnv25v)时，你会看到这个)

### `DuckDeterminer.js`

```
import React from "react";
import { DuckifyConsumer } from "./DuckContext";

class DuckDeterminer extends React.Component {
  render() {
    return (
      <DuckifyConsumer>
        {({ isADuck, dispatch }) => {
          return (
            <div>
              <div>{isADuck ? "🦆 quack" : "...silence..."}</div>
              <button onClick={e => dispatch({ type: "TOGGLE" })}>
                Change!
              </button>
            </div>
          );
        }}
      </DuckifyConsumer>
    );
  }
}

export default DuckDeterminer; 
```

Enter fullscreen mode Exit fullscreen mode

这里的秘制酱就是`dispatch`函数。因为我们可以像传递任何其他对象一样传递它，所以我们可以将它传递到我们的渲染属性函数中，并在那里调用它！在这一点上，我们的上下文存储的状态被更新，并且消费者内部的视图被更新，切换鸭子是否真的存在。

### 额外学分

你也可以在`state`和`dispatch`旁边添加一个`helpers`字段，作为一组“帮助”你筛选数据的功能。如果`state`是一个大规模数组，也许您可以编写一个`getLargest`或`getSmallest`或`getById`函数来帮助您遍历列表，而不必在您的消费组件中拆分访问列表中各种项目的实现细节。

## 结论

负责任地使用，新的上下文 API 会非常强大，并且只会随着越来越多的令人敬畏的模式被发现而增长。但是每一种新模式(甚至包括这一种)都应该小心使用，并了解权衡/好处，否则你就是在尝试死亡的*反模式*领域。

React 的新的上下文 API 非常灵活，你可以通过它传递什么。通常，您会希望将您的状态传递到`value` prop 中以供消费者使用，但是传递函数来修改状态也是可能的，并且可以使与新 API 的交互变得轻而易举。

### 试试看

现在，`DuckDeterminer`组件已经可以在 [CodeSandbox 上使用了！](https://codesandbox.io/s/kw25vnv25v)
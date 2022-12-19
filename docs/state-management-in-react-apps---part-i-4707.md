# React 应用程序中的状态管理-第一部分

> 原文：<https://dev.to/neshaz/state-management-in-react-apps---part-i-4707>

之前我写过关于如何正确使用 JSX 和 T2 的文章。然而，我仍然没有回答另一个非常重要的问题:在哪里存储您的应用程序关心的信息。今天我将讨论 React 中的应用程序状态管理。

有很多方法可以处理应用程序状态，但是首先，让我们看一看最简单的一个组件的状态。

[![ReactJS is awesome!](../Images/8a2c9d4fc8db40e7ca085f6d27fca8c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6N0jwtZA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/83cf68197a01758e1e5596641f338587/React-components.png)

## 反应组件的状态

你可能以前看过`this.state`。一个组件有它的本地状态，一个目的是保存对组件重要的信息的对象，就像你应该呈现给的[电影列表。这个本地状态首先在构造函数中初始化，就像这样:](https://kolosek.com/react-jsx-loops/) 

```
constructor() {
    super();
    this.state = {
        itemKey: defaultItemValue,
        ...
    };
} 
```

请记住，您应该为您将在组件中使用的每个状态属性定义一个默认值，即使初始值是`undefined`。

*我应该在组件状态中存储什么？*关于什么时候使用组件的状态，什么时候转向更高级的方法，有一场争论:过去许多开发人员都坚持这样一种理念，即你根本不应该使用`this.state`，但是你应该保持所有东西都处于全局可访问状态(即 redux 状态——稍后会提到)，即使它只是一个用于[条件渲染](https://kolosek.com/react-jsx-conditions/)的简单开关。

但是，如果一个值只在这一个组件中使用，那么将它保持在 redux 状态是没有意义的；如果正在开发的应用程序非常简单，就没有必要引入另一个依赖项，比如一个单独的状态管理库。一开始，当学习 React 时，最好坚持使用`this.state`，因为它不需要进入另一个库。

*React 组件的状态被异步更新。*您将使用`this.setState`功能更新您的状态。然而，有一件事要永远记住:当一个 setState 依赖于前一个 setState 时，你不应该连续使用`setState`两次，因为它的“异步”本质。

```
// assuming this.state.count === 0
this.setState({
    count: this.state.count + 1
});
this.setState({
    count: this.state.count + 1
});
// it doesn't necessarily mean that this.state.count will be 2 now 
```

然而，你可以使用所谓的*功能设置状态*来设置依赖于先前状态或依赖于某些[组件的属性](https://kolosek.com/react-props-basic/) :
的状态

```
this.setState((previousState, currentProps) => {
    return {
        count: previousState.count + 1,
    };
}); 
```

使用函数 setState 是使用 setState 的最佳方式。

如果要改变状态中保存的多个值，不需要多次调用 setState:

```
// "standard" way
this.setState({
    item1: value1,
    item2: value2,
});
// "functional" way 
this.setState((previousState, currentProps) => {
    return {
        item1: value1,
        item2: value2,
    };
}); 
```

*状态不应该突变。*这既适用于简单组件的状态，也适用于一些更高级的组件，如第三方 redux 状态。组件的状态只能通过使用`setState`功能来改变。虽然您可以只使用`this.state.disabled = true`，但它不会触发组件的重新呈现。没有防止状态下保存的变异物品，所以你要关心自己不要变异它们。

到目前为止，我已经介绍了 React 组件的状态及其三个基本概念:它是**不可变的**、**异步的**和**本地的**。这是在简单的应用程序中处理存储信息的一种快捷方式，对于应该从组件实例中的多个位置访问的局部变量也是如此，甚至在非常大和复杂的应用程序中也是如此。

然而，对于更复杂的应用程序，对于过于依赖彼此状态的[组件，以及为了更好地处理应用程序使用的数据，有第三方库，如 Redux、Flux、CerebralJS 和许多其他库，它们提供了更高级的处理应用程序状态的方法。这些我下次再讲。](https://kolosek.com/react-props-communication/)

如果您想在 React 中开发一个应用程序，但不确定如何去做，请不要犹豫，联系 Kolosek 团队并[与我们分享您的想法](https://kolosek.com/startproject/)。

本文原载于 [Kolosek 博客](https://kolosek.com/react-state/?utm_source=dvt)。
# 流中 Redux 模块的泛型类型

> 原文：<https://dev.to/egeriis/generic-typing-of-redux-modules-in-flow-384m>

虽然官方的流文档提供了一个对 Redux 模块进行类型化的解决方案，但是它们的例子的设计方式意味着将类型定义复制/粘贴到每个 Redux 模块中。这不是最佳的。

让我们来看看[他们的例子](https://flow.org/en/docs/react/redux/#toc-typing-redux-thunk-actions) :

```
type Action =
  | { type: "FOO", foo: number }
  | { type: "BAR", bar: boolean };
type ThunkAction = (dispatch: Dispatch, getState: GetState) => any;
type PromiseAction = Promise<Action>;
type GetState = () => State;
type Dispatch = (action: Action | ThunkAction | PromiseAction) => any; 
```

Enter fullscreen mode Exit fullscreen mode

查看上面代码片段中的前三行，我们看到他们的示例指定了一组静态的动作类型。这暴露了应用程序中的一个问题，即状态分散到多个模块中，导致重复代码或其他代码味道。

在这样的应用程序中使用上面片段中的方法，我们必须:

1.  指定顶级类型，并为他们提供对我们应用程序的所有动作类型的访问
2.  为每个 Redux 模块复制/粘贴这些类型定义——或者，更确切地说，是您拆分操作类型的级别

这两种解决方案都会导致代码味道。

# 使用泛型使类型通用

那么我们如何以一种通用的方式创建这些类型呢？使用[泛型](https://flow.org/en/docs/types/generics/)！

记住，原始代码中的主要问题是类型`Action`是静态的。本质上，我们需要做的就是使这个值可变。

下面是我的解决方案:

```
export type ThunkAction<S, A> = (
  dispatch: Dispatch<S, A>,
  getState: GetState<S>
) => any;
export type PromiseAction<A> = Promise<A>;

export type GetState<S> = () => S;
export type Dispatch<S, A> = (
  action: A | ThunkAction<S, A> | PromiseAction<A>
) => any; 
```

Enter fullscreen mode Exit fullscreen mode

> 哇，这么多泛型！

是啊，我知道。但是它真的没有那么复杂:

为了遵循 Flow 自己的实践，也为了简洁起见，泛型用一个字母来命名。`A`代表“动作”`S`代表“状态”。这是我们必须使之可变的两种类型，因为它们对于每个 Redux 模块都是不同的。

使用泛型，我们可以要求在使用类型的地方传递“参数”。参考我的解决方案，`ThunkAction`需要传递两个“参数”,状态和动作，所以定义一个 thunk 动作应该是这样的:

```
type FetchFooActions = ActionFoo | ActionBar;
function fetchFoo(): ThunkAction<State, FetchFooActions> {
  return (dispatch, getState) => { /* inside my mind I have a digital mind */ };
} 
```

Enter fullscreen mode Exit fullscreen mode

`State`是我们的 Redux 模块的状态的类型定义，`FetchFooActions`是期望从调用`fetchFoo`发出的动作的清晰规范。

如果您需要使用`PromiseAction`、`GetState`或`Dispatch`，只需提供它们的泛型“参数”:

```
// Supply your State and Action types to Dispatch, State only to GetState
const cb = (dispatch: Dispatch<State, Action>, getState: GetState<State>) => { ... };
// Supply Action type to PromiseAction
function asyncAction(): PromiseAction<Action> { ... } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经理清了四种类型，`ThunkAction`、`PromiseAction`、`GetState`和`Dispatch`，因此它们可以在整个应用程序中共享而没有代码味道。但是我们甚至同时使我们自己在打字时更加具体。在我的完整示例中，您可以通过尝试调度一个意外的操作来看到这一点:

[flow.org/try 的完整例子](https://flow.org/try/#0MYewdgzgLgBAZgUysAFjAvDAFAOjwQwCcBzCASgwD4YAFQkAWwEsIEdCEIQAbANwSwBtAORQmUbgmEBdMgG4AUAoD0ymAHEEYBISbAFCAB4AHEIVhQAnsYQwAKigCuYANYBBYGPAAeAMoAaGDdqTCwFGBgAExZjfGQUAC4YABEYuNQ-QOD-cJhiJF8oOIQkzShC4r9KBQp0anwwS0UjU3MYKxtaemZWDy8wb2CMLsYWBEHKRQMTMwtrWzKKqHHfEOxa6l9mmbaO21SIWPjMoLWwiPxPJnAktxgAH3snVz7rgYDTh5GehFefYJqVBgDSaSlUMAASghIo5gDoFHtaCBoBBhm5CIR8JZvNBdGBiJMEfMYEtbJgAN65RysQgASUiSTAjgYACMdDkIqYUUkaMioBAFABfKbgv5gdrzAWIgCqxkixV5KLFw0pET2SWEjjlxQAtFz+cIOTB9RAeXyBcKlIjFfzZfLlgAxfBMSSRZUU3LqmCa7XLPXmnVwZ2u4RCkVqMWomBEzoOpCoG0QSPDO0K83Kx6J1OO4PQsWKODOK7geDxlCJrBkJIOZzuYvvIrLQJx+KJyPUVUwDhQRyEcVYaKHdIoQL5cqNhAbGCdiKgSCwE3DRDxLDCZQm5TCGAAajyBQnlZw1J09LIOCgKC0WEXdWnuQiEUHR1QWHJ0YfH4lNg1WvtCH9KKGven4msBESCvIwGCoElZAjOD5PsOr5fiU3q-rqJqBrmkRbhBigfnhuSWoKQA)

希望这是有价值的启发。欢迎评论或提问:)
# 语用类型:作为有限状态机的 Redux

> 原文：<https://dev.to/stereobooster/pragmatic-types-how-to-turn-redux-to-finite-state-machine-with-the-help-of-types-5f08>

这篇文章假设了一些关于 Redux 和类型的知识，但是可以随意提问。

这是系列文章的第三篇。这个帖子的代码是[这里是](https://github.com/stereobooster/react-fsm-example/tree/post-1)

1.  [还原为有限状态机](https://dev.to/stereobooster/pragmatic-types-how-to-turn-redux-to-finite-state-machine-with-the-help-of-types-5f08)
2.  [Redux 中的副作用](https://dev.to/stereobooster/turn-redux-into-finite-state-machine-vol-2-side-effects-6j0)
3.  [乐观 UI](https://dev.to/stereobooster/optimistic-ui-5f1f)
4.  [我创造了一个怪物](https://dev.to/stereobooster/i-created-a-monster-237o)

## 我们想做什么

我们希望建立一个用户可以输入数据的表单，当用户提交表单时，我们需要显示 AJAX 请求运行时的加载状态，当 AJAX 请求完成时，显示成功的结果或失败的错误。

让我们为这个任务创建一个“经典”的缩减器和“有限状态机”的缩减器，这样我们就可以进行比较。完整的代码在这个库中[。](https://github.com/stereobooster/react-fsm-example/tree/post-1)

## 【经典】减速器

这就是“经典”减速器的样子:

```
export default (reduxState: State = defaultState, action: Actions): State => {
  switch (action.type) {
    case "SUBMIT_FRUIT":
      return {
        ...reduxState,
        state: "fruit_loading",
        form: action.form
      };
    case "SUBMIT_FRUIT_ERROR":
      return {
        ...reduxState,
        state: "fruit_error",
        error: action.error
      };
    case "SUBMIT_FRUIT_OK":
      return {
        ...reduxState,
        state: "fruit_ok",
        resonse: action.resonse
      };
    default:
      exhaustiveCheck(action.type);
      return reduxState;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

`SUBMIT_FRUIT`是响应表单提交而调度的操作。
`SUBMIT_FRUIT_ERROR`和`SUBMIT_FRUIT_OK`被调度以响应副作用，例如 AJAX 请求。我们可以针对副作用使用不同的解决方案，如 redux-thunk、redux-saga、redux-observable 或 redux-loop。让我们不要关注这一点，相反，我们将通过调度明确地触发副作用。

下面是 AJAX 请求的样子:

```
export const fruitSubmitSideEffect = (dispatch: Dispatch, form: FruitForm) => {
  // uses fetch inside returns a Promise
  fruitRequest(form).then(
    resonse => {
      dispatch({
        type: "SUBMIT_FRUIT_OK",
        resonse
      });
    },
    error => {
      dispatch({
        type: "SUBMIT_FRUIT_ERROR",
        error
      });
    }
  );
};

// and later

export default connect(
  () => ({}),
  (dispatch: Dispatch) => ({
    submit: (form: FruitForm) => {
      dispatch({ type: "SUBMIT_FRUIT", form });
      fruitSubmitSideEffect(dispatch, form);
    }
  })
)(Component); 
```

Enter fullscreen mode Exit fullscreen mode

用于创建新状态的动作中的先前状态，但未明确检查:

```
return {
  ...reduxState,
  ...newPartsOfState
}; 
```

Enter fullscreen mode Exit fullscreen mode

`State`的类型可以是这样的:

```
export type State = {
  state: "initial" | "fruit_loading" | "fruit_error" | "fruit_ok";
  form?: FruitForm;
  error?: mixed;
  resonse?: FruitResponse;
}; 
```

Enter fullscreen mode Exit fullscreen mode

结果之一是我们将需要编写额外的类型检查:

```
export default ({ state }: { state: State }) => {
  switch (state.state) {
    case "fruit_ok":
      return (
        state.resonse && // additional type check, that it is not undefined
        state.resonse.map(item => {}))
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 有限状态机

有限状态机(FSM)应该具有有限状态。让我们用类型系统强制它。这是流类型，但是 TypeScript 看起来类似(在 TS 中的`{||}`中没有必要)。

```
export type State =
  | {|
      state: "initial"
    |}
  | {|
      state: "fruit_loading",
      form: FruitForm
    |}
  | {|
      state: "fruit_error",
      form: FruitForm,
      error: mixed
    |}
  | {|
      state: "fruit_ok",
      form: FruitForm,
      resonse: FruitResponse
    |}; 
```

Enter fullscreen mode Exit fullscreen mode

现在不检查就不能用之前的状态。如果我们做

```
return {
  ...reduxState,
  state: "fruit_loading",
  form: action.form
}; 
```

Enter fullscreen mode Exit fullscreen mode

流会抱怨:

```
Could not decide which case to select. Since case 2 [1] may work but if it doesn't case 3 [2] looks promising too. To fix add a type annotation to .form [3] or to .state [3].

     src/redux-fsm/state.js
 [1] 12│   | {|
     13│       state: "fruit_loading",
     14│       form: FruitForm
     15│     |}
 [2] 16│   | {|
     17│       state: "fruit_error",
     18│       form: FruitForm,
     19│       error: mixed
     20│     |} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们需要做这样的事情:

```
switch (action.type) {
  case "SUBMIT_FRUIT":
    switch (reduxState.state) {
      case "initial":
        return {
          state: "fruit_loading",
          form: action.form
        };
      default:
        throw new Error("Inavlid transition");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们检查将要发生什么动作，先前的状态是什么，然后我们决定做什么。这种方法迫使我们明确地考虑系统中的所有转换。

```
initial
  SUBMIT_FRUIT       -> fruit_loading (1)
  SUBMIT_FRUIT_ERROR -> ?             (2)
  SUBMIT_FRUIT_OK    -> ?             (2)
fruit_loading
  SUBMIT_FRUIT       -> fruit_loading (3)
  SUBMIT_FRUIT_ERROR -> fruit_error   (4)
  SUBMIT_FRUIT_OK    -> fruit_ok      (5)
fruit_error
  SUBMIT_FRUIT       -> fruit_loading (6)
  SUBMIT_FRUIT_ERROR -> ?             (7)
  SUBMIT_FRUIT_OK    -> ?             (7)
fruit_ok
  SUBMIT_FRUIT       -> fruit_loading (6)
  SUBMIT_FRUIT_ERROR -> ?             (7)
  SUBMIT_FRUIT_OK    -> ?             (7) 
```

Enter fullscreen mode Exit fullscreen mode

> 旁注:**你为什么想这么做？**正式指定 UI，证明 UI 逻辑没有错误。例如:
> 
> *   你可以用 [sketch.systems](https://sketch.systems/) 来原型化 UI 逻辑
> *   [使用 Alloy(比 TLA+更轻的替代品)来分析你的用户界面](https://www.hillelwayne.com/post/formally-specifying-uis/)
> *   这个规范可以在 UX 人和开发者之间共享
> *   同样，参见[验证原因反应组件逻辑-原因 ML & Imandra](https://medium.com/imandra/verifying-reasonreact-component-logic-reasonml-imandra-e350d4812a9f)
> 
> 旁注 2:我在 reducer 中实现了“反向”FSM，它首先检查动作，然后检查状态

(1，5)“快乐”路径——用户提交表单并得到响应。
(1，4)错误路径——用户提交表单并得到一个错误。
(6)重复搜索——已经有错误或响应成功，用户重复搜索。
(2)永远不会发生——我们可以假设它永远不会发生，并在这种情况下抛出一个异常。
(7)竞争条件——我们已经有了一个响应(或错误),而一个新的响应(或错误)到来了，只有当我们同时允许一个以上的副作用时，这种情况才会发生。
(3)重复搜索——有一个搜索等待处理，用户要求不同的或者不耐烦的点击。那是一个有趣的案例。我们该怎么办？我们可以:

*   忽略它(通过禁用按钮直观地传达它也是有意义的)
*   取消以前的请求并启动新的请求
*   启动一个新的，忘记以前的。这基本上是我们在“经典”方法中所做的，但是这也将导致一种情形(7 ),这是一种竞争条件。此外，这种方法在(1，5)和(1，4)场景中引入了竞争条件。

对于这篇文章，我选择忽略它，作为最简单的解决方案，也许我会在下一篇文章中实现取消。

这就是为什么你要使用有限状态机，这种方法有助于找到逻辑中的“漏洞”。系统中的状态越多，隐藏的潜在漏洞就越多。

如果你认为找到这些类型的 bug 太麻烦了，想想典型的 IT 支持问题:“你有没有试着把它关掉再打开？”。是的，在某个地方隐藏着与状态相关的错误，解决方法是重启系统，将状态重置为初始状态。

[![Guy from IT crowd saying "Have you tried to turning it off and on again"](img/024a6ebfe76d5fcae45ab3d226f9560c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KPGotogP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gigf5nztyx9v70l4rvxo.gif)

另一方面，我同意 JS(或 Flow 或 TS)语法对于这种任务来说有点笨拙。与开关匹配的模式没有表现力。Redux 比传统需要更多的样板文件。**说说你的看法**。如果它需要更少的样板文件，你会使用它吗？

> Dan Lohmar 在 Unsplash 上拍摄的照片

* * *

[这篇文章是](https://dev.to/t/pragmatictypes)系列文章的一部分。在[推特](https://twitter.com/stereobooster)和 [github](https://github.com/stereobooster) 上关注我。
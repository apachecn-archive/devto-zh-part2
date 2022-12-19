# Redux 中的副作用

> 原文：<https://dev.to/stereobooster/turn-redux-into-finite-state-machine-vol-2-side-effects-6j0>

这是系列文章的第三篇。这个帖子的代码是[这里是](https://github.com/stereobooster/react-fsm-example/tree/post-2)

1.  [还原为有限状态机](https://dev.to/stereobooster/pragmatic-types-how-to-turn-redux-to-finite-state-machine-with-the-help-of-types-5f08)
2.  [Redux 中的副作用](https://dev.to/stereobooster/turn-redux-into-finite-state-machine-vol-2-side-effects-6j0)
3.  [乐观 UI](https://dev.to/stereobooster/optimistic-ui-5f1f)
4.  [我创造了一个怪物](https://dev.to/stereobooster/i-created-a-monster-237o)

## 有限状态机及其超越

在前一篇文章中，我谈到了有限状态机作为减少错误数量的一种方法，但是它的实现方式仍然为一些错误留下了空间。

可以用更精确的类型来表示 FSM。我们可以描述允许转换的对(也称为元组)，例如`type transitions = ['initial', 'SUBMIT_FRUIT'] | ['loading', 'SUBMIT_FRUIT_OK'] ...`，并使用它来强制进行正确的转换(不确定这对于 Redux 是否可行，但一般来说应该是可行的)

我用 Harel statecharts 符号描述了有限状态机(或与之非常接近的符号)，但实际上，我还没有证明它的正确性(用 Alloy 或 TLA+等)。).它可以包含无限循环、不可达状态、竞争条件和死锁——给出的例子很小，所以可能没问题，但是对于一个更大的系统，很容易遗漏一些东西

JS Redux 实现类似于 FSM，例如，副作用是与调度操作分开触发的，如果开发人员忘记触发它，用户将陷入无限加载状态(默认情况下 fetch 没有超时，因此如果开发人员忘记用`Promise.race`添加超时，用户也会陷入)

因此，我将把(第一篇文章中)描述的技术更多地视为一种分析技术，它有助于思考系统状态和转换，并且通过更彻底的分析有助于防止一些错误。为了让它对 bug 更加健壮，还需要做一些工作。

改善这一点的一个方法是使给定的实现更接近所描述的 FSM，让我们确保分派的动作总是伴随着适当的副作用。

## 副作用为消息

Redux 中有不同的副作用处理方法，如 redux-thunk、redux-saga、redux-observable。我想，这里的问题是没有“官方”的解决方案，这就是为什么不同的方法不断出现。参见:

*   [在 JavaScrip 中有效果的 Reducer 组合](https://github.com/reduxjs/redux/issues/1528)
*   如何表示 AJAX 调用等“副作用”？

我想向你展示一个非常透明的副作用处理方法(从我的角度来看)。我们可以创建响应动作的副作用，并保持 reducer 的纯净，而不是执行它，我们可以“序列化”它，并作为消息传递给 Redux 中间件，它将为我们实际执行它。这类似于他们在榆树做的:

```
// new type signature of the reducer
const reducer = (State, Actions) => [State, SideEffect];

// and somewhere in the Redux middleware
const [newState, sideEffect] = reducer(state, action);
sideEffect.execute();
return newState; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用现有的解决方案- [redux-loop](https://redux-loop.js.org/) 。它已经安装在我的项目中，你可以检查源代码，看看它是如何做到的。

下面是减速器“内部副作用”的样子:

```
-export const fruitSubmitSideEffect = (dispatch: Dispatch, form: FruitForm) => {
-  fruitRequest(form).then(
-    resonse => {
-      dispatch({
-        type: "SUBMIT_FRUIT_OK",
-        resonse
-      });
-    },
-    error => {
-      dispatch({
-        type: "SUBMIT_FRUIT_ERROR",
-        error
-      });
-    }
-  );
-}; ...
     case "SUBMIT_FRUIT":
       switch (reduxState.state) {
         case "initial":
         case "fruit_error":
         case "fruit_ok":
-          return {
-            state: "fruit_loading",
-            form: action.form
-          }; +          return loop(
+            {
+              state: "fruit_loading",
+              form: action.form
+            },
+            Cmd.run(fruitRequest, {
+              successActionCreator: resonse => ({
+                type: "SUBMIT_FRUIT_OK",
+                resonse
+              }),
+              failActionCreator: error => ({
+                type: "SUBMIT_FRUIT_ERROR",
+                error
+              }),
+              args: [action.form]
+            })
+          ); 
```

Enter fullscreen mode Exit fullscreen mode

## 写测试！

正如我所说的，为 FSM 提供的技术不足以证明正确性(它将帮助您发现一些错误，但不是全部)。所以添加测试是个好主意(直到我们添加更多的形式主义)。

好的方面是所有的逻辑都封装在 reducer 中，测试它根本不需要考虑副作用。我们仍然需要单独测试副作用，但是我们不需要将“核心”逻辑测试与副作用结合起来。

```
it("changes state to loading and creates side effect", () => {
  const [state, effect] = reducer(undefined, {
    type: "SUBMIT_FRUIT",
    form: "form"
  });
  expect(state).toEqual({ form: "form", state: "fruit_loading" });
  expect(effect.simulate({ success: true, result: "response" })).toEqual({
    resonse: "response",
    type: "SUBMIT_FRUIT_OK"
  });
  expect(effect.simulate({ success: false, result: "error" })).toEqual({
    error: "error",
    type: "SUBMIT_FRUIT_ERROR"
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

为了进行测试，我们不需要模仿任何东西——不需要 fetch，也不需要模块。

我们可以额外检查实际的副作用会发生什么(我们需要这样做一次)，我们可以单独测试副作用本身，例如`src/api/fruitRequest` :

```
jest.mock("src/api/fruitRequest", () => ({
  fruitRequest: jest.fn(() => "mockedFruitRequest")
}));
it("creates side effect with fruitRequest", () => {
  const { fruitRequest } = require("src/api/fruitRequest");
  const [state, effect] = reducer(undefined, {
    type: "SUBMIT_FRUIT",
    form: { test: 123 }
  });
  expect(effect.func(...effect.args)).toEqual("mockedFruitRequest");
  expect(fruitRequest).toBeCalledWith({ test: 123 });
}); 
```

Enter fullscreen mode Exit fullscreen mode

是不是很整洁？

> 照片由 Anton Darius | @ the sollers on Unsplash 拍摄

* * *

在 [twitter](https://twitter.com/stereobooster) 和 [github](https://github.com/stereobooster) 关注我。
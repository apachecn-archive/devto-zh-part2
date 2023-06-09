# React 钩子:如何创建和更新上下文？供应者

> 原文：<https://dev.to/oieduardorabelo/react-hooks-how-to-create-and-update-contextprovider-1f68>

如果这是你第一次听说“React Hooks ”,你可以[观看 React Conf 介绍演讲](https://www.youtube.com/watch?v=dpw9EHDh2bM)。值得！

我不会花太多时间解释新的 API，为此，[你可以去他们的文档](https://reactjs.org/docs/hooks-intro.html)。React 团队做出了惊人的工作，解释了所有的原因以及他们是如何做到这一点的。

# 入门

有了实际操作的例子，一切都会变得更好，让我们从以下内容开始:

```
$ mkdir react-hooks-contex-provider
$ cd react-hooks-contex-provider
$ yarn init -y
$ yarn add react@^16.7.0-alpha.0 react-dom@^16.7.0-alpha.0
$ yarn add parcel-bundler 
```

有了这个样板，我们已经:

*   在 alpha 版本中使用所有可用的钩子`use*`进行反应
*   package Bundler 运行我们的本地示例

让我们添加我们的 HTML 文件:

```
$ touch index.html 
```

添加一些 HTML:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  React Parcel
</head>
<body>
  <div id="root"></div>
  <script src="./src/index.js"></script>
</body>
</html> 
```

正如我们在 HTML 中看到的，我们有一个`./src/index.js`文件，让我们创建它:

```
$ mkdir src
$ touch src/index.js 
```

添加一些 JavaScript:

```
import * as React from "react";
import * as ReactDOM from "react-dom";

import { ContextOneProvider } from "./ContextOne";
import { App } from "./App";

function main(target, container) {
  ReactDOM.render(target, container);
}

main(
  <ContextOneProvider>
    <App />
  </ContextOneProvider>,
  document.getElementById("root")
); 
```

这里没什么不同。我们熟悉的`ReactDOM.render`呈现了一个名为`App`的组件，它被包装在一个名为`ContextOneProvider`的上下文中。

# 创建我们的上下文文件

来自我们的`./src/index.js`的跟进可以是我们的`./src/ContextOne.js`，让我们创建它:

```
$ touch src/ContextOne.js 
```

并使用下面的代码片段:

```
import * as React from "react";

let ContextOne = React.createContext();

let initialState = {
  count: 10,
  currentColor: "#bada55"
};

let reducer = (state, action) => {
  switch (action.type) {
    case "reset":
      return initialState;
    case "increment":
      return { ...state, count: state.count + 1 };
    case "decrement":
      return { ...state, count: state.count - 1 };
    case "set-color":
      return { ...state, currentColor: action.payload };
  }
};

function ContextOneProvider(props) {
  // [A]
  let [state, dispatch] = React.useReducer(reducer, initialState);
  let value = { state, dispatch };

  // [B]
  return (
    <ContextOne.Provider value={value}>{props.children}</ContextOne.Provider>
  );
}

let ContextOneConsumer = ContextOne.Consumer;

// [C]
export { ContextOne, ContextOneProvider, ContextOneConsumer }; 
```

我们这里有一些新面孔，嗯？90%的代码都挺熟悉的，我们来考察一下**【A】****【B】****【C】**项。

*   **【A】**:我们在这里使用了新的 [React Hooks API，名为`useReducer`](https://reactjs.org/docs/hooks-reference.html#usereducer) 。如果你熟悉 Redux，你已经知道这是如何工作的。它将返回`state`对象和一个`dispatch`函数来发送对存储状态的更新。我们正在用这两者创建一个`value`对象，并将它发送给我们的 item**【B】**。
*   **【B】**:在这里，我们使用我们的上下文提供者注入`value`对象，使其对所有消费者可用。之前我们看到，我们使用它将我们的`<App />`包装在`./src/index.js`中，这意味着来自`<App />`的所有子组件将能够拉出这个上下文来使用。
*   **【C】**:乍一看，这个出口很奇怪。我们正在导出由 React、`ContextOne`、我们的定制提供者、`ContextOneProvider`创建的默认上下文对象，以及消费者键的别名、`ContextOneConsumer`。要为上下文使用新的 [Reactk Hooks API，称为 useContext](https://reactjs.org/docs/hooks-reference.html#usecontext) ，我们需要传递 React 创建的默认对象，这是我们的第一个导出。第二个导出，`ContextOneProvider`，是我们的自定义提供者，我们需要使用它在我们的应用程序上下文中注入我们想要的东西。最后一个导出，`ContextOneConsumer`，只是为了方便订阅上下文变化，[这是 React](https://reactjs.org/docs/context.html#contextconsumer) 的一个稳定特性。

# 创建我们的应用文件

最后但同样重要的是，让我们关注一下我们的`./src/App.js`文件:

```
$ touch src/App.js 
```

粘贴一些 JavaScript 代码:

```
import * as React from "react";

import { ContextOne } from "./ContextOne";

export function App() {
  // [A]
  let { state, dispatch } = React.useContext(ContextOne);

  // [B]
  React.useEffect(
    () => {
      document.body.style.backgroundColor = state.currentColor;
    },
    [state.currentColor]
  );

  // [C]
  let inc = () => dispatch({ type: "increment" });
  let dec = () => dispatch({ type: "decrement" });
  let reset = () => dispatch({ type: "reset" });
  let setColor = color => () => dispatch({ type: "set-color", payload: color });

  return (
    <React.Fragment>
      <div style={{ textAlign: "center" }}>
        <p>
          Current color is: <b>{state.currentColor}</b>
        </p>
        <p>
          Current count: <b>{state.count}</b>
        </p>
      </div>
      <div style={{ paddingTop: 40 }}>
        <p>Count controls:</p>
        <button onClick={inc}>Increment!</button>
        <button onClick={dec}>Decrement!</button>
      </div>
      <div>
        <p>Color controls:</p>
        <button onClick={setColor("green")}>Change to green!</button>
        <button onClick={setColor("papayawhip")}>Change to papayawhip!</button>
      </div>
      <div>
        <p>Reset changes:</p>
        <button onClick={reset}>Reset!</button>
      </div>
    </React.Fragment>
  );
} 
```

Woww，现在呢？同样，90%的代码是熟悉的，让我们检查另外 10%的代码:

*   **【A】**:我们正在使用[名为 useContext](https://reactjs.org/docs/hooks-reference.html#usecontext) 的新 React Hooks API 来消费我们的上下文(注意，我们正在导入 React 创建的`ContextOne`对象)。当提供者更新时，这个钩子将触发一个带有最新上下文值的重新呈现器。
*   **【B】**:另一个新的 [React Hooks API 叫做 useEffect](https://reactjs.org/docs/hooks-reference.html#useeffect) 。将效果视为从 React 的纯功能世界进入命令世界的出口。任何变异，订阅，定时器和其他副作用你都可以用这个钩子来做。作为第一个参数，我们传递一个函数，改变物体的背景颜色。作为第二个参数，我们传递一个数组，这个数组告诉 react“嘿，当这些属性/值改变时运行我的效果”。
*   **【C】**:普通的 JavaScript 箭头函数，但是，值得注意的是，我们使用上下文中的`dispatch`函数来更新我们的提供者。

# 运行我们的例子

现在，我们已经达成了真正的交易，让我们运行我们的示例:

```
$ yarn parcel index.html 
```

您应该会看到类似这样的内容:

[![](img/d322ecb6611d3e82306c3406395e7eeb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2qoN1lnX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3vs2egyliuthdj4b4xno.png)

打开你的`localhost:1234`:

[![](img/26a821ae7acf5eedf2e4780656c1e26c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iUAZE_eS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dwhzpcexgwll0uxqtziy.png)

# 结论

[React Hooks API](https://reactjs.org/docs/hooks-reference.html) 极其强大。Twitter 上的[社区](https://twitter.com/search?f=tweets&q=%23react%20%23hooks&src=typd)开启了🔥。我们已经在 GitHub 上有了[的好例子。](https://github.com/topics/react-hooks)

你怎么想呢?你上钩了吗？:P
# 第二部分:在 Redux 中使用 Typescript

> 原文：<https://dev.to/ganderzz/part-two-using-typescript-with-redux-3cil>

[![Click Here](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/5w70j1916l)

# 第二部分:使用带有 ~~React~~ Redux 的打字稿

上一次我们通过 React 使用了 Typescript，并设置了一个小的计数器应用程序。很多应用程序都使用 Redux。对我来说，一个常见的问题是如何用 Redux 设置 Typescript 这也是我的团队面临的一个常见障碍。尽管 Redux 是一个完全不同的库，但它在 React 应用程序的状态管理市场中占有很大份额。在第二部分中，我们将绕过基础 React，看看如何使用 Redux 来转换我们的计数器！

注意:和第一部分一样，我将假设 Typescript，现在 Redux，是 setup 和 compiles。这是一篇关于集成类型的文章，而不是关于如何设置项目的文章。

### 动作

我们将开始为我们的计数器创建一些动作。

```
// Action Types
export const INCREMENT = "INCREMENT";
export const DECREMENT = "DECREMENT";

export type ActionTypes = typeof INCREMENT | typeof DECREMENT;

export interface IAction {
  type: ActionTypes;
  payload: IPayload;
}

export interface IPayload {
  amount: number;
}

// Action Creators
export function increment(amount: number): IAction {
  return {
    type: INCREMENT,
    payload: {
      amount
    }
  };
}

export function decrement(amount: number): IAction {
  return {
    type: DECREMENT,
    payload: {
      amount
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

从上到下。我们开始创建一些动作类型:*递增*和*递减*。这将允许我们重用这些字符串作为类型，并摆脱任何“神奇的字符串”。创建动作类型改进了我们的代码，所以如果我们的类型改变了，我们只有一个地方来更新这个值。此外，减少了由于人为错误而出现的错误(例如，我们不小心将“increment”或“INCREMENT”替换为“inCREMENT”)。

继续，我们遇到了一个类型脚本类型和一些接口。在 *ActionTypes* 中，我们使用联合类型来表示，“我们可以使用这些类型中的任何一种”。这个以后会派上用场的。接下来，我们为动作创建者定义一个通用接口。我们提供了重用 *ActionTypes* 所需的 Redux *type* 属性。为了保持不同的动作相似，我们将使用 payload 来传递一个我们希望我们的 reducer 处理的“值”。对于计数器，我们将使用 **IPayload** ，它有一个属性 *amount* ，这是一个数字。我们将使用它来设置数量，我们将增加或减少计数。

最后，我们将为计数器的递增和递减创建一个动作创建器。这些函数非常简单。它们都是接受一个数字、*数量*并返回一个*动作*的函数。

### 减速器

```
export const defaultState = {
  count: 0
};

export function rootReducer(state = defaultState, action: IAction) {
  switch (action.type) {
    case INCREMENT:
      return {
        count: state.count + action.payload.amount
      };

    case DECREMENT:
      return {
        count: state.count - action.payload.amount
      };

    default:
      return state;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我不会在 reducer 上花太多时间，因为 Typescript 在这里(在这个例子中)没有太大的作用。我们将导出我们的 *defaultState* 供以后使用！

### 组件

```
interface IStateToProps {
  count: number;
}

interface IDispatchToProps {
  increment: typeof increment;
  decrement: typeof decrement;
}

type AllProps = IStateToProps & IDispatchToProps;

class App extends React.Component<AllProps> {
  private handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    const type: string = event.currentTarget.title;

    if (type === "decrement") {
      this.props.decrement(1);
    } else {
      this.props.increment(2);
    }
  };

  public render() {
    return (
      <div>
        <button title="decrement" onClick={this.handleClick}>
          -
        </button>
        {this.props.count}
        <button title="increment" onClick={this.handleClick}>
          +
        </button>
      </div>
    );
  }
}

export const ConnectedApp = connect<IStateToProps, IDispatchToProps>(
  (state: typeof defaultState) => ({
    count: state.count
  }),
  {
    increment,
    decrement
  }
)(App); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用了一个库*react-redux*——使得 React 组件与 Redux 的连接更加容易。首先，我们创建了两个接口，我们将把它们用作**应用**的道具和代码底部的`connect()`函数。

我们告诉 Typescript 映射的状态将被命名为什么。在我们的案例中，我们将保留名称*计数*。我们定义我们的组件将得到什么动作。使用`typeof`我们可以很容易地告诉 Typescript 重用隐含的类型定义*增量*和*减量*(我们的动作)。

接下来，我们创建一个结合了 *IStateToProps* 和 *IDispatchToProps* 的新类型。这是为了方便起见，我们在 **App** 的正确定义中可以看到。我们也可以使用`class App extends React.Component<IStateToProps & IDispatchToProps>`而不需要*所有道具*。当使用多个生命周期挂钩(`shouldComponentUpdate(nextProps: AllProps)`)或者多个使用道具的函数时，将所有道具组合成一个类型变得很有价值！

**应用程序**组件与我们第一部分的例子非常相似，所以我们将跳过这一步，看看`connect()`函数。

`connect()`是一个泛型类型，可以接受三个类型`connect<IStateToProps, IDispatchToProps, IProps>()`。从左到右-首先是 stateToProps 的类型定义。这是我们将 Redux **状态**映射到的连接组件的道具。第二，dispatchToProps 的类型定义。这是我们映射 Redux **动作**的连接组件的道具。最后，我们在这个例子中没有使用它，但是我们可以提供我们正在连接的组件的道具。

我们已经成功地将计数器转换为 Redux，并且还提供了有用的类型！
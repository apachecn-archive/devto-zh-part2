# Redux 与 React 上下文 API

> 原文：<https://dev.to/dceddia/redux-vs-the-react-context-api-1nof>

[![React Context vs Redux: which to use, and why](../Images/cde8420d6431d9bf9ea9b85767319529.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0eyYJNs3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daveceddia.com/images/context-vs-redux.png)

React 16.3 增加了一个新的上下文 API——*new*在某种意义上说，旧的*上下文 API 是一个幕后特性，大多数人要么不知道，要么因为文档说要避免使用它而避免使用它。*

不过现在，上下文 API 是 React 中的一等公民，对所有人开放(不是说以前不是，但现在是正式的)。

React 16.3 一发布，网上就有文章宣称 Redux 的死亡是因为这个新的上下文 API。不过，如果你问 Redux，我想它会说“关于我死亡的报道被大大夸大了”

在这篇文章中，我想介绍新的 Context API 是如何工作的，它与 Redux 有什么相似之处，什么时候你可能想使用 Context *而不是* Redux，以及为什么 Context 不能在所有情况下取代 Redux。

## 一个激励人心的例子

我假设你已经掌握了 React down pat(道具和状态)的基本知识，但是如果你没有，我有一个免费的 5 天课程来帮助你[在这里](https://daveceddia.com/pure-react-email-course/)学习 React。

让我们看一个会导致大多数人使用 Redux 的例子。我们将从一个普通的 React 版本开始，然后看看它在 Redux 中是什么样子，最后看看上下文。

[![The component hierarchy](../Images/a66a2aaf92eda63fb468c6e37665cd44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---UaYF80B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daveceddia.com/images/context-v-redux-app-screenshot.png)

这个应用程序将用户信息显示在两个地方:右上角的导航栏和主要内容旁边的侧边栏。

组件结构如下所示:

[![The component hierarchy](../Images/9de25faa98ccf764bb757e6812edb814.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xt-Z8M9z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daveceddia.com/images/context-v-redux-app-tree.png)

使用 pure React(只是普通的道具)，我们需要在树中存储足够高的用户信息，以便可以传递给需要它的组件。在这种情况下，用户信息的保管人必须是`App`。

然后，为了将用户信息传递给需要它的组件，App 需要将它传递给 Nav 和 Body。反过来，他们需要将它向下传递*再传递*，传递给 UserAvatar(万岁！)和侧边栏。最后，Sidebar 必须将它传递给 UserStats。

让我们看看这在代码中是如何工作的(我将所有内容放在一个文件中，以便于阅读，但实际上这些内容可能会被拆分到不同的文件中)。

```
import React from "react";
import ReactDOM from "react-dom";
import "./styles.css";

const UserAvatar = ({ user, size }) => (
  <img
    className={`user-avatar ${size || ""}`}
    alt="user avatar"
    src={user.avatar}
  />
);

const UserStats = ({ user }) => (
  <div className="user-stats">
    <div>
      <UserAvatar user={user} />
      {user.name}
    </div>
    <div className="stats">
      <div>{user.followers} Followers</div>
      <div>Following {user.following}</div>
    </div>
  </div>
);

const Nav = ({ user }) => (
  <div className="nav">
    <UserAvatar user={user} size="small" />
  </div>
);

const Content = () => <div className="content">main content here</div>;

const Sidebar = ({ user }) => (
  <div className="sidebar">
    <UserStats user={user} />
  </div>
);

const Body = ({ user }) => (
  <div className="body">
    <Sidebar user={user} />
    <Content user={user} />
  </div>
);

class App extends React.Component {
  state = {
    user: {
      avatar:
        "https://www.gravatar.com/avatar/5c3dd2d257ff0e14dbd2583485dbd44b",
      name: "Dave",
      followers: 1234,
      following: 123
    }
  };

  render() {
    const { user } = this.state;

    return (
      <div className="app">
        <Nav user={user} />
        <Body user={user} />
      </div>
    );
  }
}

ReactDOM.render(<App />, document.querySelector("#root")); 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个关于 CodeSandbox 的工作示例。

现在，这不是可怕的 T1。它工作得很好。但是写起来有点烦。当你不得不传递许多道具(而不是只有一个)时，这变得更加令人讨厌。

然而，这种“适当的钻探”策略有一个更大的缺点:它在组件之间创建了耦合，否则这些组件会被解耦。在上面的例子中，`Nav`需要接受一个“用户”道具并将其传递给`UserAvatar`，即使 Nav 不需要这个`user`。

紧密耦合的组件(比如将道具向下传递给子组件的组件)更难重用，因为每当您将一个组件放到一个新位置时，您都必须将它们与它们的新父组件连接起来。

让我们看看如何用 Redux 改进它。

## 使用 Redux 改善数据流

我将快速浏览一下 Redux 的例子，这样我们可以更深入地了解上下文是如何工作的，所以如果你对 Redux 不清楚，请先阅读这个[对 Redux 的介绍](https://dev.to/dceddia/how-does-redux-work-cal)(或者[观看视频](https://youtu.be/sX3KeP7v7Kg))。

这是上面的 React 应用程序，被重构为使用 Redux。`user`信息已经被转移到 Redux 存储中，这意味着我们可以使用 react-redux 的`connect`函数直接将`user`道具注入到需要它的组件中。

就解耦而言，这是一个巨大的胜利。看看`Nav`、`Body`和`Sidebar`，你会发现他们不再接受和传递`user`道具。别再用道具玩烫手山芋了。不再有不必要的耦合。

```
import React from "react";
import ReactDOM from "react-dom";

// We need createStore, connect, and Provider:
import { createStore } from "redux";
import { connect, Provider } from "react-redux";

// Create a reducer with an empty initial state
const initialState = {};
function reducer(state = initialState, action) {
  switch (action.type) {
    // Respond to the SET_USER action and update
    // the state accordingly
    case "SET_USER":
      return {
        ...state,
        user: action.user
      };
    default:
      return state;
  }
}

// Create the store with the reducer
const store = createStore(reducer);

// Dispatch an action to set the user
// (since initial state is empty)
store.dispatch({
  type: "SET_USER",
  user: {
    avatar: "https://www.gravatar.com/avatar/5c3dd2d257ff0e14dbd2583485dbd44b",
    name: "Dave",
    followers: 1234,
    following: 123
  }
});

// This mapStateToProps function extracts a single
// key from state (user) and passes it as the `user` prop
const mapStateToProps = state => ({
  user: state.user
});

// connect() UserAvatar so it receives the `user` directly,
// without having to receive it from a component above

// could also split this up into 2 variables:
// const UserAvatarAtom = ({ user, size }) => ( ... )
// const UserAvatar = connect(mapStateToProps)(UserAvatarAtom);
const UserAvatar = connect(mapStateToProps)(({ user, size }) => (
  <img
    className={`user-avatar ${size || ""}`}
    alt="user avatar"
    src={user.avatar}
  />
));

// connect() UserStats so it receives the `user` directly,
// without having to receive it from a component above
// (both use the same mapStateToProps function)
const UserStats = connect(mapStateToProps)(({ user }) => (
  <div className="user-stats">
    <div>
      <UserAvatar user={user} />
      {user.name}
    </div>
    <div className="stats">
      <div>{user.followers} Followers</div>
      <div>Following {user.following}</div>
    </div>
  </div>
));

// Nav doesn't need to know about `user` anymore
const Nav = () => (
  <div className="nav">
    <UserAvatar size="small" />
  </div>
);

const Content = () => (
  <div className="content">main content here</div>
);

// Sidebar doesn't need to know about `user` anymore
const Sidebar = () => (
  <div className="sidebar">
    <UserStats />
  </div>
);

// Body doesn't need to know about `user` anymore
const Body = () => (
  <div className="body">
    <Sidebar />
    <Content />
  </div>
);

// App doesn't hold state anymore, so it can be
// a stateless function
const App = () => (
  <div className="app">
    <Nav />
    <Body />
  </div>
);

// Wrap the whole app in Provider so that connect()
// has access to the store
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.querySelector("#root")
); 
```

Enter fullscreen mode Exit fullscreen mode

[这里是 CodeSandbox](https://codesandbox.io/s/943yr0qp3o) 上的 Redux 例子。

现在你可能想知道 Redux 是如何实现这种魔力的。好奇是好事。为什么 React 不支持向下传递多级道具，而 Redux 却能做到？

答案是，Redux 使用了 React 的*上下文*特性。不是现代的上下文 API(还不是)——旧的那个。React 文档说除非你正在写一个库或者知道你在做什么，否则不要使用它。

上下文就像运行在每个组件后面的电气总线:要接收通过它的电力(数据),你只需要插上插头。而(React-)Redux 的`connect`函数就是这么做的。

不过，Redux 的这个特性只是冰山一角。到处传递数据是 Redux 最明显的特性。以下是一些开箱即用的其他好处:

### `connect`纯粹

`connect`自动使连接的组件“纯净”，这意味着它们只会在它们的属性改变时重新渲染——也就是当它们的 Redux 状态部分改变时。这可以防止不必要的重新渲染，并保持您的应用程序快速运行。DIY 方法:创建一个扩展`PureComponent`的类，或者自己实现`shouldComponentUpdate`。

### 用 Redux 轻松调试

编写 actions 和 reducers 的仪式被它提供给你的令人敬畏的调试能力所平衡。

有了 Redux DevTools 扩展，你可以自动记录你的应用程序执行的每一个动作。您可以随时打开它，查看哪些动作被触发，它们的负载是什么，以及动作发生前后的状态。

[![Redux devtools demo](../Images/2482abb28796c063bd5de1d1c62cec7c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bp0AkVKe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://daveceddia.com/images/redux-devtools.gif)

Redux DevTools 启用的另一个伟大功能是*时间旅行调试*又名。你可以点击任何过去的动作并跳转到该时间点，基本上重放直到并包括该动作的每个动作(但没有进一步的)。这能工作的原因是因为每一个动作*不变地*更新状态，所以你能得到一个记录状态更新的列表并重放它们，没有不良影响，并在你期望的地方结束。

还有像 LogRocket 这样的工具，基本上可以为你的每个用户提供一个永远在线的 Redux dev tools T2。有错误报告吗？太好了。在 LogRocket 中查找用户的会话，您可以看到他们所做的事情的回放，以及触发了哪些操作。这一切都是通过利用 Redux 的动作流来实现的。

### 用中间件定制 Redux

Redux 支持*中间件*的概念，这是一个花哨的词，表示“每次调度动作时运行的功能”。编写自己的中间件并不像看起来那么难，它可以实现一些强大的东西。

例如…

*   想在每次动作名称以`FETCH_`开头时启动 API 请求吗？你可以用中间件做到这一点。
*   想要一个集中的地方将事件记录到您的分析软件中吗？中间件是一个很好的地方。
*   想要阻止某些动作在特定时间触发？你可以通过中间件做到这一点，对你的应用程序的其余部分是透明的。
*   想要拦截具有 JWT 令牌的操作并将它们自动保存到 localStorage 吗？是的，中间件。

这里有一篇很好的文章，有一些如何编写 Redux 中间件的[例子。](https://medium.com/@jacobp100/you-arent-using-redux-middleware-enough-94ffe991e6)

## 如何使用 React 上下文 API

但是，嘿，也许你不需要 Redux 的那些花哨功能。也许您不关心简单的调试、定制或自动性能改进——您想要做的只是简单地传递数据。也许你的应用程序很小，或者你只需要让某些东西工作起来，然后再处理那些花哨的东西。

React 的新上下文 API 可能会满足这个要求。让我们看看它是如何工作的。

我在 Egghead 上发布了一个快速上下文 API 课程，如果你更愿意看而不是读的话(3:43):

[![Context API lesson on Egghead.io](../Images/d2f3830638f089e8edf318b41d997d76.png)T2】](https://egghead.io/lessons/react-pass-props-through-multiple-levels-with-react-s-context-api)

上下文 API 有 3 个重要部分:

*   创建上下文的`React.createContext`函数
*   `Provider`(由`createContext`返回)建立贯穿组件树的“电气总线”
*   `Consumer`(也由`createContext`返回)接入“电气总线”以提取数据

`Provider`与 React-Redux 的`Provider`非常相似。它接受一个`value`道具，可以是你想要的任何东西(它甚至可以是一个 Redux 商店…但那会很傻)。它很可能是一个包含您的数据和您希望能够对数据执行的任何操作的对象。

`Consumer`的工作方式有点像 React-Redux 的`connect`函数，利用数据并使其对使用它的组件可用。

以下是亮点:

```
// Up top, we create a new context
// This is an object with 2 properties: { Provider, Consumer }
// Note that it's named with UpperCase, not camelCase
// This is important because we'll use it as a component later
// and Component Names must start with a Capital Letter
const UserContext = React.createContext();

// Components that need the data tap into the context
// by using its Consumer property. Consumer uses the
// "render props" pattern.
const UserAvatar = ({ size }) => (
  <UserContext.Consumer>
    {user => (
      <img
        className={`user-avatar ${size || ""}`}
        alt="user avatar"
        src={user.avatar}
      />
    )}
  </UserContext.Consumer>
);

// Notice that we don't need the 'user' prop any more,
// because the Consumer fetches it from context
const UserStats = () => (
  <UserContext.Consumer>
    {user => (
      <div className="user-stats">
        <div>
          <UserAvatar user={user} />
          {user.name}
        </div>
        <div className="stats">
          <div>{user.followers} Followers</div>
          <div>Following {user.following}</div>
        </div>
      </div>
    )}
  </UserContext.Consumer>
);

// ... all those other components go here ...
// ... (the ones that no longer need to know or care about `user`)

// At the bottom, inside App, we pass the context down
// through the tree using the Provider
class App extends React.Component {
  state = {
    user: {
      avatar:
        "https://www.gravatar.com/avatar/5c3dd2d257ff0e14dbd2583485dbd44b",
      name: "Dave",
      followers: 1234,
      following: 123
    }
  };

  render() {
    return (
      <div className="app">
        <UserContext.Provider value={this.state.user}>
          <Nav />
          <Body />
        </UserContext.Provider>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是代码沙箱中的[完整代码。](https://codesandbox.io/s/q9w2qrw6q4)

让我们回顾一下这是如何工作的。

记住有 3 部分:上下文本身(用`React.createContext`创建)，和两个与之对话的组件(`Provider`和`Consumer`)。

### 提供者和消费者是一对

提供者和消费者被绑定在一起。不可分割。而他们只知道如何互相*交谈*。如果您创建了两个单独的上下文，比如“上下文 1”和“上下文 2”，那么上下文 1 的提供者和消费者将无法与上下文 2 的提供者和消费者通信。

### 上下文没有保存状态

注意上下文*没有自己的状态*。它仅仅是你数据的一个管道。您必须向`Provider`传递一个值，该值将被传递给任何知道如何查找它的`Consumer`(与提供者绑定到相同上下文的消费者)。

当您创建上下文时，您可以像这样传入一个“默认值”:

```
const Ctx = React.createContext(yourDefaultValue); 
```

Enter fullscreen mode Exit fullscreen mode

这个默认值是当`Consumer`被放置在一个上面没有`Provider`的树中时，它将接收到的值。如果你没有通过一个，这个值将只是`undefined`。不过，请注意，这是一个默认的*值*，而不是一个初始的*值*。上下文不会保留任何东西；它只是分发你传入的数据。

### 消费者使用渲染道具模式

Redux 的`connect`函数是高阶分量(或简称为 HoC)。它*包装*另一个组件，并将道具传递给它。

相反，上下文`Consumer`期望子组件是一个函数。然后，它在渲染时调用该函数，传递从它上面的`Provider`中获得的值(或者上下文的默认值，或者如果没有传递默认值，则传递`undefined`)。

### 提供者接受一个值

只有一个值，作为`value`道具。但是请记住，该值可以是任何值。实际上，如果你想向下传递多个值，你可以创建一个包含所有值的对象，然后向下传递对象。

这是上下文 API 的基本内容。

## 上下文 API 灵活

因为创建上下文给了我们两个组件(提供者和消费者)，所以我们可以随意使用它们。这里有几个想法。

### 把消费者变成更高阶的成分

不喜欢在每个需要的地方添加`UserContext.Consumer`的想法？这是你的准则。你可以做你想做的。你是成年人了。

如果您更愿意将该值作为一个道具接收，您可以编写一个类似于
的小包装器

```
function withUser(Component) {
  return function ConnectedComponent(props) {
    return (
      <UserContext.Consumer>
        {user => <Component {...props} user={user}/>}
      </UserContext.Consumer>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以重写，比如说，`UserAvatar`来使用这个新的`withUser`函数:

```
const UserAvatar = withUser(({ size, user }) => (
  <img
    className={`user-avatar ${size || ""}`}
    alt="user avatar"
    src={user.avatar}
  />
)); 
```

Enter fullscreen mode Exit fullscreen mode

嘣，上下文可以像 Redux 的`connect`一样工作。减去自动纯度。

这里有一个[代码沙箱的例子，带有这个高阶组件](https://codesandbox.io/s/jpy76nm1v)。

### 保持提供者中的状态

记住，上下文的提供者只是一个管道。它不会保留任何数据。但这并不妨碍你用自己的*包装器来保存数据。*

在上面的例子中，我让`App`保存数据，所以您需要理解的唯一新东西是提供者+消费者组件。但也许你想开一家自己的“商店”。您可以创建一个组件来保存状态并通过上下文传递它们:

```
class UserStore extends React.Component {
  state = {
    user: {
      avatar:
        "https://www.gravatar.com/avatar/5c3dd2d257ff0e14dbd2583485dbd44b",
      name: "Dave",
      followers: 1234,
      following: 123
    }
  };

  render() {
    return (
      <UserContext.Provider value={this.state.user}>
        {this.props.children}
      </UserContext.Provider>
    );
  }
}

// ... skip the middle stuff ...

const App = () => (
  <div className="app">
    <Nav />
    <Body />
  </div>
);

ReactDOM.render(
  <UserStore>
    <App />
  </UserStore>,
  document.querySelector("#root")
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，你的用户数据被很好地包含在它自己的组件中，而这个组件的*唯一的*关注的是用户数据。太棒了。`App`又可以无国籍了。我也觉得看起来干净一点。

这里有一个使用这个用户存储库的[示例代码沙箱。](https://codesandbox.io/s/jpy76nm1v)

### 通过上下文向下传递动作

记住，通过`Provider`传递的对象可以包含您想要的任何内容。这意味着它可以包含函数。你甚至可以称之为“行动”

这里有一个新的例子:一个简单的房间，有一个灯光开关来切换背景颜色——呃，我指的是灯光。

[![the fire is dead. the room is freezing.](../Images/cfbf56df57a13f4b9fe4ad19f4b5f446.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kz0OYgyP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://daveceddia.com/images/lightswitch-app.gif)

状态保存在存储器中，存储器还具有开关灯的功能。状态和功能都是通过上下文传递的。

```
import React from "react";
import ReactDOM from "react-dom";
import "./styles.css";

// Plain empty context
const RoomContext = React.createContext();

// A component whose sole job is to manage
// the state of the Room
class RoomStore extends React.Component {
  state = {
    isLit: false
  };

  toggleLight = () => {
    this.setState(state => ({ isLit: !state.isLit }));
  };

  render() {
    // Pass down the state and the onToggleLight action
    return (
      <RoomContext.Provider
        value={{
          isLit: this.state.isLit,
          onToggleLight: this.toggleLight
        }}
      >
        {this.props.children}
      </RoomContext.Provider>
    );
  }
}

// Receive the state of the light, and the function to
// toggle the light, from RoomContext
const Room = () => (
  <RoomContext.Consumer>
    {({ isLit, onToggleLight }) => (
      <div className={`room ${isLit ? "lit" : "dark"}`}>
        The room is {isLit ? "lit" : "dark"}.
        <br />
        <button onClick={onToggleLight}>Flip</button>
      </div>
    )}
  </RoomContext.Consumer>
);

const App = () => (
  <div className="app">
    <Room />
  </div>
);

// Wrap the whole app in the RoomStore
// this would work just as well inside `App`
ReactDOM.render(
  <RoomStore>
    <App />
  </RoomStore>,
  document.querySelector("#root")
); 
```

Enter fullscreen mode Exit fullscreen mode

下面是 CodeSandbox 中的[完整工作示例。](https://codesandbox.io/s/jvky9o0nvw)

## 应该用上下文，还是 Redux？

既然你已经看到了两种方式，你应该使用哪一种？嗯，如果有一件事能让你的应用程序*更好*和*写起来更有趣*，那就是**掌控决策**。我知道你可能只是想要“答案”，但我很抱歉不得不告诉你，“这要看情况。”

这取决于你的应用程序有多大，或者将会有多大。有多少人会参与其中——只有你，还是一个更大的团队？您或您的团队在函数概念(Redux 所依赖的概念，比如不变性和纯函数)方面有多少经验？

弥漫在 JavaScript 生态系统中的一个大的致命谬误是竞争的概念。每个选择都是零和游戏的想法:如果你使用*库 A* ，你一定不能使用*的竞争对手库 B* 。当一个新的库出现时，在某些方面会更好，它必须取代现有的库。有一种观点认为，一切都必须是非此即彼的，你必须要么选择最好的最新产品，要么与过去的开发人员一起退居幕后。

一个更好的方法是像看待工具箱一样看待这些奇妙的选择。这就像是在使用螺丝刀和冲击起子之间的选择。对于 80%的工作来说，冲击式螺丝刀拧螺丝的速度要比螺丝刀快。但是对于另外的 20%,螺丝刀实际上是更好的选择——可能是因为空间紧张，或者物品很脆弱。当我得到一个冲击式螺丝刀时，我没有立即扔掉我的螺丝刀，甚至我的非冲击式钻头。冲击车手并没有用*取代*他们，它只是给了我另一个*选项*。解决问题的另一种方法。

上下文不会“替换”Redux，也不会“替换”Angular 或 jQuery。见鬼，当我需要快速做一些事情时，我仍然使用 jQuery。我有时仍然使用服务器渲染的 EJS 模板，而不是构建一个完整的 React 应用程序。有时候 React 比你手头的任务需要的还要多。有时候 Redux 比你需要的还要多。

今天，当 Redux 超出您的需要时，您可以触及上下文。

[Redux vs . React Context API](https://daveceddia.com/context-api-vs-redux/)最初由戴夫·塞迪亚于 2018 年 7 月 17 日在[戴夫·塞迪亚](https://daveceddia.com)发表。
# React-Redux connect():何时以及如何使用它

> 原文：<https://dev.to/bnevilleoneill/react-redux-connect-when-and-how-to-use-it-7ai>

#### 学习如何在 React 中创建连接到 Redux 状态的容器组件。

[![](../Images/45630b67e501d2923fc6c4bac7203f90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QfcvYkpG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANUNUmFiNcqYH3hjNjgB70g.jpeg)

React 提供了两个主要的向组件提供数据的机制，即: **props** 和 **state** 。虽然*属性*是只读的并且允许父组件将属性传递给子组件，但是*状态*是本地的并且封装在组件中，并且可以在组件生命周期中的任何时候改变。

由于*状态*对于构建强大的动态 React 应用程序来说是一个非常强大的机制，因此在应用程序中正确管理状态变得非常必要。已经有几个库为管理应用程序状态提供了一个结构良好的架构，比如**[**Redux**](https://redux.js.org/)[**MobX**](https://mobx.js.org/)。**

 **Redux 是一个可预测的状态容器，适用于从普通应用到框架应用的 JavaScript 应用。它占用空间非常小，但允许您编写可在任何环境下运行的一致应用程序:

[![](../Images/299a4d86cf731b12616d927bb484afb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3lx7KfET--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1008/0%2ArkzzibjBKN8nV_EY.png) 

<figcaption>简单雷杜商店</figcaption>

*本指南基于 React 应用程序中的状态管理，使用*[***React-Redux***](https://github.com/reduxjs/react-redux)*。它不是 React 或 Redux 的介绍。*

它假设您已经对 React 和 Redux 架构和 API 有了基本的了解。如果不是这样，那么你可以查看 React 文档 [*这里*](https://reactjs.org/docs/) *和 Redux 文档* [*这里*](https://redux.js.org/) *。*

### React-Redux

**react-redux** 包为 redux 状态容器提供了 react 绑定，使得 React 应用程序可以非常容易地连接到 Redux 存储。这允许您根据 React 应用程序组件与 Redux 存储的连接来分离它们，如下所示:

1.  呈现组件 —这些组件只关心事物的外观，不知道冗余状态。它们从 props 获取数据，并可能触发通过 props 传递给它们的回调。
2.  容器组件(Container Components)—这些组件负责事物如何工作，并完全了解 Redux 状态。它们通常是使用 React Redux 创建的，可能会调度 Redux 操作。它们还订阅 Redux 状态的更改。

您可以从这篇文章中了解更多关于关注点分离的信息。在本指南中，我们主要关注使用 **react-redux** 连接到 Redux 状态的容器组件。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

**react-redux** 包公开了一个非常简单的接口，您应该感兴趣的只是以下内容:

1.  **—包装 React 应用程序，并使 Redux 状态对应用程序层次结构中的所有容器组件可用**
***   **connect([mapStateToProps]，[mapDispatchToProps]，[mergeProps]，[options])** —创建一个高阶组件，用于从基本 React 组件创建容器组件**

 **你可以在你的项目中安装 **react-redux** 如下:

```
npm install react-redux --save 
```

假设您已经为 React 应用程序设置了一个 Redux store，下面是如何将应用程序连接到 Redux store 的方法:

```
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import createStore from './createReduxStore';

const store = createStore();
const rootElement = document.getElementById('root');

ReactDOM.render((
  <Provider store={store}>
    <AppRootComponent />
  </Provider>
), rootElement); 
```

有了这个设置，您现在可以使用 **connect()** API 在**appro component**的层次结构中创建连接到 Redux 存储的容器组件。

### 何时使用 connect()

#### 1。创建容器组件。

如前所述，**react-redux****connect()**API 用于创建连接到 Redux 存储的容器元素。要连接的 Redux 存储是使用 React 上下文机制从组件的最高祖先派生的。如果你只是创建一个表示性的组件，你不需要 **connect()** 。

无论您是想从 Redux store 获取数据，还是想在 Redux store 上调度操作，或者想在 React 组件中实现这两者，您都可以将该组件封装在一个由**React-Redux****connect()**:
返回的更高级组件中，使其成为一个容器组件

```
import React from 'react';
import { connect } from 'react-redux';
import Profile from './components/Profile';

function ProfileContainer(props) {
  return (
    props.loggedIn
      ? <Profile profile={props.profile} />
      : <div>Please login to view profile.</div>
  )
}

const mapStateToProps = function(state) {
  return {
    profile: state.user.profile,
    loggedIn: state.auth.loggedIn
  }
}

export default connect(mapStateToProps)(ProfileContainer); 
```

#### 2。避免手动订阅 Redux 商店。

您可以自己创建一个容器组件，并使用 **store.subscribe()** 手动订阅 Redux 存储。然而，使用**react-redux****connect()**带来了一些性能改进和优化，这些您可能无法在您的应用程序中实现。

在下面的代码片段中，我们尝试手动创建一个容器组件，并通过订阅存储将其连接到 Redux 存储，以实现与前面的代码片段类似的功能:

```
import React, { Component } from 'react';
import store from './reduxStore';
import Profile from './components/Profile';

class ProfileContainer extends Component {

  state = this.getCurrentStateFromStore()

  getCurrentStateFromStore() {
    return {
      profile: store.getState().user.profile,
      loggedIn: store.getState().auth.loggedIn
    }
  }

  updateStateFromStore = () => {
    const currentState = this.getCurrentStateFromStore();

    if (this.state !== currentState) {
      this.setState(currentState);
    }
  }

  componentDidMount() {
    this.unsubscribeStore = store.subscribe(this.updateStateFromStore);
  }

  componentWillUnmount() {
    this.unsubscribeStore();
  }

  render() {
    const { loggedIn, profile } = this.state;

    return (
      loggedIn
        ? <Profile profile={profile} />
        : <div>Please login to view profile.</div>
    )
  }

}

export default ProfileContainer; 
```

**react-redux****connect()**还提供了额外的灵活性，允许您根据最初传递给容器组件的属性来配置容器组件以接收动态属性。这对于基于道具选择 Redux 状态的一部分，或者将动作创建者绑定到道具中的特定变量非常有用。

如果您的 React 应用程序使用多个 Redux 存储，**React-Redux****connect()**允许您轻松地指定容器组件应该连接到哪个存储。

### 解剖连接()

**react-redux** 提供的 **connect()** 函数最多可以带四个参数，都是*可选的*。调用 **connect()** 函数返回一个*高阶组件*，它可以用来包装任何 React 组件。

由于一个更高阶的组件是由 **connect()** 返回的，所以必须用基本的 React 组件再次调用它，以便将它转换成一个容器组件:

```
const ContainerComponent = connect()(BaseComponent); 
```

下面是 **connect()** 函数的签名:

```
connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options]) 
```

#### mapStateToProps(state, [ownProps]) = > stateProps

这个参数是一个**函数**，它返回一个**普通对象**或**另一个函数**。传递该参数会使容器组件订阅 Redux 存储更新，这意味着每次存储更新时都会调用 **mapStateToProps** 函数。如果您对商店更新不感兴趣，请将其保留为**未定义**或 **null** 。

**mapStateToProps** 用**两个参数**声明，第二个是*可选*。第一个参数是 Redux 存储的当前状态。第二个参数，如果传递，是传递给组件的 props 的一个对象:

```
const mapStateToProps = function(state) {
  return {
    profile: state.user.profile,
    loggedIn: state.auth.loggedIn
  }
}

export default connect(mapStateToProps)(ProfileComponent); 
```

如果从 **mapStateToProps** 返回一个普通对象，则返回的 **stateProps** 对象被合并到组件的 Props 中。您可以在组件中访问这些道具，如下:

```
function ProfileComponent(props) {
  return (
    props.loggedIn
      ? <Profile profile={props.profile} />
      : <div>Please login to view profile.</div>
  )
} 
```

但是，如果返回一个函数，该函数将被用作组件的每个实例的 **mapStateToProps** 。这对于提高渲染性能和记忆非常有用。

#### mapDispatchToProps(dispatch, [ownProps]) = > dispatchProps

这个参数可以是一个**对象**或一个**函数**，它返回一个**普通对象**或**另一个函数**。为了更好地说明 **mapDispatchToProps** 如何工作，您将需要一些动作创建器。

例如，假设您有以下动作创建者:

```
export const writeComment = (comment) => ({
  comment,
  type: 'WRITE_COMMENT'
});

export const updateComment = (id, comment) => ({
  id,
  comment,
  type: 'UPDATE_COMMENT'
});

export const deleteComment = (id) => ({
  id,
  type: 'DELETE_COMMENT'
}); 
```

**1。默认实现**

如果您没有提供自己的 **mapDispatchToProps** 对象或函数，将使用默认实现，它只是将商店的 **dispatch** 方法作为道具注入到组件中。

您可以使用组件中的**分派**道具，如下所示:

```
import React from 'react';
import { connect } from 'react-redux';
import { updateComment, deleteComment } from './actions';

function Comment(props) {
  const { id, content } = props.comment;

  // Invoking the actions via props.dispatch()
  const editComment = () => props.dispatch(updateComment(id, content));
  const removeComment = () => props.dispatch(deleteComment(id));

  return (
    <div>
      <p>{ content }</p>
      <button type="button" onClick={editComment}>Edit Comment</button>
      <button type="button" onClick={removeComment}>Remove Comment</button>
    </div>
  )
}

export default connect()(Comment); 
```

**2。传递一个物体**

如果为此参数传递了一个**对象**，则该对象中的每个函数都将被视为 Redux 动作创建者，并将被打包成对商店的 **dispatch** 方法的调用，以便可以直接调用它。由此产生的 **dispatchProps** 对象的动作创建者将被合并到组件的道具中。

下面的代码片段展示了如何通过提供动作创建者的对象来定义 **mapDispatchToProps** ，以及如何将动作创建者用作 React 组件的道具:

```
import React from 'react';
import { connect } from 'react-redux';
import { updateComment, deleteComment } from './actions';

function Comment(props) {
  const { id, content } = props.comment;

  // Invoking the actions directly as component props
  const editComment = () => props.updatePostComment(id, content);
  const removeComment = () => props.deletePostComment(id);

  return (
    <div>
      <p>{ content }</p>
      <button type="button" onClick={editComment}>Edit Comment</button>
      <button type="button" onClick={removeComment}>Remove Comment</button>
    </div>
  )
}

// Object of action creators
const mapDispatchToProps = {
  updatePostComment: updateComment,
  deletePostComment: deleteComment
}

export default connect(null, mapDispatchToProps)(Comment); 
```

**3。传递函数**

如果传递了一个**函数**，那么就由您返回一个 **dispatchProps** 的对象，该对象使用商店的 **dispatch** 方法绑定动作创建者。该函数将商店的**派单**作为其第一个参数。与 **mapStateToProps** 一样，它也可以接受一个*可选的* **ownProps** 第二个参数，该参数映射到传递给组件的原始属性。

如果此函数返回另一个函数，则返回的函数将被用作 **mapDispatchToProps** ，这对于提高渲染性能和记忆非常有用。

Redux 提供的 **bindActionCreators()** 助手可以在这个函数中使用，将动作创建者绑定到商店的 **dispatch** 方法。

下面的代码片段展示了如何通过提供一个函数来定义 **mapDispatchToProps** ，以及如何使用 **bindActionCreators()** 助手将评论动作创建者绑定到 React 组件的 **props.actions** :

```
import React from 'react';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
import * as commentActions from './actions';

function Comment(props) {
  const { id, content } = props.comment;
  const { updateComment, deleteComment } = props.actions;

  // Invoking the actions from props.actions
  const editComment = () => updateComment(id, content);
  const removeComment = () => deleteComment(id);

  return (
    <div>
      <p>{ content }</p>
      <button type="button" onClick={editComment}>Edit Comment</button>
      <button type="button" onClick={removeComment}>Remove Comment</button>
    </div>
  )
}

const mapDispatchToProps = (dispatch) => {
  return {
    actions: bindActionCreators(commentActions, dispatch)
  }
}

export default connect(null, mapDispatchToProps)(Comment); 
```

#### mergeProps(stateProps, dispatchProps, ownProps) = > props

这个参数如果通过，就是一个**函数**，它带有**三个参数**——即:

*   **`stateProps`** —调用返回的道具对象 **`mapStateToProps()`**

*   **`dispatchProps`** —动作创作者道具对象来自 **`mapDispatchToProps()`**

*   **`ownProps`** —组件收到的原始道具。

这个函数返回一个普通的 props 对象，它将被传递给包装的组件。这对于根据道具有条件地映射 Redux 存储的部分状态或动作创建者非常有用。

当没有提供该函数时，默认实现如下:

```
const mergeProps = (stateProps, dispatchProps, ownProps) => {
  return Object.assign({}, ownProps, stateProps, dispatchProps)
} 
```

#### 选项

如果指定的话，options 对象包含用于修改 **connect()** 行为的选项。 **connect()** 是 **connectAdvanced()** 的一个特殊实现，它接受了 **connectAdvanced()** 可用的大部分选项以及一些附加选项。

您可以参考[这个文档](https://github.com/reduxjs/react-redux/blob/master/docs/api.md)来查看 **connect()** 可用的所有选项，以及它们如何修改它的行为。

### 如何使用 connect()

#### 设置商店

在使用 **connect()** 将常规 React 组件转换为容器组件之前，必须指定组件将连接到的 Redux 存储。

假设您有一个名为 **NewComment** 的容器组件，用于向帖子添加新评论，并显示一个提交评论的按钮。该组件可能看起来像下面的代码片段:

```
import React from 'react';
import { connect } from 'react-redux';

class NewComment extends React.Component {

  input = null

  writeComment = evt => {
    evt.preventDefault();
    const comment = this.input.value;

    comment && this.props.dispatch({ type: 'WRITE_COMMENT', comment });
  }

  render() {
    const { id, content } = this.props.comment;

    return (
      <div>
        <input type="text" ref={e => this.input = e} placeholder="Write a comment" />
        <button type="button" onClick={this.writeComment}>Submit Comment</button>
      </div>
    )
  }

}

export default connect()(NewComment); 
```

为了在您的应用程序中实际使用这个组件，您必须指定该组件必须连接到的 Redux 存储，否则，您将得到一个错误。

这可以通过两种方式实现:

**1。在容器组件**上设置商店道具

第一种方法是通过传递对 Redux store 的引用来指定组件上的 Redux store，作为组件
的 **store** prop 的值

```
import React from 'react';
import store from './reduxStore';
import NewComment from './components/NewComment';

function CommentsApp(props) {
  return <NewComment store={store} />
} 
```

**2。在`<Provider>`组件**上设置商店道具

如果您希望为您的应用程序设置一次 Redux 存储，那么这是一个好办法。对于只使用一个 Redux 商店的应用程序来说，通常就是这种情况。

**react-redux** 提供了一个 **`<Provider>`** 组件，可以用来包装根应用组件。它接受一个 **store** prop，该 prop 需要一个对您希望应用程序使用的 Redux store 的引用。使用 React 的上下文机制将**存储**传递给应用层次结构中的容器组件:

```
import React from 'react';
import ReactDOM from 'react-dom';
import store from './reduxStore';
import { Provider } from 'react-redux';
import NewComment from './components/NewComment';

function CommentsApp(props) {
  return <NewComment />
}

ReactDOM.render((
  <Provider store={store}>
    <CommentsApp />
  </Provider>
), document.getElementById('root')) 
```

#### 访问自己的道具

如前所述，传递给 **connect()** 的 **mapStateToProps** 和**mapdispatctoprops**函数可以用组件的 **ownProps** 作为第二个参数来声明。

然而，有一个**警告**。如果声明函数的强制参数个数小于 **2** ，那么 **ownProps** 永远不会被通过。但是如果函数声明时没有强制参数或者至少有 **2 个**参数，那么 **ownProps** 被传递。

以下是几个场景:

**1。不带参数**声明

```
const mapStateToProps = function() {
  console.log(arguments[0]); // state
  console.log(arguments[1]); // ownProps
}; 
```

这里， **ownProps** 被传递，因为该函数是在没有强制参数的情况下声明的。因此，使用新的 ES6 rest 参数语法，以下内容也将以类似的方式工作:

```
const mapStateToProps = function(...args) {
  console.log(args[0]); // state
  console.log(args[1]); // ownProps
}; 
```

**2。用一个参数**声明

```
const mapStateToProps = function(state) {
  console.log(state); // state
  console.log(arguments[1]); // undefined
}; 
```

这里只有一个参数，**状态**。因此，**自变量【1】**是**未定义的**，因为 **ownProps** 未通过。

**3。用默认参数**声明

```
const mapStateToProps = function(state, ownProps = {}) {
  console.log(state); // state
  console.log(ownProps); // {}
}; 
```

这里只有一个强制参数， **state** ，因为第二个 **ownProps** 参数是*可选的*，因为已经为它指定了一个默认值。因此，由于只有一个强制参数， **ownProps** 没有被传递，因此它映射到分配给它的默认值- `{}`。

**4。用两个参数**声明

```
const mapStateToProps = function(state, ownProps) {
  console.log(state); // state
  console.log(ownProps); // ownProps
}; 
```

这很简单。 **ownProps** 在这里被传递，因为该函数是用两个强制参数声明的。

### 结论

在本指南中，您已经看到了何时以及如何使用由 **react-redux** 包提供的 **connect()** API 来创建连接到 redux 状态的容器组件。

虽然本指南涵盖了对 **connect()** API 及其用法的大部分剖析，但并没有广泛展示用例示例。你可以在[这份文档](https://github.com/reduxjs/react-redux/blob/master/docs/api.md)中找到更多。

#### 拍拍&跟着

如果你觉得这篇文章很有见地，如果你不介意的话，请随意鼓掌。

你也可以在 Medium ( [Glad Chinda](https://medium.com/u/ddcd0e9719e5) )上关注我，获取更多对你有帮助的有见地的文章。你也可以在推特上关注我( [@gladchinda](https://twitter.com/@gladchinda) )。

***享受编码……***

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](../Images/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子 [React Redux Connect 教程——何时以及如何使用](https://blog.logrocket.com/react-redux-connect-when-and-how-to-use-it-f2a1edab2013/)最先出现在[日志博客](https://blog.logrocket.com)上。****